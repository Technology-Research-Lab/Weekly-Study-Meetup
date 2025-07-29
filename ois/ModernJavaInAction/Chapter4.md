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
 
⛔ ### 사용 예제
```java
    List<Dish> menu = Arrays.asList(
        new Dish("pork", false, 800, Dish.Type.MEAT),
        new Dish("beef", false, 700, Dish.Type.MEAT), 
        new Dish("chicken", false, 400, Dish.Type.MEAT), 
        new Dish("french fries", true, 530, Dish.Type.OTHER), 
        new Dish("rice", true, 350, Dish.Type.OTHER), 
        new Dish("season fruit", true, 120, Dish.Type.OTHER), 
        new Dish("pizza", true, 550, Dish.Type.OTHER), 
        new Dish("prawns", false, 300, Dish.Type.FISH), 
        new Dish("salmon", false, 450, Dish.Type.FISH)
    );
    
    public class Dish { private final String name;
        private final boolean vegetarian;
        private final int calories;
        private final Type type;
        public Dish(String name, 
            
        boolean vegetarian, int calories, Type type) {
            this.name = name;
            this.vegetarian = vegetarian;
            this.calories = calories;
            this.type = type;
        }
        
        public String getName() { return name; }
        public boolean isVegetarian() { return vegetarian; }
        public int getCalories() { return calories; }
        public Type getType() { return type; }
        
        @Override
        public String toString() { return name; }
        public enum Type { MEAT, FISH, OTHER }
    }
```

## 4.2 스트림 시작하기

- 스트림의 정의
    - 데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소(Sequence of delements)
        - 연속된 요소
            - 컬렉션과 같이 스트림은 특정 요소 형식으로 이루어진 연소된 값의 집합을 제공
            - 컬렉션의 주제는 데이터, 스트림의 주제는 계산이다.
                - 컬렉션은 요소 저장 및 접근 연산이 주를 이룬다.
                - 스트림은 계산식이 주를 이룬다.
        - 소스
            - 컬렉션, 배열, 파일 등 다양한 소스에서 생성 가능하다.
        - 데이터 처리 연산
            - filter, map, reduce 같은 함수형 연산으로 데이터를 조작할 수 있다.
- 스트림의 특징
    - 파이프라이닝
        - 스트림 연산끼리 연결해서 파이프라인을 구성할 수 있다.
        - laziness, short circuiring
    - 내부 반복
        - 반복자를 이용해서 명시적으로 반복하는 컬렉션과 달리 스트림은 내부 반복을 지원한다.
- 예제로 확인
    
    ```java
    List<String> threeHighCaloricDishNames = 
        menu.stream()
            .filter(dish -> dish.getCalories() > 300)
            .map(Dish::getName) 
            .limit(3) 
            .collect(toList());
    System.out.println(threeHighCaloricDishNames);
    ```
    
    - filter 메서드로 300 칼로리 이상의 dish 객체들만 걸러냄
    - 걸러진 dish 객체들을 이름을 뽑아서 String으로 변환 → 요리명 추출
    - 앞의 3가지만 선택
    - 결과를 리스트로 저장

> 요리 리스트를 포함하는 menu에 stream 메서드를 호출해서 스트림을 얻었다. 여기서 **데이터 소스**는 요리 리스트(메뉴)다. 데이터 소스는 **연속된 요소**를 스트림에 제공한다. 다음으로 스트림에 filter, map, limit, collect로 이어지는 일련의 **데이터 처리 연산**을 적용한다. collect를 제외한 모든 연산은 **파이프라인**을 형성할 수 있도록 스트림을 반환한다.
>
