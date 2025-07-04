# Chpater 3.

# 람다 표현식

## 3.1 람다란 무엇인가?

- 특징
    - 익명
        - 이름이 없는 메서드
    - 함수
        - 특정 클래스에 종속되지 않음
        - 메서드와 같이 파라미터, 바디, 반환 형식, 예외 리스트 포함
    - 전달
        - 메서드 인수로 전달 또는 변수로 저장 가능
    - 간결성
        - 익명 클래스에 비해 훨씬 간결
- 용어(lambda)
    - 미적분학 학계에서 개발한 시스템에서 유래
- 람다는 왜 이용하는가?
    - 어떤 메서드에 인자로 동작을 외부에서 전달할 때(동작 파라미터화), 간결하게 구현 가능해서
- 구성
    
    ```java
    inventory.sort((Apple a1, Appple a2) -> a1.getWiehgt().compareTo(a2.getWeight()));
    ```
    
    - 파라미터 리스트, 화살표, 람다 바디(실행문)
- 자바 8의 대표적인 람다 표현식
  
  <img src="./images/table3-1.png" alt="image" width="700">
    
## 3.2 어디에, 어떻게 람다를 사용할까?

- 메서드의 인자로 외부에서 주입되는 동작(함수)이 함수형 인터페이스 타입이면, 동작을 람다로 표현 가능하다.
    - 람다는 함수형 인터페이스가 갖고 있는 단일 추상 메서드의 구현식

### 3.2.1 함수형 인터페이스

- Prdicate<T>
    
    ```java
    public interface Predicate<T> {
        boolean test(T t);
    }
    ```
    
    - 오직 하나의 추상 메서드를 갖는다.
    - 정확히 하나의 추상 메서드를 지정하는 인터페이스다.
        
        ```java
        // java.util.Comparator
        public interface Comparator<T> {
            int compare(T o1, T o2);
        }
        
        // java.lang.Runnable
        public interface Runnable {
            void run();
        }
        
        // java.awt.event.ActionListener
        public interface ActionListener extends EventListener {
            void actionPerformed(ActionEvent e);
        }
        
        // java.util.concurrent.Callable
        public interface Callable<V> {
            V call() throws Exception;
        }
        
        // java.security.PrivilegedAction
        public interface PrivilegedAction<T> {
            T run();
        }
        ```
        
        > 인터페이스는 디폴트 메서드를 포함할 수 있다. 많은 디폴트 메서드가 있어도 추상 메서드가 하나면 함수형 인터페이스다.
        > 
- 함수형 인터페이스의 추상 메서드 구현을 람다로 구현 후 다른 메서드의 인자(동작)로 전달 가능하므로, 람다 자체를 함수형 인터페이스의 인스턴스라 할 수 있다.
    
    ```java
    함수형 인터페이스 {
        추상 메서드
    }
    
    어떤 메서드((입력 값) -> {원하는 동작})
    ```
    
### 3.2.2 함수 디스크립터

- 함수형 인터페이스의 추상 메서드의 시그니처 ⇒ 람다 표현식의 시그니처
- 시그니처가 일치한다?
    - 함수형 인터페이스의 추상 메서드의 인수와 반환값이 람다의 인수와 반환값과 일치한다.
    - 함수형 인터페이스를 인수로 받는 메서드에만 람다 표현식 사용이 가능하다.
        
        ```java
        public void process(Runnable r) {
            r.run();
        }
        
        process(() -> System.out.println("This is awesome!!"));
        ```
        
        - Runnable의 추상 메서드 run()은 인수와 반환 타입이 없다.
        - () 인수 없음 → System.out.println() void 반환

## 3.3 람다 활용 : 실행 어라운드 패턴

- 람다와 동작 파라미터의 실용 예제
    - 실행 어라운드 패턴
        - 자원 열기(설정) - 실제 작업(처리) - 닫기(정리)
            
            ```java
            public String processFile() throws IOException {
                try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
                    return br.readLine();
                }
            }
            ```
            
            - 설정 : BufferedReader로 읽을 파일을 설정
            - 실행 : 설정된 파일을 한 줄 씩 읽음
            - 정리 : try-with-resource 구문으로 작업 정리

### 3.3.1 1단계 : 동작 파라미터화를 기억하라

- 현재 정의된 processFile()은 한 줄만 읽기 가능
- 한 번에 두 줄을 읽거나 가장 자주 사용되는 단어를 반환하고 싶다면?

### 3.3.2 2단계 : 함수형 인터페이스를 이용해서 동작 전달

