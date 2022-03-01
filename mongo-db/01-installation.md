# Installation

- https://docs.mongodb.com/v4.4/tutorial/install-mongodb-on-os-x/

## Installing MongoDB 4.4 Community Edition
1. `brew tap mongodb/brew`
2. `brew install mongodb-community@4.4`

## Run MongoDB Community Edition
- running MongoDB (mongod process) as a macOS service
    - `brew services start mongodb-community@4.4`
- stopping a mongod
    - `brew services stop mongodb-community@4.4`
- 실행 중인 service 확인
    - `brew services list`
- 환경 변수 설정 (zsh)
    - ex) `export PATH=...:/usr/local/Cellar/mongodb-community@4.4/4.4.11/bin`