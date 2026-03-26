Либо общий роут:
```go
func (h *UserHandler) Users(w http.ResponseWriter, r *http.Request) {  
	switch r.Method {  
	  
	case http.MethodGet:  
		h.GetUsers(w, r)  
	  
	case http.MethodPost:  
		h.CreateUser(w, r)  
	  
	default:  
		http.Error(w, "method not allowed", http.StatusMethodNotAllowed)  
	}  
}
```

И регают в main так:
```go
mux.HandleFunc("/users", userHandler.Users)
```

Либо вот так, через Helper и константы:
```go
func MethodHandler(get, post http.HandlerFunc) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {

        switch r.Method {

        case http.MethodGet:
            get(w, r)

        case http.MethodPost:
            post(w, r)

        default:
            http.Error(w, "method not allowed", http.StatusMethodNotAllowed)
        }
    }
}
```

И регают также общим в main так:
```go
mux.HandleFunc("/users", MethodHandler(userHandler.GetUsers, userHandler.CreateUser),  
)
```

