```
go get github.com/rs/zerolog
```

Крутая и быстрая либа для вывода логов.
- не использует reflection
- минимум аллокаций
- пишет JSON напрямую
Поэтому его любят в микросервисах.

Пример:
```go
package main  
  
import (  
	"github.com/rs/zerolog/log"  
)  
  
func main() {  
	log.Info().Msg("service started")  
}
```
Вывод:
```json
{"level":"info","time":"2026-03-08T18:30:12+01:00","message":"service started"}
```

Но главная фишка - structured logging
```go
log.Info().  
Str("service", "account-service").  
Int("port", 9000).  
Msg("starting http server")
```
Вывод:
```json
{  
	"level":"info",  
	"service":"account-service",  
	"port":9000,  
	"message":"starting http server"  
}
```

Это позволяет системам логирования легко искать логи.
Работает в комбе с [[spew]]
Связано [[zerologs Events]]