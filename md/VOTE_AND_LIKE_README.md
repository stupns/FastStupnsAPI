

```python
class Vote(BaseModel):
    post_id: int
    dir: conint(le=1)
```

dir - це ціле число в діапазоні від 0 до 1, яке вказує напрямок голосування. Значення 1 означає, що голос був поданий
"за" пост, а значення 0 означає голос "проти" поста.

У цьому коді використовується тип conint (обмежене ціле число) з модуля pydantic, який забезпечує перевірку на
відповідність заданому діапазону значень. У даному випадку, le=1 означає, що значення dir повинно бути менше
або дорівнювати 1.

Цей код описує функцію vote, яка є обробником запиту POST на маршруті /. Функція очікує отримати об'єкт Vote в тілі 
запиту та дві залежності: об'єкт сесії бази даних db та ідентифікатор поточного користувача current_user.

У функції спочатку виконується запит до бази даних, щоб перевірити, чи є вже голос користувача за цей пост. Якщо такий
голос вже існує, то повертається помилка HTTP 409 Conflict. Якщо ж такого голосу ще немає, то відбувається запис нового
голосу в базу даних, і повертається повідомлення про успішне додавання голосу.

Якщо значення dir в Vote рівне 0, то функція виконує запит до бази даних, щоб перевірити наявність голосу користувача
за цей пост. Якщо такий голос не знайдено, то повертається помилка HTTP 404 Not Found. Якщо ж голос користувача знайдено,
то відбувається його видалення з бази даних, і повертається повідомлення про успішне видалення голосу.

```python
@router.post("/", status_code=status.HTTP_201_CREATED)
def vote(vote: schemas.Vote, db: Session = Depends(database.get_db), current_user: int = Depends(oauth2.get_current_user)):
    vote_query = db.query(models.Vote).filter(
        models.Vote.post_id == vote.post_id, models.Vote.user_id == current_user.id)
    found_vote = vote_query.first()
    if vote.dir == 1:
        if found_vote:
            raise HTTPException(status_code=status.HTTP_409_CONFLICT,
                                detail=f"user {current_user.id} has alredy voted on post {vote.post_id}")
        new_vote = models.Vote(post_id=vote.post_id, user_id=current_user.id)
        db.add(new_vote)
        db.commit()
        return {"message": "successfully added vote"}

    else:
        if not found_vote:
            raise HTTPException(

                status_code=status.HTTP_404_NOT_FOUND, detail="Vote does not exist")

        vote_query.delete(synchronize_session=False)

        db.commit()

        return {"message": "successfully deleted vote"}
```