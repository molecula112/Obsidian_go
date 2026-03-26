`middleware нужен чтобы не дублировать код и не ломать архитектуру`, также `middleware это перехватчик запроса`

Представь у тебя 10 роутов:
```
/users  
/orders  
/products  
/payments
```
И ты хочешь логировать каждый запрос, тебе придется в каждом handler писать так:
```go
func getUsers(w http.ResponseWriter, r *http.Request) {

	fmt.Println("request:", r.Method, r.URL.Path)

	fmt.Fprintln(w, "users")
}

func getOrders(w http.ResponseWriter, r *http.Request) {

	fmt.Println("request:", r.Method, r.URL.Path)

	fmt.Fprintln(w, "orders")
}
```
Теперь у нас копипаста логики, если логирование поменяется то придется менять везде, а с middleware логика вынесена отдельно:
```go
func LoggingMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {

		fmt.Println("request:", r.Method, r.URL.Path)

		next.ServeHTTP(w, r)
	})
}
```
И далее просто подключаем и кайфуем:
```go
mux.Handle("/users", LoggingMiddleware(http.HandlerFunc(getUsers)))
```

Middleware — это **перехватчик запроса**, он может:
```go
сделать что-то ДО handler  
вызвать handler  
сделать что-то ПОСЛЕ handl
```

Важно!
```
middleware вызывает next.ServeHTTP(w, r),  
потому что next — это http.Handler
```