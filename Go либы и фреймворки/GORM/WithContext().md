В GORM данный метод `db.WithContext(ctx)` просто передает контекст дальше в драйвер базы данных, то есть происходит такая цепочка действий:
```
handler  
↓  
service  
↓  
repository  
↓  
gorm  
↓  
database/sql  
↓  
postgres / mysql driver
```
и `ctx` проходит через все уровни 

Пример с отменой запроса:
```go
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)  
defer cancel()  
  
db.WithContext(ctx).Find(&users)
```
Если запрос выполняется дольше 2 секунд, происходит:
```
context cancel  
↓  
database/sql получает сигнал  
↓  
driver отправляет cancel в БД  
↓  
запрос прерывается
```
