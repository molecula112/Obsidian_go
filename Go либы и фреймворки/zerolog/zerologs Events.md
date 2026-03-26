`Если грубо — Event в zerolog это как коробка, куда ты складываешь уровень, поля и сообщение, и потом отдаёшь эту коробку в лог, на джавовском короче это способ сбилдить лог(builder)`

В `zerolog` каждый лог — это Event
Event содержит:
- уровень (`debug`, `info`, `error` и т.д.)
- сообщение (`Msg("текст")`)
- поля(`Str`, `Int`, `Err`, `Bool` и т.д.)
Каждый вызов `logger.Error()`, `logger.Info()`, `logger.Debug()` создаёт новый Event, который потом отправляется в вывод, когда ты вызываешь `Msg()`.

```go
logger.Info().  
Str("user", "123").  
Int("attempt", 1).  
Msg("User logged in")
```

Это создаст Event вроде JSON:
```json
{
  "level": "info",
  "user": "123",
  "attempt": 1,
  "message": "User logged in",
  "time": "2026-03-11T12:00:00Z"
}
```

В zerolog ты сначала строишь Event, потом вызываешь `Msg()` для финального логирования:
```go
ev := logger.Error() // создаём Event с уровнем error  
ev.Str("user", "123") // добавляем поле  
ev.Err(err) // добавляем ошибку  
ev.Msg("Failed action") // финализируем и отправляем
```
