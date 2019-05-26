# 2장 의미 있는 이름

2장에서는 이름을 잘 짓는 간단한 규칙을 몇 가지 소개하고 있다.  

## 의도를 분명히 밝혀라
변수나 함수 그리고 클래스 이름은 다음과 같은 굵직한 질문에 모두 답해야 한다.
> 변수(혹은 함수나 클래스)의 존재 이유는?  
> 수행 기능은?  
> 사용 방법은?

만약 추가적인 주석이 필요하다면, 이름만으로는 의도를 분명히 드러내지 못했다는 말이다.

---
```java
public List<int[]> getThem() {
  List<int[]> list1 = new ArrayList<>();
  for (int[] x : theList) {
    if (x[0] == 4)
      list1.add(x);
  }
  return list1;
}
```

위의 코드는 의도가 명확히 밝혀지지 않아 코드가 하는 일을 짐작하기가 어렵다. 아래와 같이 각 개념에 이름만 붙여도 코드가 상당히 나아진다.

```java
public List<int[]> getFlaggedCells() {
  List<int[]> flaggedCells = new ArrayList<>();
  for (int[] cell : gameBoard) {
    if (cell[STATUS_VALUE] == FLAGGED)
      flaggedCells.add(cell);
  }
  return flaggedCells;
}
```

한 걸음 더 나아가, int 배열 대신 '칸'을 의미하는 간단한 클래스를 만들어도 좋다. `isFlagged`라는 좀 더 명시적인 함수를 사용해 `FLAGGED`라는 상수를 감춰도 좋을 것이다.

```java
public List<Cell> getFlaggedCells() {
  List<Cell> flaggedCells = new ArrayList<>();
  for (Cell cell : gameBoard) {
    if (cell.isFlagged())
      flaggedCells.add(cell);
  }
  return flaggedCells;
}
```

## 그릇된 정보를 피하라
- 코드에 그릇된 단서를 남겨서는 안된다.
- 나름대로 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용해도 안된다.
- 서로 흡사한 이름을 사용하지 않도록 주의한다.  
  (ex. `XYZControllerForEfficientHandlingOfStrings`와 `XYZControllerForEfficientStorageOfStrings`)

## 의미 있게 구분하라
- 비록 컴파일러를 통과할지라도, 연속된 숫자를 덧붙이거나(ex. `a1`, `a2`, ..., `aN`) 의미없는 단어(noise word)(ex. `variable`)를 추가하는 방식은 적절하지 못하다.
- 읽는 사람이 차이를 알도록 이름을 지어야 한다.

## 발음하기 쉬운 이름을 사용하라
- 발음하기 쉬운 이름은 중요하다. 프로그래밍은 사회 활동이기 때문이다.

## 검색하기 쉬운 이름을 사용하라
