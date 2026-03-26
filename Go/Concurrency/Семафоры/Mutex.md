`Mutex` - это примитив синхронизации, который гарантирует, что в один момент времени только одна goroutine выполняет критическую секцию кода
#### Основная идея:
Если несколько goroutine работают с одними и теми же данными, может возникнуть data race
```go
var counter int  
  
for i := 0; i < 1000; i++ {  
	go func() {  
		counter++  
	}()  
}
```
`counter++` не атомарная операция, то есть чтобы выполнить ++ нужно пройти:
```
read  
modify  
write
```

Следовательно если несколько горутин это делают то будет гонка:
```
counter = 5  
  
goroutine1 read → 5  
goroutine2 read → 5  
goroutine1 write → 6  
goroutine2 write → 6
```

#### Как Mutex решает проблему
```go
var mu sync.Mutex  
var counter int  
  
mu.Lock()  
counter++  
mu.Unlock()
```
Теперь:
```
goroutine1 → Lock  
goroutine2 → ждёт  
goroutine3 → ждёт
```
После `Unlock()` следующая goroutine продолжает.

---
# Критическая секция
Код между:
```go
mu.Lock()  
...
mu.Unlock()
```
Называется critical section - участок кода,  который одновременно может выполнять только одна goroutine

# Типичный Go-паттерн
Очень распространённый стиль:
```go
mu.Lock()  
defer mu.Unlock()  
// работа с данными
```
Почему `defer`: гарантирует Unlock даже при panic или return

Связано: [[Lock() vs TryLock()]]
Связано: [[Устройство Mutex]]
Связано: [[Режимы работы Mutex]]
