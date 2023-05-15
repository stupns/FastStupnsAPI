# Many to one field in models
___
```python

class Post(Base):
    __tablename__ = 'posts'
    ...
    ...
    owner_id = Column(Integer, ForeignKey("users.id", ondelete="CASCADE"), nullable=False)
```

```python
class Post(PostBase):
    id: int
    created_at: datetime
    owner_id: int

    class Config:
        orm_mode = True
```

```python
@router.post('/', status_code=status.HTTP_201_CREATED, response_model=schemas.Post)
def create_posts(post: schemas.PostCreate, db: Session = Depends(get_db),
                 current_user: int = Depends(get_current_user)):
    ...
    ...
    new_post = models.Post(owner_id=current_user.id, **post.dict())
    
    

@router.delete('/{id}', status_code=status.HTTP_204_NO_CONTENT)
def delete_post(id: int, db: Session = Depends(get_db),
                current_user: int = Depends(get_current_user)):
    ...
    ...
    if post.first() == None:
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail=f"post with id : {id} does not exist")
    if post.ownder_id != current_user.id:
        raise HTTPException(status_code=status.HTTP_403_FORBIDDEN, detail=f"not authorized to perform requested action")
```

## Show specific posts for users

```python
@router.get('/', response_model=List[schemas.Post])
def get_posts(db: Session = Depends(get_db)):
    posts = db.query(models.Post).filter(models.Post.owner_id == current_user.id).all()
    return posts
```

## Show info about owner in JSON

```python
# models.py
class Post(Base):
    __tablename__ = 'posts'
    ...
    ...
    owner = relationship("User")
```

```python
# schemas.py
class Post(PostBase):
    id: int
    created_at: datetime
    owner_id: int
    owner: UserOut
```

Result will be: 
```text
    {
        "title": "new title",
        "content": "new content",
        "published": true,
        "id": 1,
        "created_at": "2023-04-17T13:22:56.216365+03:00",
        "owner_id": 8,
        "owner": {
            "id": 8,
            "email": "stupns@i.ua",
            "created_at": "2023-04-13T15:03:05.107685+03:00"
        }
    },
```

___

[<-- prev step](2_JWT_AUTHENTICATION_README.md)___________________________________________________[next step -->](4_REGULAR_PARAM_README.md)