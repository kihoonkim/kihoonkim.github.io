---
title: Java 8 알아보기
date: 2017-01-01 16:17:39
tags:
  - FP
  - java 8
categories: Java
thumbnail: /images/java8.png
---
# Java 8 알아보기

## 덧셈 모듈을 만들어 주세요
```Java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}
```

## 뺄셈 모듈도 만들어 주세요
```Java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
    public int subtract(int a, int b) {
        return a - b;
    }
}
```

## 숫자 여러개 받을 수 있게 해주세요
```Java
public class Calculator {
    public int add(int[] arrs) {
        int result  = 0;
        for(int a : arrs) {
            result += a;
        }
        return result;
    }
    public int subtract(int[] arrs) {
        int result = 0;
        for(int a : arrs) {
            result -= a;
        }
        return result;
    }
}
```

## 0 보다 작은 수가 입력된 경우엔 무시해 주세요
```Java
public class Calculator {
    public int add(int[] arrs) {
        int result  = 0;
        for(int a : arrs) {
            if(a <= 0) continue;
            result += a;  // (1)
        }
        return result;
    }
    public int subtract(int[] arrs) {
        int result = 0;
        for(int a : arrs) {
            if(a <= 0) continue;
            result -= a;  // (2)
        }
        return result;
    }
}
```
메소드 이름 과 (1) (2) 부분 빼고는 모두 중복 코드  

## Refactoring
```Java
public class Calculator {
    private boolean isLessThanZero(int a){
        return a <= 0;
    }
    public int add(int[] arrs) {
        int result  = 0;
        for(int a : arrs) {
            if(isLessThanZero(a)) continue;
            result += a;
        }
        return result;
    }
    public int subtract(int[] arrs) {
        int result = 0;
        for(int a : arrs) {
            if(isLessThanZero(a)) continue;
            result -= a;
        }
        return result;
    }
}
```
그래도 중복은 여전하네..  
익명 클래스를 사용해서 실제 계산하는 부분만 처리하자  

## Refactoring continue..
```Java
public class Calculator {
    interface Operator {
        int operate(int a, int b);    
    }
    private boolean isGreaterThanZero(int a){
        return a <= 0;
    }
    private List<Integer> getOnlyNaturalnumbers(int[] arrs){
        List<Integer> list = new ArrayList<>();
        for(int a : arrs) {
            if(isGreaterThanZero(a)) list.add(a);
        }
        return list;
    }
    private int calcuate(List<Integer> arrs, Operator opt) {
        int result = 0;
        for(int a : arrs) {
            result = opt.operate(result, a);
        }
        return result;
    }
    public int add(int[] arrs) {
        return calcuate(getOnlyNaturalnumbers(arrs), new Operator() {
                    public int operate(int a, int b) {
                        return a + b;
                    }
                });
    }
    public int subtract(int[] arrs) {
        return calcuate(getOnlyNaturalnumbers(arrs), new Operator() {
                    public int operate(int a, int b) {
                        return a - b;
                    }
                });
    }
}
```

## 곱셈, 나눗셈이 추가되더라도..
```Java
...
public int multiply(int[] arrs) {
    return calcuate(getOnlyNaturalnumbers(arrs), new Operator() {
                public int operate(int a, int b) {
                    return a * b;
                }
            });
}
public int divide(int[] arrs) {
    return calcuate(getOnlyNaturalnumbers(arrs), new Operator() {
        public int operate(int a, int b) {
            return a / b;
        }
    });
}
...
```

## 이름을 좀 바꿔 볼까요
```Java
public class Calculator {
    interface Function {
        int apply(int a, int b);
    }
    private boolean isGreaterThanZero(int a){
        return a <= 0;
    }
    private List<Integer> filter(int[] arrs){
        List<Integer> list = new ArrayList<>();
        for(int a : arrs) {
            if(isGreaterThanZero(a)) list.add(a);
        }
        return list;
    }
    private int calcuate(List<Integer> arrs, Function opt) {
        int result = 0;
        for(int a : arrs) {
            result = opt.apply(result, a);
        }
        return result;
    }
    public int add(int[] arrs) {
        return calcuate(filter(arrs), new Function() {
                    public int apply(int a, int b) {
                        return a + b;
                    }
                });
    }
    public int subtract(int[] arrs) {
        return calcuate(filter(arrs), new Function() {
                    public int apply(int a, int b) {
                        return a - b;
                    }
                });
    }
}
```

## Java8 Stream API와 Lambda 로 바꿔 볼까요
```Java
public class Calculator {
    private Predicate<Integer> ltzero = a -> a <= 0;
    public int add(List<Integer> arrs) {
        return arrs.stream().filter(ltzero).reduce((a, b) -> a + b).get();
    }
    public int subtract(List<Integer> arrs) {
        return arrs.stream().filter(ltzero).reduce((a, b) -> a - b).get();
    }
    public int multiply(List<Integer> arrs) {
        return arrs.stream().filter(ltzero).reduce((a, b) -> a * b).get();
    }
    public int divide(List<Integer> arrs) {
        return arrs.stream().filter(ltzero).reduce((a, b) -> a / b).get();
    }
}
```

Java8 에 대한 자세한 내용은..  
http://www.slideshare.net/gyumee/java-8-lambda-35352385  
http://www.slideshare.net/gyumee/8-37599530  
