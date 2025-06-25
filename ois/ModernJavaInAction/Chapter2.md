# Chapter 2 동작 파라미터화 코드 전달하기

- 요구사항은 항상 바뀐다.
- 요구사항에 유연하게 대처하면서 새로운 기능의 구현이 쉽고 유지보수가 코드를 작성해야 한다.
- 동작 파라미터화
    
    > 동작 파라미터화(behavior parameterization)를 이용하면 자주 바뀌는 요구사항에 효과적으로 대응할 수있다. 동작 파라미터화란 아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미한다.
    이 코드 블록은 나중에 프로그램에서 호출한다. 즉, 코드 블록의 실행은 나중으로 미뤄진다. 예를 들어 나중에 실행될 메서드의 인수로 코드 블록을 전달할 수 있다. 결과적으로 코드 블록에 따라 메서드의 동작이 파라미터화된다.
    > 
    - 짝수만 출력하는 코드
        
        ```java
        public void printEvenNumbers(List<Integer> numbers) {
            for (int n : numbers) {
                if (n % 2 == 0) {
                    System.out.println(n);
                }
            }
        }
        ```
        
    - 홀수를 출력하거나, 5보다 큰 수를 출력하고 싶다면?
        - 각각 메서드를 다 만든다?
    - 동작 파라미터화 적용
        
        ```java
        public void printNumbers(List<Integer> numbers, Predicate<Integer> condition) {
            for (int n : numbers) {
                if (condition.test(n)) {
                    System.out.println(n);
                }
            }
        }
        ```
        
        - 짝수만 출력
            
            ```java
            printNumbers(list, n -> n % 2 == 0);
            ```
            
        - 홀수만 출력
            
            ```java
            printNumbers(list, n -> n % 2 != 0);
            ```
            
        - 5보다 큰 수만 출력
            
            ```java
            printNumbers(list, n -> n > 5);
            ```
            
    - Interface Predicate<T>
        
        
        | Modifier and Type | Method | Description |
        | --- | --- | --- |
        | boolean | test(T t) | Evaluates this predicate on the given argument. |
        - 동작(메서드, 함수, 2급 시민)을 인자로 직접 전달 불가
        - 함수형 인터페이스를 통해 동작을 객체로 포장 → 1급 시민으로 취급

## 2.1 변화하는 요구사항에 대응하기

- 농장 재고 목록 애플리케이션
    - 농부의 요구사항
        - 녹색 사과만 필터링 하는 기능 추가

### 2.1.1 첫 번째 시도 : 녹색 사과 필터링

- 사과 색 정의
    
    ```java
    enum Color {RED, GREEN}
    ```
    
- 녹색 사과 필터링
    
    ```java
    public static List<Apple> filterGreenApples(List<Apple> inventory) {
        List<Apple> result = new ArrayList<>(); // 사과 리스트
        for (Apple apple : inventory) {
            if (GREEN.equals(apple.getColor()) { // 녹색 사과만 선택
                result.add(apple;)
            }
        }
        
        return result;
    }
    ```
    
- 다른 색의 사과도 필터링 하고 싶다면?
    - 같은 메서드 계속 생성? → 거의 비슷한 코드가 반복되면 추상화

### 2.1.2 두 번째 시도 : 색을 파라미터화

- filterGreenApples를 반복하지 않고 filterRedApples 구현하는 방법은?
- 색을 파라미터화 할 수 있게 메서드에 파라미터 추가
    
    ```java
    public static List<Apple> filterApplesByColor(List<Apple> inventory, Color color) {
        List<Apple> result = new ArrayList<>();
        for (Apple apple : inventory) {
            if (apple.getColor().equals(color)) {
                result.add(apple);
            }
        }
        
        return result;
    }
    
    // 메서드 호출
    List<Apple> greenApples = filterApplesByColor(inventory, GREEN);
    List<Apple> greenApples = filterApplesByColor(inventory, RED);
    ```
    
- 농부가 무게 150그램 이상인 사과를 분류하고 싶다고 한다면?
    
    ```java
    public static List<Apple> filterApplesByWeight(List<Apple> inventory, int weight) { 
        List<Apple> result = new ArrayList<>();
        for (Apple apple: inventory) { 
            if (apple.getWeight() > weight) { 
                result.add(apple);
            } 
        } 
        
        return result;
    }
    ```
    
- 색 필터, 무게 필터 코드 대부분이 중복
    - DRY(don’t repeat yourself) 원칙 위반
    - filter 메서드로 병합?
        - 색이나 무게 중 기준을 가리키는 플래그 추가
        - 실전에서 사용 금지

### 2.1.3 세 번째 시도 : 가능한 모든 속성으로 필터링

- 모든 속성을 파라미터로 추가 → 실전에서 금지하는 이유
    
    ```java
    public static List<Apple> filterApples(List<Apple> inventory, Color color, int weight, boolean flag) { 
        List<Apple> result = new ArrayList<>();
        for (Apple apple: inventory) { 
            if ((flag && apple.getColor().equals(color)) || 
                (!flag && apple.getWeight() > weight)) {
                result.add(apple);
            } 
        } 
        
        return result;
    }
    
    // 메서드 호출
    List<Apple> greenApples = filterApples(inventory, GREEN, 0, true);
    List<Apple> heavyApples = filterApples(inventory, null, 150, false);
    ```
