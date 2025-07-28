# Chapter 4. 스트림 소개

- 컬렉션(collection)이란?
    - 데이터를 관리하고 처리하기 위한 다양한 자료구조와 이들을 구현한 클래스들의 집합
    - 주요 특징
        - 객체(데이터)를 모아 그룹화한 자료구조
        - 모든 컬렉션은 객체만 저장 가능, 기본형은 Wrapper 클래스를 통해 저장
        - 컬렉션 프레임워크에 각종 자료구조들과 이를 처리하는 다양한 기능 포함
        - 거의 모든 자바 애플리케이션이 컬렉션 사용
- 많은 요소를 포함하는 커다란 컬렉션 즉, 대용량 데이터 집합은 어떻게 처리해야 효율적일까?
    - 대용량 데이터 집합은 성능을 높이려면 병렬 처리가 답이다.
        - 병렬 처리 코드는 복잡하다.
        - 복잡한 코드는 디버깅도 어렵다.
    - SQL 쿼리처럼 선언형으로 다루는 방법은 없나?
        - SQL 쿼리는 어떤 데이터를 선택할지 요구만 표현할 뿐,
        - 어떻게 필터링하고, 어떤 조건인지, 어떻게 반복하고 어떻게 누적하는지 등 세부 구현에 대해 신경 쓸 필요가 없다.
    - 답은 스트림이다.
        - 복잡한 병렬 처리 코드를 SQL 쿼리처럼 선언형으로 컬렉션 데이터 처리가 가능하다.

## 4.1 스트림이란 무엇인가?

- 데이터 컬렉션 반복을 멋지게 처리하는 기능
- 복잡한 멀티스테드 코드를 구현하지 않아도 데이터를 투명하게 병렬로 처리 가능
- 예제
    - 요구 사항
        - 저칼로리 요리의 이름을 반환하고, 칼로리를 기준으로 요리를 정렬한다.
            - 자바 8 이전
                
                ```java
                List<Dish> lowCaloricDishes = new ArrayList<>();
                for (Dish dish : menu) {
                    if (dish.getCalories() < 400) {
                        lowCaloricDishes.add(dish);
                    }
                }
                
                Collections.sort(lowCaloricDishes, new Comparator<Dish>() {
                    public int compare(Dish dish1, Dish dish2) {
                        return Integer.compare(dish1.getCalories(), dish2.getCarlories());
                    }
                });
                
                List<String> lowCaloricDishesName = new ArrayList<>();
                for (Dish dish : lowCaloricDishes) {
                    lowCaloricDishesName.add(dish.getName());
                }
                ```
                
            - 스트림 사용
                
                ```java
                import static java.util.Comparator.comparing;
                import static java.util.stream.Collectors.toList;
                
                List<String> lowCaloricDishesName = 
                            menu.stream()
                                .filter(d -> d.getCalories() < 400)
                                .sorted(comparing(Dish::getCalories))
                                .map(Dish::getName)
                                .collect(toList());
                ```
                
                - parallelStream() 사용하면 멀티코어 아키텍처에서 병렬로 실행 가능
