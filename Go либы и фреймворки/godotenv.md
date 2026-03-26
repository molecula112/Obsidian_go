Либа позволяет читать с env файлов
```
go get -u github.com/joho/godotenv
```

Способ читать из .env
Сначала Load
```go
if err := godotenv.Load(); err != nil {  
    log.Fatalf("error loading env variables: %s", err.Error())  
}
```

Потом 
```go
Password: os.Getenv("DB_PASSWORD")
```

Связно: [[env]]