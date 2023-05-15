# How add parameters in url_path:
___

We want add limit and skip parameters: 
```python
@router.get('/', response_model=List[schemas.Post])
def get_posts(db: Session = Depends(get_db), current_user: int = Depends(get_current_user), limit: int = 10,
              skip: int = 0):
    # posts = db.query(models.Post).filter(models.Post.owner_id == current_user.id).all()
    posts = db.query(models.Post).limit(limit).offset(2).all()
    return posts
```

and when u send next request: 

{{URL}}posts?limit=2

result will be:
```text
[
    {
        "title": "authenticate post",
        "content": "content last",
        "published": true,
        "id": 4,
        "created_at": "2023-04-17T14:50:15.648063+03:00",
        "owner_id": 12,
        "owner": {
            "id": 12,
            "email": "admin@i.ua",
            "created_at": "2023-04-14T12:15:18.391024+03:00"
        }
    },
    {
        "title": "authenticate post",
        "content": "content last",
        "published": true,
        "id": 5,
        "created_at": "2023-04-17T14:50:16.595901+03:00",
        "owner_id": 12,
        "owner": {
            "id": 12,
            "email": "admin@i.ua",
            "created_at": "2023-04-14T12:15:18.391024+03:00"
        }
    },
    {
        "title": "authenticate post",
        "content": "content last",
        "published": true,
        "id": 6,
        "created_at": "2023-04-17T14:50:17.374717+03:00",
        "owner_id": 12,
        "owner": {
            "id": 12,
            "email": "admin@i.ua",
            "created_at": "2023-04-14T12:15:18.391024+03:00"
        }
    },
    {
        "title": "authenticate post",
        "content": "content last",
        "published": true,
        "id": 7,
        "created_at": "2023-04-17T14:50:18.097339+03:00",
        "owner_id": 12,
        "owner": {
            "id": 12,
            "email": "admin@i.ua",
            "created_at": "2023-04-14T12:15:18.391024+03:00"
        }
    }
]
```
## Add search:

```python
@router.get('/', response_model=List[schemas.Post])
@router.get('/', response_model=List[schemas.Post])
def get_posts(db: Session = Depends(get_db), current_user: int = Depends(get_current_user),search: Optional[str] = ""):
    posts = db.query(models.Post).filter(models.Post.title.contains(search)).all()
    return posts
```