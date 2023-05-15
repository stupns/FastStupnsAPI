# MANUAL
create config.py
```python
from pydantic import BaseSettings


class Settings(BaseSettings):
    DB_HOST: str
    DB_PORT: str
    DB_PASSWORD: str
    DB_USERNAME: str
    DB_NAME: str
    SECRET_KEY: str
    ALGORITHM: str
    ACCESS_TOKEN_EXPIRE_MINUTES: int

    class Config:
        env_file = '.env'


settings = Settings()
```

create .env file:
```ignore
DB_HOST=localhost
DB_PORT=5432
DB_PASSWORD=postgres
DB_USERNAME=postgres
DB_NAME=db_fast_stupns_api
SECRET_KEY=09d25e094faa6ca2556c818166b7a9563b93f7099f6f0f4caa6cf63b88e8d3e7
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=60
```
refactor code : 

```python
SQLALCHEMY_DATABASE_URL = f"postgresql://{settings.DB_USERNAME}:{settings.DB_PASSWORD}@{settings.DB_HOST}:" \
                          f"{settings.DB_PORT}/{settings.DB_NAME}"
```