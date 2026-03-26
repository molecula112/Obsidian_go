```go
package main  
  
import (  
    "account/internal/config"  
    "account/internal/logger"    "account/internal/repository"    "fmt"    "log"    "net"  
    "github.com/gin-gonic/gin"    swaggerFiles "github.com/swaggo/files"  
    ginSwagger "github.com/swaggo/gin-swagger"  
    "google.golang.org/grpc"    "google.golang.org/grpc/health"    "google.golang.org/grpc/health/grpc_health_v1"    "google.golang.org/grpc/reflection"    "gorm.io/driver/postgres"    "gorm.io/gorm")  
  

func main() {  
    cfg, err := config.Load()  
    if err != nil {  
       log.Fatalf("Failed to load config: %v", err)  
    }   
  
    listenAddr := fmt.Sprintf("%s:%d", cfg.Host, cfg.Port)  
    lis, err := net.Listen("tcp", listenAddr)  
    if err != nil {  
       logger.Error().Msgf("Failed to listen on %s: %v", listenAddr, err)  
       return  
    }  
  
    grpcServer := grpc.NewServer()  
  
    healthSrv := health.NewServer()  
    grpc_health_v1.RegisterHealthServer(grpcServer, healthSrv)  
    reflection.Register(grpcServer)  
  
    logger.Info().Msgf("gRPC server listening on %s", listenAddr)  
    err = grpcServer.Serve(lis)  
    if err != nil {  
       logger.Error().Msgf("Failed to serve gRPC: %v", err)  
       return  
    }  
  
    logger.Info().Msgf("Service starting up")  
  
}
```

Что тут прикольного:
```go
lis, err := net.Listen("tcp", listenAddr)
```
Вот эта строчка создает TCP сокет, который слушает порт, внутри происходит следующее:
```
OS открывает порт  
↓  
создаёт TCP listener  
↓  
ждёт входящие соединения
```
Далее мы этот Listener передаем  в наш ` err = grpcServer.Serve(lis)`

---
```go
healthSrv := health.NewServer()  
grpc_health_v1.RegisterHealthServer(grpcServer, healthSrv)  
```
Вот так регистрируется стандартный `health-check` сервис для `gRPC`
Эта штука позволяет проверить другим сервисам жив ли наш сервис или готов ли, то есть любой `gRPC` клиент может вызвать `grpc.health.v1.Health/Check` и получить `SERVING` или `NOT_SERVING`