Вот так можно засетапить базовый репо:
```go
package repository  
  
import (  
    "github.com/rs/zerolog"  
    "gorm.io/gorm"
)  
  
type Repository struct {  
    db     *gorm.DB  
    logger *zerolog.Logger  
}  
  
func NewRepository(db *gorm.DB, logger *zerolog.Logger) *Repository {  
    return &Repository{  
       db: db,  
       logger: logger,  
    }  
}
```

Сетап репозитория + подключения к бд:
```go
package main  
  
import (  
    "account/internal/config"  
    "account/internal/logger"    "account/internal/repository"    "log"  
    "gorm.io/driver/postgres"    "gorm.io/gorm")  
  
func main() {    
    db, err := gorm.Open(postgres.Open(cfg.DbDsn), &gorm.Config{})  
    if err != nil {  
       logger.Error().Msgf("Не удалось подключиться к бд: %v", err)  
       return  
    }  
    repo := repository.NewRepository(db, &logger)  
    _ = repo    
}
```

И вот сам CRUD
```go
package repository  
  
import (  
    "account/internal/model"  
    "account/internal/repository/mapper"    repomodel "account/internal/repository/model"  
    "context"    "errors"  
    "github.com/rs/zerolog"    "gorm.io/gorm"    "gorm.io/gorm/clause")  
  
type Repository struct {  
    db     *gorm.DB  
    logger *zerolog.Logger  
}  
  
func NewRepository(db *gorm.DB, logger *zerolog.Logger) *Repository {  
    return &Repository{  
       db:     db,  
       logger: logger,  
    }  
}  
  
func (r *Repository) CreateUser(ctx context.Context, createUser model.CreateUser) error {  
    repoUser := mapper.CreateUserToRepoUser(createUser)  
    res := r.db.WithContext(ctx).Clauses(clause.OnConflict{UpdateAll: true}).Create(&repoUser)  
    if res.Error != nil {  
       r.logger.Error().Err(res.Error).Msg("failed to create user")  
       return res.Error  
    }  
    return nil  
}  
  
func (r *Repository) GetUser(ctx context.Context, userID uint64) (*model.User, error) {  
    var repoUser repomodel.User  
    res := r.db.WithContext(ctx).Where("id = ?", userID).First(&repoUser)  
  
    if errors.Is(res.Error, gorm.ErrRecordNotFound) {  
       return nil, res.Error  
    }  
  
    if res.Error != nil {  
       r.logger.Error().Err(res.Error).Msg("failed to get user by id")  
       return nil, res.Error  
    }  
  
    findedUser := mapper.RepoUserToUser(repoUser)  
  
    return &findedUser, nil  
}  
  
func (r *Repository) GetUsers(ctx context.Context, limit int, offset int) ([]model.User, error) {  
    var repoUsers []repomodel.User  
    res := r.db.WithContext(ctx).Limit(limit).Offset(offset).Find(&repoUsers)  
    if res.Error != nil {  
       r.logger.Error().Err(res.Error).Msg("failed to get users")  
       return nil, res.Error  
    }  
    return mapper.RepoUsersToUsers(repoUsers), nil  
}  
  
func (r *Repository) UpdateUser(ctx context.Context, userID uint64, updateUser model.UpdateUser) error {  
    repoUser := mapper.UpdateUserToRepoUser(updateUser)  
    res := r.db.WithContext(ctx).Where("id = ?", userID).Updates(repoUser)  
    if res.Error != nil {  
       r.logger.Error().Err(res.Error).Msg("failed to update user")  
       return res.Error  
    }  
    return nil  
}  
  
func (r *Repository) DeleteUser(ctx context.Context, userID uint64) error {  
    res := r.db.WithContext(ctx).Where("id = ?", userID).Delete(repomodel.User{})  
    if res.Error != nil {  
       r.logger.Error().Err(res.Error).Msg("failed to delete user")  
       return res.Error  
    }  
    return nil  
}
```