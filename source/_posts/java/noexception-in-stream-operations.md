---
title: Java Exception Handle in Stream Operations
date: 2017-09-09 15:23:30
tags:
  - java
  - java 8
  - exception
categories:
  - Java
  - Java8
---

java8 스트림에서 Exception이 발생하는 경우 어떻게 처리해야 할까?  
우선 기본적인 내용부터 간단히 알아보고 넘어가 보자.  

### Java Exception
![Exception Hierarchy in java](/images/exceptionhierarchy.png "")  
Java에서 **Exception**은 **Checked Exception**과 RuntimeException을 상속받아 구현된 **Unchecked Exception**으로 구분된다.  
RuntimeException은 자동으로 이전 스택으로 throw되기 때분에 별도의 처리가 없어도 된다. 하지만 `Checked Exception 의 경우에는 throws를 명시하거나 try-catch로 처리해 주어야 한다.`  
자세한 내용은 [Java Tutorials](https://docs.oracle.com/javase/tutorial/essential/exceptions/) 를 참고하기 바란다.  

### Java Stream Operations
java8에서 Collection의 내부반복을 위해 [Stream API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html) 를 지원하고 다양한 operation을 제공하고 있다.  
대부분의 operation 들은 [Predicate](https://docs.oracle.com/javase/8/docs/api/java/util/function/Predicate.html), [Supplier](https://docs.oracle.com/javase/8/docs/api/java/util/function/Supplier.html), [Function](https://docs.oracle.com/javase/8/docs/api/java/util/function/Function.html)등을 받아서 처리를 한다.  
하지만 **java8에서 기본적으로 제공하고 있는 FunctionalInterface은 아래 메소드 시그니처와 같이 Exception을 throws 하지 않는다**. 내부에서 Checked Exception이 발생한다면 처리할 방법이 없기 때문에 compile error를 내버린다.  
```java
@FunctionalInterface
public interface Predicate<T> {
    boolean test(T t)
}
@FunctionalInterface
public interface Supplier<T> {
    T	get()
}
@FunctionalInterface
public interface Function<T,R> {
    R apply(T t);
}
```

하지만, 대부분의 애플리케이션은 예외가 발생했을때 별도의 처리로직을 가져야 하는 경우가 있다.  
예를 들면, Exception이 발생한 경우 별도로 정의한 Custom Exception으로 감싸서 던진다.  
```java
try {
    doSomeThing();
} catch(IllegalArgumentException e) {
    throw new MyException("ERROR_CODE", "ERROR_MESSAGE", e);
}
```

## Steam내에서 Exception 처리
그렇다면 Stream operation에서 Exception이 발생한다면 어떻게 처리 할 수 있을까?  
DZone에 [noException in Stream Operations](https://dzone.com/articles/noexception-in-stream-operations)에서 해답을 얻을 수 있었다.  
간단한 예제와 같이 알아보자.  

아래와 같이 내부적으로 Chedcked Exception인 UnknownHostException 을 발생하는 메소드가 있고, 이를 Stream내에서 사용하는 경우를 생각해 보자.  
```java
public class InetAddress {
    public static String getByName(String host) throws UnknownHostException {
        if(1==1) throw new UnknownHostException(host);
        return "localhost";
    }
}
```

### Step 1
아래와 같이 Stream map에서 위 메소드를 호출하려고 하면 Function<T,R> 은 Exception을 throw 하지 않기 때문에 compile error가 발생한다.  
```java
public static void main(String[] args) {
    String[] allowed = {"127.0.0.1", "::1"};

    Arrays.stream(allowed)
            .map(InetAddress::getByName)   // <--- compile error
            .collect(toSet());             // Unhandled exception: java.net.UnknownHostException
}
```
>  Stream.map signature
  `<R> Stream<R>	map(Function<? super T,? extends R> mapper)`

### Step 2
이 문제를 제일 단순하게 해결하는 방법은 **try-catch**로 감싸서 RuntimeException으로 감싸서 throw 해주는 것일 것이다.  
```java
public static void main(String[] args) {
    String[] allowed = {"127.0.0.1", "::1"};

    Arrays.stream(allowed)
            .map( host -> {
                try {
                    return InetAddress.getByName(host);
                } catch(UnknownHostException e) {
                    throw new RuntimeException(e);
                }
            })
            .collect(toSet());
}
```

하지만, 코드가 너무 복잡해 보이고 의도가 불명확해 지고, Stream API를 통해 코드를 서술적으로 작성하고자했던 목적에서 멀어지는 것 같다.  

### Step 3
우선 Function이 예외를 던져주지 않기 때문에 예외를 던져주는 **Supplier**를 정의해 준다.  
```java
public interface ExceptionSupplier<T> {
    T get() throws Exception;
}
```

try-catch 블럭을 대신 처리해 줄 메소드를 다음과 같이 정의한다.  
```java
public static <T> T wrap(ExceptionSupplier<T> z) {
    try {
        return z.get();
    } catch (Exception e) {
        throw new RuntimeException(e);
    }
}
```

ExceptionSupplier의 descriptor는 () -> T 이기 때문에
wrap 함수에 `()->InetAddress.getByName(s)` 로 넘겨 주면된다.  
```java
public static void main(String[] args) {
    String[] allowed = {"127.0.0.1", "::1"};

    Arrays.stream(allowed)
            .map(s -> wrap(()->InetAddress.getByName(s)))
            .collect(toSet());
}
```

이 정도로도 충분히 예외처리 관련 로직을 분리해 내서 좋은 코드지만, 좀더 단순하게 작성하고 싶다. 메서드 레퍼런스도 그대로 사용될 수 있었으면 좋겠다.  

### Step 4
메서드 레퍼런스를 그대로 받아서 처리하기 위해서는 Supplier를 Function으로 변경해 주어야 한다.  
InetAddress.getByName(s) 는  String을 입력받아 String을 리턴해 주기 때문에 `T -> R` 로 표현 할 수 있다.   
```java
public interface ExceptionFunction<T, R> {
    R apply(T r) throws Exception;
}
```

ExceptionSupplier를 입력받아서 값을 리턴해주던 wrap 함수도 ExceptionFunction을 입력받아 try-catch를 처리한 Function을 리턴하도록 변경 할 수있다.  
```java
public static <T, R> Function<T, R> wrap(ExceptionFunction<T, R> f) {
  // (r) ->
  //     wrap(
  //           ()->InetAddress.getByName(r)
  //     )
    return (T r) -> {
        try {
            return f.apply(r);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    };
}
```

최종적으로는 아래와 같이 exception 처리를 분리해내면서 함수형 스타일로 작성할 수 있다.  
```java
public static void main(String[] args) {
    String[] allowed = {"127.0.0.1", "::1"};

    Arrays.stream(allowed)
            .map(wrap(InetAddress::getByName))
            .collect(toSet());
}
```

Good!!
