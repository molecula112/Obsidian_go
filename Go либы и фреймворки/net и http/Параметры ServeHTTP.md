Метод ServeHTTP - это главный метод обработчика HTTP запросов в GO. Он вызывается сервером каждый раз, когда приходит HTTP запрос:
```go
ServeHTTP(w http.ResponseWriter, r *http.Request)
```

```go
r *http.Request
```
Это весь HTTP запрос.
Внутри:
```
method
url
headers
body
cookies
```
Например: 
```go
r.Method
r.URL.Path
r.Header
r.Host
```


```go
w http.ResponseWriter
```
Это объект для ответа клиенту, в него мы пишем ответ:
```go
fmt.Fprintln(w, "hello")
```

Это тоже самое что:
```
HTTP/1.1 200 OK
Content-Type: text/plain

hello
```