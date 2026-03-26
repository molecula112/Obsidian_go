Это способ загрузить связанные данные за минимум запросов. Без `Preload` часто возникает **N+1 проблема**
```go
type User struct {  
	ID uint64  
	Login string  
	Orders []Order  
}  
  
type Order struct {  
	ID uint64  
	UserID uint64  
	Price int  
}
```

Если написать:
```go
var user User  
db.First(&user, 1)
```
GORM сделает:
```go
SELECT * FROM users WHERE id = 1;
```
Но `user.Orders` будет `[]` потому что ORM не загрузил связи

А с `Preload`
```go
var user User  
  
db.Preload("Orders").First(&user, 1)
```
GORM сделает 2 запроса:
```sql
SELECT * FROM users WHERE id = 1;  
  
SELECT * FROM orders WHERE user_id IN (1);
```
И структура будет:
```json
user = {  
    ID: 1,  
    Login: "alex",  
    Orders: [  
        {ID:1, Price:100},  
        {ID:2, Price:200},  
    ]  
}
```

