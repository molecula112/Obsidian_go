ServeMux хранит в себе таблицу маршрутов, примерно так:
```go
routes map[string]Handler
```

Когда ты пишешь:
```go
mux.HandleFunc("/hello", helloHandler)
```
Внутри происходит что то типо:
```go
routes["/hello"] = helloHandler
```

Потом при запросе:
```go
r.URL.Path == "/hello"
```
mux сделает:
```go
handler := routes[path]  
handler.ServeHTTP()
```

Связано: [[HandlerFunc]]

Обрати внимание что тут нет конкретного HTTP метода, на самом деле идея net/http 
```
routing по path
```
Поэтому и появились фреймворки, потому что базовый http/net также не умеет работать с path parameters