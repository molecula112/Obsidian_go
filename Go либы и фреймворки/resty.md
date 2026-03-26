Прикольная либа которая еще проще позволяет рулить клиентом.
```go
client := resty.New()

resp, err := client.R().
    SetHeader("Content-Type", "application/json").
    SetBody(map[string]string{
        "name": "John",
    }).
    Post("https://api.example.com/users")
```