- 함수형 인터페이스 자리에 람다를 사용할 수 있다.
    
    ⇒ BufferedReader를 받아서 String을 반환하고, IOException을 던질 수 있는 시그니처와 일치하는 함수형 인터페이스가 필요하다.
    
    ```java
    @FunctionalInterface
    public interface BufferedReaderProcessor {
        String process(BufferedReader b) throws IOException;
    }
    
    // BufferedReaderProcessor를 processFile() 메서드의 인자로 전달
    public String processFile(BufferedReaderProcessor p) throws IOException {
    	...
    }
    ```
    
### 3.3.3 3단계 : 동작 실행

- 이제 함수형 인터페이스 BufferedReaderProcessor에 정의된 process() 메서드의 시그니처(BufferedReader → String)와 일치하는 람다를 전달할 수 있다.
- processFile 바디 내에서 BufferedReaderProcessor 객체의 process 호출 가능
    
    ```java
    public String processFile(BufferedReaderProcessor p) throws IOException {
        try (BufferedReader br = new BufferedRader(new FileReader("dadta.txt"))) {
            return p.process(br); // BufferedReader 객체 처리								
        }
    }
    ```
    
### 3.3.4 4단계 : 람다 전달

```java
String oneLine = processFile((BufferedReader br) -> br.readLine());
String twoLines = processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

## 3.4 함수형 인터페이스 사용

- 오직 하나의 추상 메서드
- 람다 표현식 → 추상 메서드의 구현식
    - 시그니처가 일치해야 한다.
    - 매개변수 타입과 개수, 반환 타입일 일치해야 한다.
- 함수형 인터페이스의 추상 메서드 시그니처를 함수 디스크립터라고 한다.
- 함수형 프로그래밍 지원을 위해 java.util.function 패키지에 다양한 함수형 인터페이스 추가
    - Predicate, Consumer, Function 등

### 3.4.1 Predicate

- 패키지 : java.util.function.Predicate<T>
- 추상 메서드 : test()
- 시그니처 : T 타입의 인자를 받아 Boolean 타입 반환
    
    ```java
    @FunctionalInterface
    public interface Predicate<T> {
        boolean test(T t);
    }
    
    public <T> List<T> filter(List<T> list, Predicate<T> p) {
        List<T> results = new ArrayList<>();
        for (T t : list) {
            if (p.test(t)) {
                results.add(t);
            }
        }
            
        return results;
    }
    
    Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();
    List<String> nonEmpty = filter(listOfStrings, nonEmptyStringPredicate);
    ```
    
### 3.4.2 Consumer

- 패키지 : java.util.function.Consumer<T>
- 추상 메서드 : accept()
- 시그니처 : T 타입의 인자를 받아 void 반환
    
    ```java
    @FunctionalInterface
    public interface Consumer<T> {
        void accept(T t);
    }
    
    public <T> void forEach(List<T> list, Consumer<T> c) {
        for (T t : list) {
            c.accept(t);
        }
    }
    
    // 람다가 Consumer의 accept() 구현
    forEach(Arrays.asList(1, 2, 3, 4, 5), (Integer i) -> System.out.println(i));
    ```

### 3.4.3 Function

- 패키지 : java.util.function.Function<T, R>
- 추상 메서드 : apply()
- 시그니처 : T 타입의 인자를 받아서 R 객체를 반환
    - 입력을 출력으로 매핑
        
        ```java
        @FunctionalInterface
        public interface Function<T, R> {
            R apply(T t);
        }
        
        public <T, R> List<R> map(List<T> list, Function<T, R> f) {
            List<R> result = new ArrayList<>();
            for (T t : list) {
                result.add(f.apply(t));
            }
            
            return result;
        }
        
        // [7, 2, 6]
        List<Integer> l = map(
            Arrays.asList("lambda", "in", "action"), (String s) -> s.length();
        )
        ```
        

### 기본형 특화

- 자바의 모든 형식은 참조형 또는 기본형
- 제네릭 파라미터에는 참조형만 사용 가능
    - 박싱 : 기본형 → 참조형
    - 언박싱 : 참조형 → 기본형
    - 오토박싱
        
        ```java
        // int -> Integer
        List<Integer> list = new ArrayList<>();
        for (int i = 300; i < 400; i++) {
            list.add(i);
        }
        ```
        
        - 박싱하면 기본형이 참조형으로 감싸지고 힙에 저장된다.      
            → 메모리 탐색 → 비용 up
          
      - Function 인터페이스는 다양한 출력 형식 파라미터를 제공한다.
        <img src="./images/table3-2.png" alt="image" width="700">
