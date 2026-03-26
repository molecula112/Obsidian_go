С ним бро ты уже знаком поэтому вот как засетапить:
```
go install github.com/swaggo/swag/cmd/swag@latest
go get -u github.com/swaggo/http-swagger
go get -u github.com/swaggo/gin-swagger
go get -u github.com/swaggo/files
```

`swag` - это CLI утилка для генерации документации, а остальное нужно чтобы отдавать документации через HTTP сервер в Swagger UI

Теперь чтобы сделать документацю нужно:
```shell
export PATH=$PATH:$(go env GOPATH)/bin

swag init -g cmd/main.go
```

Описываются спеки через теги в комменте:
```go
// @title Account Service  
// @version 1.0  
// @description Account Service  
// @host localhost:8080  
// @BasePath /  
func main() {
}
```

Далее после описания тегов, вызываешь снова:
```shell
swag init -g cmd/main.go
```

И рестартаешь сервис.