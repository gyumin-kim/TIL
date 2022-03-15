# Insert Documents

## Insert a Single Document
```json
db.inventory.insertOne(
   { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm" } }
)
```
- 위에서 삽입한 document 찾기
    ```json
    db.inventory.find( { item: "canvas" } )
    ```

## Insert Multiple Documents
- https://docs.mongodb.com/v4.4/reference/method/db.collection.insertMany/#mongodb-method-db.collection.insertMany
```json
db.inventory.insertMany([
   { item: "journal", qty: 25, tags: ["blank", "red"], size: { h: 14, w: 21, uom: "cm" } },
   { item: "mat", qty: 85, tags: ["gray"], size: { h: 27.9, w: 35.5, uom: "cm" } },
   { item: "mousepad", qty: 25, tags: ["gel", "blue"], size: { h: 19, w: 22.85, uom: "cm" } }
])
```
- 위에서 삽입한 document 찾기
    ```json
    db.inventory.find( { qty: 25 } )
    db.inventory.find( { "size.uom": "cm" } )
    ```
- MongoDB에서는 48MB보다 큰 메시지를 허용하지 않으므로, 48MB보다 큰 삽입을 시도하면 삽입된 데이터를 48MB 크기의 일괄 삽입 여러 개로 분할한다.
- insertMany()의 2번째 매개변수로 options document를 지정할 수 있다.
    ```json
    db.movies.insertMany([
        {"_id": 0, "title": "Top Gun"},
        {"_id": 1, "title": "Back to the Future"}
        {"_id": 1, "title": "Gremlins"},
        {"_id": 2, "title": "Aliens"}],
        {"ordered": false}  // 기본값은 true
    )
    ```
    - `ordered`를 false로 지정하면, mongod가 성능을 개선하기 위해 삽입할 document의 순서를 재배열할 수도 있다.
    - `ordered`가 true이면 삽입 도중 오류 발생 시, 이후의 document는 삽입되지 않는다. 반면 `ordered`가 false이면 일부 삽입의 오류 발생 여부와 관계없이 모든 document 삽입을 시도한다.

## `_id` field
- MongoDB에서 하나의 collection에 저장된 각각의 document는 unique한 `_id` 값을 가지며, 이는 primary key로서 존재한다.
- 삽입되는 document에 `_id` 필드가 생략된 경우, MongoDB 드라이버가 자동으로 `ObjectId` 타입의 `_id` 값을 생성한다. 

