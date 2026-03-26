`logger.Error()`
```go
r.logger.Error().Msgf("failed to save user")
```
- Создаёт новый Event с уровнем `error`.
- Пока ты не вызовешь `Msg()`, ничего не отправляется в лог.
- После `Error()` можно добавить поля через `Str()`, `Int()`, `Err()` и т.д.
```go
logger.Error().  
	Err(res.Error). // добавляет поле "error": res.Error  
	Str("user", "123").  
	Msg("Failed to save user")
```

---
`logger.Err()` 
```go
r.logger.Err(res.Error).Msg("failed to save user")
```
- Это короткий способ логировать ошибку без отдельного `Error()`.
- Внутри `Err()` zerolog делает примерно то же самое, что `Error().Err(err)`

---

|Сценарий|Что использовать|Примечание|
|---|---|---|
|Нужно просто логнуть ошибку|`logger.Err(err).Msg("text")`|Короче, автоматически error-level|
|Нужно логнуть ошибку **+ другие поля**|`logger.Error().Err(err).Str(...).Msg(...)`|Явно, можно цепочку полей|
|Нужен Event с уровнем **не error**, но добавить поле ошибки|`logger.Info().Err(err).Msg(...)`|`Err()` не меняет уровень Event, берёт текущий уровень (`Info` в примере)|

Правильный вариант все же этот:
```go
r.logger.Error().Msgf("failed to get users")
```

Связано: [[zerologs Events]]
