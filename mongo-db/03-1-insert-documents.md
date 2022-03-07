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

## `_id` field
- MongoDB에서 하나의 collection에 저장된 각각의 document는 unique한 `_id` 값을 가지며, 이는 primary key로서 존재한다.
- 삽입되는 document에 `_id` 필드가 생략된 경우, MongoDB 드라이버가 자동으로 `ObjectId` 타입의 `_id` 값을 생성한다.