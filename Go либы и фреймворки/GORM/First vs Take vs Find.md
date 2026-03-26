Методы которые делают примерно похожие вещи

 `First` — взять первую запись с ORDER BY(стандартный способ получить 1 запись)
```go
db.First(&user)
```
SQL примерно такой:
```sql
SELECT * FROM users ORDER BY id LIMIT 1;
```
Особенности:
1. Автоматически делает `ORDER BY primary key`
2. Возвращает `gorm.ErrRecordNotFound`, если записи нет

Если используешь вместе с `WHERE` то его особенность не примечательна, также вместо `WHERE` можно сразу написать `db.First(&user, userID)`

---
`Take` — взять любую запись
```go
db.Take(&user)
```
SQL примерно такой:
```sql
SELECT * FROM users LIMIT 1;
```
Особенности:
1. Без ORDER BY
2. Быстрее на больших таблицах
3. Тоже возвращает `ErrRecordNotFound`

Используют когда не важно какая запись

---
`Find` — получить список
```go
db.Find(&users)
```
SQL примерно такой:
```sql
SELECT * FROM users;
```
Особенности:
1. Возвращает `slice`
2. Никогда не возвращает `ErrRecordNotFound`

Если ничего не найдено:
```go
users = []  
err = nil
```
Поэтому проверки обычно такие:
```go
if err != nil {  
	return err  
}
```
