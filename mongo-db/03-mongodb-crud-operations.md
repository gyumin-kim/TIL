# MongoDB CRUD Operations

- Create Operations
    - `db.collection.insertOne()`
        ```json
            db.users.insertOne(
                {
                    name: "sue",
                    age: 26,
                    status: "pending"
                }   // 하나의 document
            )
        ```
    - `db.collection.insertMany()`
- Read Operations
    - `db.collection.find()`
    - query filter 혹은 criteria를 명시할 수도 있다.
        ```json
            db.users.find(
                { age: { $gt: 18 } },   // query criteria
                { name: 1, address: 1 } // projection
            ).limit(5)                  // cursor modifier
        ```
- Update Operations
    - `db.collection.updateOne()`
    - `db.collection.updateMany()`
        ```json
            db.users.updateMany(
                { age: { $lt: 18 } },           // update filter
                { $set: { status: "reject" } }  // update action
            )
        ```
    - `db.collection.replaceOne()`
- Delete Operations
    - `db.collection.deleteOne()`
    - `db.collection.deleteMany()`
        ```json
            db.users.deleteMany(
                { status: "reject" }    // delete filter
            )
        ```
- MongoDB에서 write operation(create, update, delete)은, 1개의 document level에서 [atomic](https://docs.mongodb.com/v4.4/reference/glossary/#std-term-atomic-operation)하다(원자적이다).