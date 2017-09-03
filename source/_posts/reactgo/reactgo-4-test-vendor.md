---
title: golang test code.. vendor 이녀석!!
date: 2017-04-03 15:13:00
tags:
  - 개발일지
  - golang
categories:
  - ReactGo
  - golang
thumbnail:
---

현재 있는 개발 팀의 핵심 프랙티스 중의 하나가 TDD 이다.  
Test first를 하는 것은 아니지만, Test Code 작성을 매우 강조하고 있다.  
이와 관련해서는 [TDD(android unit test)](http://kihoonkim.github.io/2017/03/11/Agile/TDD/) 에서 설명하고 있으니 참고하면 될 것 같다.  
TDD를 해야 하기 때문에, 새로운 언어나 프레임워크를 사용할 때 제일 먼저 스파이킹 하는 것이 "Test Code를 어떻게 작성하면 되는 가.." 이다.  

Java로 작성할 때는 기본적으로 junit을 사용하여 유닛테스트를 작성했다.  
Golang은 별도의 라이브러리나 프레임워크 도움없이 `go test` 만으로도 충분히 테스트 할 수 있는 것 같다.  
심지어 Test Coverage 까지 측정해 준다.  

간단한 코드로 테스트를 시작해 보았다.  
테스트 코드는 **테스트하고자하는 파일명 + _test.go** 로 작성하면 된다.  
테스트 파일은 테스트 대상파일과 **같은 패키지** 에 넣으면 된다.  
>  [Package testing > Overview](https://golang.org/pkg/testing/)
  To write a new test suite, create a file whose name ends  **\_test.go** that contains the **TestXxx functions** as described here. Put the file in **the same package** as the one being tested. The file will be excluded from regular package builds but will be included when the “go test” command is run.  

테스트는 기본적으로 파일단위가 아니라 **패키지단위로 실행** 되는 것 같다.  
```Go
// ../gogo/calc/calculator.go
package calc

func Sum(a int, b int) int {
	return a + b
}
```
```Go
// ../gogo/calc/calculator_test.go
package calc

import "testing"

func TestSum(t *testing.T)  {
	result := Sum(1, 2)
	if result != 3 {
		t.Errorf("expected:%s actual:%s", 3, result)
	}
}
```
![](/images/gotest_clac.PNG)
내가 만든 `calc` 패키지를 테스트 하기위해서 `go test calc`라고 입력 하고 실행 했더니 아래와 같이 오류가 났다.  
```
C:\Users\kihoonkim\goworkspace\src\github.com\kihoonkim\gogo>  go test -v calc
can't load package: package calc: cannot find package "calc" in any of:
        C:\Go\src\calc (from $GOROOT)
        C:\Users\kihoonkim\goworkspace\src\calc (from $GOPATH)
```

**패키지 이름만 입력된 경우 `$GOROOT` 와 `$GOPATH` 에서 해당 패키지를 찾는 것 같다.**  
따라서 아래와 같이 **상대 경로(./xxx)** 를 통해서 해당 패키지를 실행 해야 한다.  
```
~gogo>  go test -v ./calc
=== RUN   TestSum
--- PASS: TestSum (0.00s)
PASS
ok      github.com/kihoonkim/gogo/calc  0.033s
```

패키지명 없이 `go test` 만 실행하는 경우 현재 디렉토리에 있는 패키지를 찾아 테스트를 실행한다.  
```
~gogo\calc> go test -v
=== RUN   TestSum
--- PASS: TestSum (0.00s)
PASS
ok      github.com/kihoonkim/gogo/calc  0.035s
```

여러 패키지를 한번에 실행시키고 싶으면 일일히 열거해 주거나 `...` 을 사용하면 된다.  
`...` 은 하위의 모든 패키지를 의미한다.  
```
~gogo>  go test -v ./calc ./utils
```
```
~gogo>  go test -v ./...
```

그런데, `...` 을 이용해서 테스트 하는 경우 문제가 있다.  
package manager를 통해 생성된 **vendor** 하위에 있는 패키지들까지 인식되면서 오류가 난다.  
```
~gogo>  go test -v ./...
vendor\github.com\jinzhu\gorm\model_struct.go:12:2: cannot find package "github.com/jinzhu/inflection" in
.........
```

안타깝게도 특정 패키지만 exclude 시키는 옵션은 없는 것 같다.  
linux나 Mac 이라면 아래와 같이 실행 시킬 수 는 있다.  
```bash
~gogo $  go list ./... | grep -v vendor | xargs -n1 go test -v
```
package manager로 glide를 사용하고 있다면 더 쉬운 방법이 있다.  
```bash
~gogo $  go test -cover $(glide novendor)
```

winodw는 아래 처럼 하면 되긴 하지만, 더 좋은 방법을 찾지는 못했다..ㅠ  
```
~gogo>  go list ./... | findstr /V "vendor" > pkgs.txt
~gogo>  for /F %p in (pkgs.txt) DO go test %p
```

java 개발시 테스트 코드는 별도의 test 디렉토리를 만들고 그 안에 동일한 패키지 구조로 코드를 만든다.  
golang 도 처음에는 습관적으로 이런 구조로 디렉토리 구조를 생성했었다.  
![](/images/gotest_directory.PNG)
이 경우에 test 디렉토리 아래에 있는 패키지만 실행 시키면 되기 때문에 OS에 종속적인 커맨드 없이 `go test` 만으로 실행 시킬 수 있는 장점이 있기는 하다.  
```
~gogo>  go test -v ./test/...
testing: warning: no tests to run                                                                                    PASS
ok      github.com/kihoonkim/gogo/test  0.034s [no tests to run]                                                     === RUN   TestSum
--- PASS: TestSum (0.00s)
PASS
ok      github.com/kihoonkim/gogo/test/calc     0.037s
=== RUN   TestUtils
--- PASS: TestUtils (0.00s)
PASS
ok      github.com/kihoonkim/gogo/test/utils    0.043s
```

**하지만!!!
Coverage 측정을 해야하는 경우, 반드시 같은 디렉토리에 넣어야 한다.**  

* test 디렉토리에 넣은 경우 커버리지 측정 안됨

```
~gogo>  go test -v -cover ./test/...
testing: warning: no tests to run
PASS
coverage: 0.0% of statements
ok      github.com/kihoonkim/gogo/test  0.033s  coverage: 0.0% of statements [no tests to run]
=== RUN   TestSum
--- PASS: TestSum (0.00s)
PASS
coverage: 0.0% of statements
ok      github.com/kihoonkim/gogo/test/calc     0.042s  coverage: 0.0% of statements
=== RUN   TestUtils
--- PASS: TestUtils (0.00s)
PASS
coverage: 0.0% of statements
ok      github.com/kihoonkim/gogo/test/utils    0.039s  coverage: 0.0% of statements
```

* 같은 디렉토리에 넣은 경우 커버리지 측정 됨

```
~gogo>  go test -v -cover ./calc ./utils
=== RUN   TestSum
--- PASS: TestSum (0.00s)
PASS
coverage: 100.0% of statements
ok      github.com/kihoonkim/gogo/calc  0.039s  coverage: 100.0% of statements
=== RUN   TestUtils
--- PASS: TestUtils (0.00s)
PASS
coverage: 100.0% of statements
ok      github.com/kihoonkim/gogo/utils 0.032s  coverage: 100.0% of statements
```

서두에 언급한 것같이 같은 디렉토리에 테스트파일을 함께 넣는 것을 표준처럼 가이드를 하고 있는데, 이런경우 대상파일의 private 한 func 까지 접근이 가능해 진다.  
TDD 를 하는 경우 black box 테스트를 하듯이 테스트 대상의 API를 정의해 나가야 한다. 즉, public 한 녀석들만 접근하고 테스트 하는 것이 좋다고 생각한다.  
하지만 항상 정답은 없는 것 같다. 각자 좋다고 생각하는 방식으로 구조를 잡으면 될 것 같다.  

암튼 우리는 Test Coverage를 측정해야 하기 때문에 같은 디렉토리 내에 테스트코드를 넣기로 했다.  


### Test Coverage & Report
golang 에서 테스트 커버리지를 측정하고 html로 리포트를 보는 방법은 간단하다.  
```
~gogo> go test -coverprofile=coverage.out
~gogo> go tool cover -html=coverage.out
```
하지만.. vendor 라는 녀석이 항상 문제다...
```
~gogo>  go test -coverprofile=coverage.out ./calc ./utils
cannot use test profile flag with multiple packages
```
shell script로 처리하려면 [여기](https://www.ory.am/blog/2016/10/24/get-accurate-code-coverage-in-golang-go)를 참조해 보자.  

어려우면 gocov 를 받아서 실행해보자.  
```
> go get github.com/axw/gocov/gocov
> go get -u gopkg.in/matm/v1/gocov-html
```

```
# windows
~gogo> gocov test ./calc .utils | gocov-html > coverage.html

# linux
$ gocov test $(glide novendor) | gocov-html > coverage.html
```
![](/images/gocov_html.PNG)
