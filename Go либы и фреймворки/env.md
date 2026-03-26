Еще одна прикольная либа для работы с енвами, подтянуть можно:
`go get github.com/caarlos0/env/v10`

Ее суть в следующем, что с помощью нее можно преобразовать `.env` в структуру. 
`Работает в связке с godotenv.`

Без нее:
```go
package config

import (
	"os"
	"strconv"
)

type Config struct {
	ServiceName string
	AppEnv      string
	Host        string
	Port        int
	LogLevel    string
}

func Load() (*Config, error) {
	port, err := strconv.Atoi(os.Getenv("HTTP_PORT"))
	if err != nil {
		return nil, err
	}

	cfg := &Config{
		ServiceName: os.Getenv("SERVICE_NAME"),
		AppEnv:      os.Getenv("APP_ENV"),
		Host:        os.Getenv("HTTP_HOST"),
		Port:        port,
		LogLevel:    os.Getenv("LOG_LEVEL"),
	}

	return cfg, nil
}
```
Не удобно, то есть структура есть но мы сами создаем ее объект, а дальше через os.Getenv() для каждого поля достаем.

---
Теперь с ней:
```go
package config  
  
import (  
    "log"  
  
    "github.com/caarlos0/env/v10"    
    "github.com/joho/godotenv"
)  
  
type Config struct {  
    ServiceName string `env:"SERVICE_NAME" required:"true" default:"account-service"`  
    AppEnv      string `env:"APP_ENV" required:"true" default:"development"`  
    Host        string `env:"HTTP_HOST" required:"true" default:"localhost"`  
    Port        int    `env:"HTTP_PORT" required:"true" default:"9000"`  
    LogLevel    string `env:"LOG_LEVEL" required:"true" default:"info"`  
}  
  
func Load() (*Config, error) {  
    err := godotenv.Load()  
    if err != nil {  
       log.Println("Warning: .env не найден")  
    }  
  
    cfg := &Config{}  
    err = env.Parse(cfg)  
    if err != nil {  
       return nil, err  
    }  
    return cfg, nil  
}
```
Тут ключевая строчка `err = env.Parse(cfg)`, сам `godotenv` загружает `.env` файл а уже либа `env`
- читает теги struct
- ищет переменные окружения
- конвертирует типы
- применяет default
- проверяет required