Вот так можно накатить в систему
```shell
brew install golang-migrate 
```

Вот так можно проинитить и создать папку schema с пустыми файлами миграций
```shell
migrate create -ext sql -dir ./schema -seq init
```

Вот так выполнить миграцию(в конце название миграции)
```
migrate -path ./schema -database 'postgres://postgres:123@localhost:5432/postgres?sslmode=disable' up
```

Прикольная фича если произошла проблема при выполнении миграции, такая миграция считается грязной, чтобы не дропать бд можно
```
update schema_migrations set version=`000001`, dirty=falsel
```