# Delete Documents
https://docs.mongodb.com/v4.4/tutorial/remove-documents/

## Delete Only One Document
```json
db.inventory.deleteOne( { status: "D" } )
```

## Delete All Documents that Match a Condition
```json
db.inventory.deleteMany({ status : "A" })
```

## Delete All Documents
```json
db.inventory.deleteMany({})
```
- 전체 컬렉션을 삭제하고자 할 때는 drop을 사용한다.
    ```json
    db.inventory.drop()
    ```
    - drop은 해당 컬렉션과 관련된 모든 인덱스도 함께 지운다.
    https://docs.mongodb.com/v4.4/reference/method/db.collection.drop/