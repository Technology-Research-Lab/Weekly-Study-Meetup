# Chapter 5. 스트림 활용

## 5.1 필터링

### 5.1.1 프레디케이트로 필터링

- filter 메서드
    - predicate를 인수로 받아서 일치하는 요소 반환
        - predicate → boolean을 반환하는 함수
            
            ```java
            List<Dish> vegetarianMenu = menu.stream()
                                            .filter(Dish::isVegetarian)
                                            .collect(toList());
            ```
            
            - Dish 객체의 isVegetarian 메서드(boolean 반환)를 참조하여 반환되는 true인 dish들만 걸러서 리스트로 반환한다.

### 5.1.2 고유 요소 필터링

- distinct 지원 → 고유 요소 반환
    
    ```java
    List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
    numbers.stream()
           .filter(i -> i % 2 == 0)
           .distinct()
           .forEach(System.out::println);
    ```

## 5.2 스트림 슬라이싱

### 5.2.1 프레디케이트를 이용한 슬라이싱

- takeWhile 메서드
    - 스트림 앞에서부터 조건이 true인 요소만 포함하고, false가 반환 되는 순간부터 제외
        
        ```java
        List<Dish> slicedMenu1 = menu.stream()
                                     .takeWhile(dish -> dish.getCalories() < 320)
                                     .collect(toList());
        ```
        
- dropWhile 메서드
    - 스트림 앞에서부터 조건이 true인 요소는 무시하고, false가 반환 되는 지점부터 나머지 요소 포함
        
        ```java
        List<Dish> slicedMenu2 = menu.stream()
                                     .dropWhile(dish -> dish.getCalories() < 320)
                                     .collect(toList());
        ```
        

### 5.2.2 스트림 축소

- limit 메서드
    
    ```java
    List<Dish> dishes = menu.stream()
                            .filter(dish -> dish.getCalories() > 300)
                            .limit(3)
                            .collect(toList());
    ```
    

### 5.2.3 요소 건너뛰기

- skip 메서드
    - 처음 n개의 요소를 제외한 스트림 반환
    - limit 메서드와 상호 보적
        
        ```java
        List<Dish> dishes = menu.stream()
                                .filter(d -> d.getCalories() > 300)
                                .skip(2)
                                .collect(toList());
        ```

## 5.3 매핑

- 매핑이란?
    - 원본 데이터를 원하는 형태로 재배열하거나 변환하는 과정
        - 스트림 객체에서 특정 필드를 추출하는 것 → 선택
        - 다른 타입 객체로 바꾸는 것

### 5.3.1 스트림의 각 요소에 함수 적용하기

- map 메서드
    - 함수를 인수로 받는다.
    - 스트림에 제공되는 각 요소에 함수가 적용된 새로운 요소로 매핑된다.
        - 기존의 값을 고치는 게 아닌 변환에 가까운 매핑
    - 예제
        
        ```java
        List<String> dishNames = menu.stream()
                                     .map(Dish::getName)
                                     .collect(toList());
        ```
        
        ```java
        List<String> words = Arrays.asList("Modern", "Java", "In", "Action");
        List<Integer> wordLengths = words.stream()
                                         .map(String::length)
                                         .collect(toList());
        ```
        
        - "Modern", "Java", "In", "Action" 각 요소에 String::length를 적용해서 각 요소의 계산 결과를 매핑해서 리스트로 반환
        
        ```java
        List<Integer> dishNameLengths = menu.stream()
                                            .map(Dish::getName)
                                            .map(String::length)
                                            .collect(toList());
        ```
