---
title: (Java Basic) Generics
date: 2017-09-03 15:23:30
tags:
  - java
  - generic
categories:
  - Java
  - Basic
---

# Java Generic
![fig](/images/cup_generic.jpg "Cup<Coffee>")  

- [Java Documentation](https://docs.oracle.com/javase/tutorial/java/generics/index.html)
- [토비의 봄 TV Generics](https://youtu.be/ipT2XG1SHtQ)

## Generic을 왜 사용하는가?
- Stronger type checks at compile time.
- Elimination of casts.

## Generic Class
  - Type parameter & argument
  ```java
  public class Generics<T> { // type parameter
      void print(T t) { ... }
      public static void main(String[] args) {
        new Generics<String>().print(""); // type argument
      }
  }
  ```
  - static method에 type parameter 사용 불가
  ```java
  public class Generics<T> {
      static void hello(T t) {} // compile error
  }
  ```
  - Type parameter convention
  ```
  T - Type
  E - Element
  K - Key
  V - Value
  N - Number
  S, U... - 2nd, 3rd, 4th types
  ```

## Generic Method
- Method 안에서만 사용할 타입 지정
```java
public class Generics<E> {
  <T> Generics(T t) {}
  <T> void method1(T t) { ... }
  <S, E> E method2(S t) { ... }
  <S, T> S method3(T t) { ... }
  static <T> void method4(T t) { ... }
}
```

## Type Eraser
https://docs.oracle.com/javase/tutorial/java/generics/erasure.html
- If type parameter **T is unbounded**, the Java compiler replaces it with **Object**  
```java
public class Node<T> {
    private T data;
    public T getData() { return data; }
}
public class Node {
    private Object data;
    public Object getData() { return data; }
}
```
- The Java compiler replaces the **bounded type parameter T** with the **first bound class**
```java
public class Node<T extends Comparable<T>> {
  private T data;
  public T getData() { return data; }
}
public class Node {
  private Comparable data;
  public Comparable getData() { return data; }
}
```

## Raw type
Generic이 없는 기본 타입
```java
public static void main(Sting[] args) {
  List rawTypeList = new ArrayList<String>();

  List<Integer> list1 = Arrays.asList(1,2,3);
  List rawInt = list;           // not compile error
  List<Integer> list2 = rawInt; // warning : unchecked or unsafe operation (하위 호환을 위해서)
  List<String> strs = rawInt;   // not compile error
  String str = strs.get(0) ;    // ClassCastException
}
```

## Generic과 타입의 상속 관계
```java
public class Generics {
  public static void main(String[] args) {
    // Number is Integer's super type.
    Integer i = 10;
    Number n = i;

    List<Integer> intList = new ArrayList<>();
    List<Number> numberList = intList; // compile error : Incompatible types.

    // List is ArrayList's super type.
    ArrayList<Integer> intList = new ArrayList<>();
    List<Integer> numberList = intList; // OK
  }
}
```

## Bounded Type parameter
- upper bound : extends
```java
// T는 List의 sub type 만 가능
// T에 상관없이 List에 있는 기능만 사용하겠음을 의미
public class Generics<T extends List> {
  <S extends List> void hello(S s) {}
  <S extends Comparable<S>> long count(S[] list, S elem) {
    return Arrays.stream(list)(s -> s.compareTo(elem) > 0).count();
  }
}
```
- lower bound : super
```java
// T는 List의 super type 만 가능
public class Generics<T super Integer> {}
new Generics<Number>();
```
- 주로..
```java
// upper bound : method 내부에서 사용되는 경우
// lower bound : method 외부에서 사용되는 경우
public class Generics {
 private static <T extends Comparable<? super T>> max(List<? extends T> list) {
   return list.stream().reduce((a,b)-> a.compareTo(b)>0 ? a:b).get();
 }
}
```


## Type 추론
compiler가 타입을 추론
```java
public class Generics {
  static <T> void method(T t) {}
  public static void main(String[] args) {
    Generics.method(1);
    Generics.<Integer>method(1);  // hint
    List<String> strs = new ArrayList<>();
    List<String> strs2 = Collections.emptyList();
    List<String> strs3 = Collections.<String>emptyList();
  }
}
```

## Wild card : ?
- ? : 타입 모름. 내부 구현에서 타입을 알필요 없음. 타입이 정해지면 그 이후 사용하겠음
```java
public class Generics {
  static void printList(List<Object> list) {
    list.forEach(System.out::println);
  }
  static void printList2(List<? extends Object> list) {
    list.forEach(System.out::println);
  }
  public static void main(String[] args) {
      List<?> list;                // List인거 이외에는 관심없음
      List<? extends Object> objs; // Object에 있는 기능만 사용

      printList(Arrays.asList(1,2,3));
      printList2(Arrays.asList(1,2,3));

      List<Integer> intList = Arrays.asList(1,2,3);
      printList(list);  // compile error
      printList2(list); // ok
  }
}
```
```java
public class Generics {
  static class A {}
  static class B extends A {}

  public static void main(String[] args) {
    List<B> listB = new ArrayList<>();
    listB.add(new B());  // ok

    List<A> listA = listB;        // complie error
    List<? extends A> lA = listB; // ok
    lA.add(new A());              // complie error
    lA.add(new B());              // complie error
    lA.add(null);                 // only ok
    List<? super B> l2 = listB;   // ok
  }
}
```

## Type parameter vs Wild card 무엇을 사용해야 할까
- Type Parameter를 사용한다는 것은 내부에서 T이 무엇인지 관심있음
- wild card를 사용하는 경우는 Type이 무엇인지 관심없음
- API 설계 의도에 따라 선택해서 사용해야 함
```java
public class Generics {
  static <T> void method1(List<T> list, T t){
  }
  static void method2(List<?> list) {
    list.add(1);  //불가능
    list.add(null); // 가능
    list.clear(); // 원소와 상관없는 기능만 사용 가능
  }
  public static void main(String[] args) {
    List<Integer> list = Arrays.asList(1,2,3,4,5);
    method1(list);
    method2(list);
  }
}
```
```java
public class Generics {
  static <T> boolean isEmpty1(List<T> list) {
    return list.size() == 0;
  }
  static boolean isEmpty2(List<?> list) {
    return list.size() == 0;
  }
  public static void main(String[] args) {
    List<Integer> list = Arrays.asList(1,2,3,4,5);
    System.out.println(isEmpty1(list));
    System.out.println(isEmpty2(list)); // API 설계의도에 더 맞음
  }
}
 ```

## wild card capture
- type을 추론해야 되는 경우 capture를 실행하게 됨
- 추론할 수 없는 경우 에러
```java
public class Generics {
  // API 구현에 오해를 부름. 내부에서 원소 타입이 중요한가???
  // static <T> void reverse(List<T> list) {
  //   List<T> temp = new ArrayList<>(list);
  //   for(int=0; i<list.size(); i++) {
  //     list.set(i, temp.get(list.size()-i-1));
  //   }
  // }

  // wild card를 사용하려고 하니 capture 불가..
  // static void reverse(List<?> list) {
  //   List<?> temp = new ArrayList<>(list);
  //   for(int=0; i<list.size(); i++) {
  //     list.set(i, temp.get(list.size()-i-1)); // compile error : capture<?>
  //   }
  // }

  static void reverse(List<?> list) {
    reverseHelper(list);
  }
  private static <T> void reverseHelper(List<T> list) {
      List<T> temp = new ArrayList<>(list);
      for(int=0; i<list.size(); i++) {
        list.set(i, temp.get(list.size()-i-1));
      }
  }
  // Raw Type 이용
  // private void reverse(List<?> list) {
  //     List temp = new ArrayList<>(list);
  //     List list2 = list;
  //     for(int=0; i<list.size(); i++) {
  //       list2.set(i, temp.get(list2.size()-i-1));
  //     }
  // }
  public static void main(String[] args) {
    List<Integer> list = Arrays.asList(1,2,3,4,5);
    reverse(list);
    System.out.println(list);
  }
}
```

## Intersection Type과 람다를 이용한 동적 기능확장
- interface & 로 연결
- class 는 한번만 사용 가능
```java
public class Generics {
  <S extends Serializable & Comparable> hello(S s) {}
}
```
- with Lambda : 정의해햐할 메소드 개수가 1개면 됨
```java
public class IntersectionType {
  public static void main(String[] args) {
    //  markable interface : Serializable
    hello( (Function & Serializable) s->s);
  }
  private static void hello(Function o) {}
  private static void hello(Serializable o) {}
  private static <T extends Function & Serializable> void hello(T o) {}
}
```
- default method를 이용하여 기능 확장가능
```java
public class IntersectionType {
  interface Hello {
    default void hello(){}
  }
  interface Hi {
    default void hi(){}
  }
  interface Printer {
    default void print(String str){}
  }
  public static void main(String[] args) {
    //  markable interface : Serializable
    hello( (Function & Hello & Hi) s->s);
    run( (Function & Hello & Hi & Printer) s->s, o -> {
      o.hello();
      o.hi();
      o.print();
    });
  }
  // 함수마다 계속 수정해야 하나??
  private static <T extends Function & Hello & Hi> void hello(T t) {
    t.hello();
    t.hi();
  }
  // callback을 이용하여 해결 가능
  private static <T extends Function> void run(T t, Cunsumer<T> consumer) {
    consumer.accept(t);
  }
}
```
- delegate를 통해 기능 조합
```java
public class IntersectionType {
  interface DelegateTo<T>{
    T delegate();
  }
  interface Hello extends DelegateTo<String> {
    default void hello() {
      System.out.println("Hello" + delegate())
    }
  }
  public static void main(String[] args) {
    run((DelegateTo<String> & Hello)()->"Kihoon", o->{
      o.hello();
    });
  }

  private static <T extends DelegateTo<S>, S> void run(T t, Consumer<T> consumer) {
    consumer.accept(t);
  }
}
```
- Library 수정 없이 기능 확장하기
```java
public class IntersectionType {
  interface Pair<T> {
    T getFirst();
    T getSecond();
    void setFirtst(T first);
    void setSecond(T second);
  }
  static class Name implements Pair<String> {...}
  interface ForwardingPair<T> extends DelegateTo<Pair<T>>, <Pair<T> {
    default T getFirst(){ return delegate().getFirst();}
    default T getSecond(){ return delegate().getSecond();}
    default void setFirtst(T first){ return delegate().setFirst(first);}
    default void setSecond(T second){ return delegate().setSecond(second);}
  }
  interface DelegateTo<T>{
    T delegate();
  }

  private static <T extends DelegateTo<S>, S> void run(T t, Consumer<T> consumer) {
    consumer.accept(t);
  }

  interface Convertable<T> extends DelegateTo<T>> {
    default void convert(Function<t,T> mapper) {
      Pair<T> pair = delegate();
      pair.setFirst(mapper.apply(pair.getFirst());
      pair.setSecond(mapper.apply(pair.getSecond());
    }
  }
  public static void main(String[] args) {
    Pair<String> name = new Name("Ki", "Kim");
    run((ForwardingPair<String> & Convertable<String>)()->name, o->{
      o.convert(s->s.toUpperCase());
    });
  }

}
```
