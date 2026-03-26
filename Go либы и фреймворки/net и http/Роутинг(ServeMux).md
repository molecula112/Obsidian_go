Можно сделать быстро и костыльно:
```go
func (h MyHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {  
    
    switch r.URL.Path {  
      
    case "/hello":  
       fmt.Fprintf(w, "hello")  
      
    case "/time":  
       fmt.Fprintf(w, "time")  
      
    default:  
       http.NotFound(w, r)  
    }  
}
```
Это работает но есть проблема, много роутов = огромным свич, поэтому стандартная библиотека даёт **router**.

ServeMux — это **HTTP router** из пакета net/http, он и делает связь:
```
URL path → handler
```
И при этом сам реализует Handler, у него есть метод
```go
func (mux *ServeMux) ServeHTTP(w ResponseWriter, r *Request)
```
Поэтому его запросто можно передать в:
```go
http.ListenAndServe(":8080", mux)
```


Получается уже красиво и прикольно, это аналог app в fastAPI
```go
import (  
    "fmt"  
    "net/http"
) 
  
func helloHandler(w http.ResponseWriter, r *http.Request) {  
    fmt.Fprintf(w, "hello")  
}  
  
func timeHandler(w http.ResponseWriter, r *http.Request) {  
    fmt.Fprintf(w, "time")  
}  
  
func main() {  
    mux := http.NewServeMux()  
    mux.HandleFunc("/hello", helloHandler)  
    mux.HandleFunc("/time", timeHandler)  
  
    http.ListenAndServe(":8080", mux)  
}
```

Теперь архитектура такая:
```
HTTP Server
     ↓
ServeMux (router)
     ↓
Handler
     ↓
твой код
```

А при запросе происходит следующее:
```
http.Server
      ↓
mux.ServeHTTP()
      ↓
match "/hello"
      ↓
helloHandler()
```

Связано: [[Внутри ServeMux]]