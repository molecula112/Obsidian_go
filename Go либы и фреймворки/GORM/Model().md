`Model` используется чтобы явно указать таблицу  если GORM не может её определить автоматически
```go
db.Model(&User{})
```
Обычно используется перед запросом, чтобы явно указать таблицу, чаще всего на `Update` методах.

Часто можно без него если передаешь объект в терминальный метод:
```go
db.First(&user)  
db.Find(&users)  
db.Create(&user)
```
GORM сам определит таблицу по типу структуры
```go
db.Find(&users)
```
SQL:
```sql
SELECT * FROM users;
```
---
А вот кейс когда `Model` нужен:
```go
db.Model(&User{}).Where("age > ?", 18).Update("active", true)
```
Тут мы не указываем над какой таблицей работаем, поэтому тут нужен

---
Вот еще прикольный кейс
c `Model`
```go
db.Model(&User{}).Where("age > ?", 18).Delete(nil)
```
Как видишь в `Delete` можно не передавать ничего ибо указана модель, а вот без `Model`:
```go
db.Where("age > ?", 18).Delete(repomodel.User{})
```
---
Мини-инсайт:
`Model → FROM table`

Правило:
```
если передаёшь struct → Model не нужен  
если используешь map / raw update → Model нужен
```
