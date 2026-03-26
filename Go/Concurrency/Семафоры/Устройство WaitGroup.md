```
Концептуально WaitGroup — это счетчик задач.  
  
Но внутри runtime реализует его через атомарные операции  
и семафоры, чтобы эффективно блокировать и пробуждать goroutine
```

Внутри `sync.WaitGroup` примерно такая структура (упрощённо):
```go
type WaitGroup struct {  
    state1 uint64  
    sema   uint32  
}
```

В `state1` хранятся два числа сразу:
```
| counter | waiters |
```
1. counter — сколько задач осталось
2. waiters — сколько горутин ждут `Wait()`

В `sema` - лежит семафор, используемый runtime Go для блокировки, и пробуждения goroutine, которые вызывают Wait()

---
#### Что происходит при `Add`
Когда вызывается:
```go
wg.Add(1)
```
runtime делает:
1. Атомарно увеличивает `counter`  
2. Проверяет не стал ли он **отрицательным**  
3. Если `counter == 0` → будит все goroutine из `Wait()`
---
#### Что происходит при `Wait`
Когда вызывается:
```go
wg.Wait()
```

runtime делает:
1. Проверяет `counter`  
2. Если `counter == 0` - сразу выходит  
3. Иначе:
	1. увеличивает `waiters`
	2. блокирует горутину через runtime semaphore
---
#### Что делает `Done`
```go
wg.Done()
```
Когда вызывается, это просто:
```go
wg.Add(-1)
```

Но внутри:
1. атомарное уменьшение `counter`  
2. если `counter == 0`, runtime разблокирует все `Wait()`
---
#### Почему WaitGroup очень быстрый
Потому что там используются:
1. atomic operations
2. runtime semaphores
3. lock-free логика
---
#### Почему нельзя копировать WaitGroup
Потому что внутри есть runtime semaphore, если скопировать:
```go
wg2 := wg
```
у тебя получится два счетчика, но один semaphore

Поэтому Go даже добавил специальную структуру:
```go
type WaitGroup struct {  
    noCopy noCopy   --> что типо нельзя копировать, go vet подскажет warn
	
	state atomic.Uint64  
    sema  uint32  
}
```
