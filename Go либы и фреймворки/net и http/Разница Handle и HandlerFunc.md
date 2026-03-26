Золотое правило:
```
если используешь middleware → Handle  
если просто функцию → HandleFunc
```

Если коротко то:
```
HandlerFunc — это ТИП  
Handle — это МЕТОД регистрации маршрута
```

**http.HandlerFunc** - это тип функции который умеет превращаться в Handler
```go
type HandlerFunc func(ResponseWriter, *Request)
```

И у него есть метод:
```go
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
	f(w, r)
}
```
Поэтому:
```
function → HandlerFunc → Handler interface
```
Пример:
```go
h := http.HandlerFunc(helloHandler) --> теперь h - это http.Handler 
```



**mux.Handle()** - это метод роутера, который регистрирует handler для маршрута.
Сигнатура:
```go
func (mux *ServeMux) Handle(pattern string, handler Handler)
```
Пример:
```go
mux.Handle("/hello", handler)
```



**mux.HandleFunc()** - это просто удобная обертка.
Сигнатура:
```go
func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request))
```
А внутри он делает примерно это:
```go
mux.Handle(pattern, http.HandlerFunc(handler))
```
То есть HandleFunc = Handle + HandlerFunc

> Суть такая, если хочешь функцию превратить в handler то юзай handleFunc, если юзаешь именно Handler то его надо Handle(пример 2)

2 примера чтобы понять:
1) Обычный handleFunc
```go
import (  
"fmt"  
"net/http"  
)

func hello(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "hello")
}

func main() {
	mux := http.ServeMux()
	mux.HandleFunc("/hello", hello)
	http.ListenAndServe(":8080", mux)
}
```

2) Тот же пример но с Handle
```go
type HelloHandler struct{}

func (h HelloHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "hello from struct")
}

func main() {
	mux := http.ServeMux()
	mux.Handle("/hello", HelloHandler{})
	http.ListenAndServe()
}
```