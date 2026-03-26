Либа позволяет читать из .yml конфигов
```
go get github.com/spf13/viper
```

Прогрев либы
```go
func initConfig() error {  
    viper.AddConfigPath("configs")  
    viper.SetConfigName("config")  
    return viper.ReadInConfig()  
}

if err := initConfig(); err != nil {  
    logrus.Fatalf("error initializing configs: %s", err.Error())  
}
```

Использование
```go
db, err := repository.NewPostgresDB(repository.Config{  
    Host:     viper.GetString("db.host"),  
    Port:     viper.GetString("db.port"),  
    Username: viper.GetString("db.username"),  
    DBName:   viper.GetString("db.dbname"),  
    SSLMode:  viper.GetString("db.sslmode"),
})
```

