Главная идея: `HandlerFunc` — это **адаптер**, который превращает **обычную функцию** в Handler. или `HandleFunc` существует, чтобы можно было использовать обычные функции как HTTP обработчики.

---
Разберем подробнее:
HTTP сервер на GO работает не с функциями, а с интерфейсом Handler.
Интерфейс выглядит так:
```go
type Handler interface {
    ServeHTTP(ResponseWriter, *Request)
}
```
То есть сервер понимает любой объект, у которого есть метод:
```go
ServeHTTP(ResponseWriter, *Request)
```

Если мы сделаем кастомный хэндлер:
```go
type MyHandler struct{}  
  
func (h MyHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {  
	fmt.Fprintln(w, "Hello")  
}
```
То его можно передать серверу, потому что MyHandler реализует интерфейс Handler:
```go
http.ListenAndServe(":8080", MyHandler{})
```

Но писать структуры не очень удобно каждый раз на каждый роут, пришлось бы делать что то типо такого:
```go
type HelloHandler struct{}  
  
func (h HelloHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {  
	fmt.Fprintln(w, "Hello")  
}

type TimeHandler struct{}  
  
func (h TimeHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {  
	fmt.Fprintln(w, "Time")  
}
```
Поэтому GO позволяет писать просто функцию:
```go
func hello(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln
}
```

Но есть нюанс, это функция а не Handler, поэтому в GO существует один хитрый трюк и он называется HandlerFunc. Есть тип:
```go
type HandlerFunc func(ResponseWriter, *Request)
```
Это тип функции. У него есть метод:
```go
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
	f(w, r)
}
```
То есть:
1) HandlerFunc - это функция
2) У нее есть метод ServeHTTP
3) Значит она реализует интерфейс Handler

Что происходит внутри:
Когда ты пишешь:
```go
http.HandleFunc("/hello", hello)
```
Сигнатура следующая:
```go
func (mux *ServeMux) HandleFunc(pattern string, handler func(ResponseWriter, *Request))
```
То есть как можно заметить принимает обычную функцию, но внутри происходит преобразование:
```go
mux.Handle(pattern, HandlerFunc(handler))
```
То есть:
```
function → HandlerFunc → Handler
```

То есть логика такая - если есть ServeHTTP метод, значит это реализация Handler interface

Связано: [[Разница Handle и HandlerFunc]]