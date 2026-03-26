Вот так можно накатить в проект
```sh
go install github.com/pressly/goose/v3/cmd/goose@latest
```

Инит в проект
```sh
mkdir internal/migrations
goose -dir internal/migrations create initdb
```

