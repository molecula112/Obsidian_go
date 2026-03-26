Подтянуть можно так
```
go get -u github.com/jmoiron/sqlx
```

Настройка драйвера бд
```go
import (  
    "fmt"  
  
    "github.com/jmoiron/sqlx")  
  
type Config struct {  
    Host     string  
    Port     string  
    Username string  
    Password string  
    DBName   string  
    SSLMode  string  
}  
  
func NewPostgresDB(cfg Config) (*sqlx.DB, error) {  
    db, err := sqlx.Open("postgres", fmt.Sprintf("host=%s port=%s user=%s password=%s dbname=%s sslmode=%s",  
       cfg.Host, cfg.Port, cfg.Username, cfg.Password, cfg.DBName, cfg.SSLMode))  
    if err != nil {  
       return nil, err  
    }  
  
    err = db.Ping()  
    if err != nil {  
       return nil, err  
    }  
  
    return db, nil  
  
}
```

Инициализация в main
```go
db, err := repository.NewPostgresDB(repository.Config{  
    Host:     viper.GetString("db.host"),  
    Port:     viper.GetString("db.port"),  
    Username: viper.GetString("db.username"),  
    Password: viper.GetString("db.password"),  
    DBName:   viper.GetString("db.dbname"),  
    SSLMode:  viper.GetString("db.sslmode"),  
})  
if err != nil {  
    log.Fatalf("failed to initialized db: %s", err.Error())  
}
```
