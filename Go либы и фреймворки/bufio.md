Данная либа позволяет работать с вводами, в том числе, с вводом с клавиатуры:
```go
input := bufio.NewScanner(os.Stdin)  
input.Scan() --> ввод чего-то
fmt.Println(input.Text()) --> вывод того что ввели
```
