Первый способ - префикс роут. 
Регистрируем префикс:
```go
mux.HandleFunc("/users/", userHandler.GetUserByID)
```
Обрати внимание на / в конце, теперь этот handler будет ловить:
```go
/users/1
/users/2
/users/abc
```

Теперь вычленяем id из URL, полный урл лежит тут:
```go
r.URL.Path
```
И отрезаем /users/
```go
import "strings"

func (h *UserHandler) GetUserByID(w http.ResponseWriter, r *http.Request) {
	path := r.URL.Path
	id := strings.TrimPrefix(path, "/users/")
	fmt.Println("user id:", id)
	fmt.Fprintf(w, "user id: %s", id)
}

```
