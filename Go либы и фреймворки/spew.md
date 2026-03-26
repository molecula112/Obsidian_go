```
go get github.com/davecgh/go-spew/spew
```

Либа используется для удобного дебага сложных структур, но только на дебаг стендах.
Если обычный `fmt.Println` такую структуру:
```go
type Config struct {  
	ServiceName string  
	Port int  
	Database struct {  
		Host string  
		Port int  
	}  
}
```
Выведет так:
```
{account-service 9000 {localhost 5432}}
```

То `spew`:
```
(config.Config) {  
	ServiceName: (string) "account-service",  
	Port: (int) 9000,  
	Database: (struct) {  
		Host: (string) "localhost",  
		Port: (int) 5432  
	}  
}
```

2 главные команды для вывода в таком виде:
```go
spew.Dump(user) --> выводит в консоль
spew.Sdump(user) --> возвращает строку
```

И важный набор конфигов:
```go
spew.Config.Indent = "  "  --> добавляет отсупы при печати структур
spew.Config.DisablePointerAddresses = true  --> отключает вывод адресов
spew.Config.DisableCapacities = true  --> отключает инфу о вместимости слайсов
spew.Config.SortKeys = true --> при работе с мапой сортирует ключи
```