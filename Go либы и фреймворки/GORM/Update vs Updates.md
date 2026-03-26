Разница простая:
```
Update → обновить ОДНО поле  
Updates → обновить НЕСКОЛЬКО полей
```

`Update`
```go
db.Model(&User{}).Where("id = ?", id).Update("email", "new@mail.com")
```
SQL такой:
```sql
UPDATE users SET email = 'new@mail.com' WHERE id = ?;
```

Особенности:
1. Обновляет одно поле
2. Принимает имя поля + значение
---
`Updates`
```go
db.Model(&User{}).  
Where("id = ?", id).  
Updates(User{  
	Email: "new@mail.com",  
	Age: 30,  
})
```
SQL такой:
```sql
UPDATE users SET email='new@mail.com', age=30 WHERE id=?;
```
---
Можно использовать с `map`, вопрос зачем?
`map[string]interface{}` вот такая мапа говорит ключ с любым значением:

```go
db.Model(&User{}).  
Where("id = ?", id).  
Updates(map[string]interface{}{  
	"email": "new@mail.com",  
	"age": 30,  
})
```
И типо видишь, мы тут вставляем и строку, и число и все збс, с этим как раз связана частая ловушка в GO:
`Когда используешь struct, GORM игнорирует zero values`

Пример:
```go
Updates(User{  
	Email: "",  
	Age: 0,  
})
```
GORM не обновит эти поля, он считает их пустыми

Поэтому в таком случае решается как раз через `map`:
```go
Updates(map[string]interface{}{  
	"email": "",  
	"age": 0,  
})
```

