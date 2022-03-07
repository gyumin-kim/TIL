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