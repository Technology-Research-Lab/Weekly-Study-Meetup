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
