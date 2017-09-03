---
title: golang configuration file 삽질기
date: 2017-04-05 23:38:00
tags:
  - 개발일지
  - golang
categories:
  - ReactGo
  - golang
thumbnail:
---

개발을 하다보면 local, development, production 등의 환경이 다르다. 예를 들면 데이터베이스 접속 정보가 있을 것 이다.  
소스코드에 하드코딩 되어 있다면, 데이터베이스 인스턴스의 IP, Port등이 변경되는 경우 소스를 재 빌드, 배포, 실행을 해줘야 할 것이다.  
보통 이런경우에 환경설정 파일을 배포환경 별로 관리를 하게된다.  
```
config
  |------ config.local.json
  |------ config.dev.json
  |------ config.prod.json
```

하지만 [Twelve Factor App](https://12factor.net/ko/config)에 의하면 config 설정은 특정 언어나 OS에 의존하지 않도록 **환경변수** 를 사용하라고 나와있다.  

[Go 언어를 위한  Configuration library](https://github.com/avelino/awesome-go#configuration)가 다양하게 존재한다.  
이 라이브러리들도 크게 환경설정 파일을 이용하거나 환경변수를 이용하는 것들로 나뉘는 것 같다. 대부분의 사용법이나 컨셉은 비슷한 것 같다.   

환경설정을 위해서 구글 검색을 하다가 **설정파일 기반의  [Gonfig](https://github.com/Tkanos/gonfig)** 를 발견하고 적용해 보았다. [config-sample](https://github.com/Tkanos/gonfig-sample)에 나와있는 대로 설정하고 실행해 보면 잘 되는 것을 확인 할 수 있다.  
아래 구성한 것은 gonfig 관련된 소스를 config 패키지로 뺀것만 다르다.  
```
~gogo>  glide get github.com/tkanos/gonfig
```
![배포 환경별 설정 파일들](/images/gonfig_files.PNG)
![전체 소스](/images/gonfig_config.PNG)

```
~gogo> go run main.go
8081
local
```
테스트도 정상적으로 동작하는 것을 확인 할 수 있다.  
```
~gogo\config> go test
filePath C:\Users\kihoonkim\goworkspace\src\github.com\kihoonkim\gogo\config/config.local.json
PASS
ok      github.com/kihoonkim/gogo/config        0.036s
```
하지만 **문제는 test coverage 를 측정할 때 오류** 가 난다.  
`_test\_obj_test`가 경로에 추가되면서 제대로된 경로에서 찾지를 못하면서 오류가 난다.  
```
~gogo\config>  go test -cover
filePath github.com\kihoonkim\gogo\config\_test\_obj_test/config.local.json
exit status 500
FAIL    github.com/kihoonkim/gogo/config        0.033s
```
run, test 때와 cover 옵션을 붙였을 때 `runtime.Caller(0)`가 리턴해주는 경로가 다른 것이다.  

해당 문제를 제대로 해결하지는 못했다. config 패키지의 전체 경로를 하드코딩하든가, `_test\_obj_test` 가 경로에 있는 경우 삭제하는 방식으로 해결 할 수 있었다. 하지만, 두 방법 다 좋아 보이지 않는다.   

결국엔 **환경변수** 를 사용하는 방식으로 가기로 결정했다.  
**[env](https://github.com/caarlos0/env) library** 를 사용해서 구현하였다.  
라이브러리는 github star를 많이 받은 녀석 중 가장 간단해 보이는 것으로 선택했다.  

```
~gogo>  glide get github.com/caarlos0/env
```
설치 후 configuration.go, configuration_test.go 를 아래와 같이 변경 후 실행하였다.  
```Go
// gogo/config/configuration.go
package config

import (
	"fmt"
	"github.com/caarlos0/env"
)

type Configuration struct {
	Port  int  `env:"PORT" envDefault:"3000"`  // env library
}

func GetConfiguration() Configuration {
	configuration := Configuration{}
	err := env.Parse(&configuration)   // env library
	if err != nil {
		fmt.Printf("%+v\n", err)
	}
	return configuration
}
```
```Go
// gogo/config/configuration_test.go
package config

import (
	"testing"
	"os"
	"fmt"
)

func TestGetConfiguration(t *testing.T) {
	// mock env variable
	os.Setenv("PORT", "1234")

	configuration := GetConfiguration()

	if configuration.Port != 1234 {
		fmt.Errorf("Expected %d, but Actual %d", 1234, configuration.Port)
	}
}
```
```
~gogo\config>  go test -cover
PASS
coverage: 80.0% of statements
ok      github.com/kihoonkim/gogo/config        0.040s
```
테스트 및 커버리지 측정도 정상적으로 동작하는 것을 확인 할 수 있었다.  

환경변수는 각 OS 별로 설정하면 될 것이다.  
- windows: 시스템속성 > 환경변수  
- Linux/Mac:  export PORT=4321

linux/mac 이라면 아래와 같이 실행시 넘겨줘도 된다.  
```
$ PORT=1111 go run main.go
1111
```
docker 를 사용해서 실행한다면 -e 옵션으로 넘겨주면 된다.  
```
$ docker run ... -e PORT="1111" ...
```


환경설정파일을 사용하는 라이브러리와 환경변수를 사용하는 라이브러리에 대해서 테스트 해 보았다. 라이브러리를 떠나서 두 경우다 장단점이 있을 것이다.  
각자의 환경에 맞게 구성하면 될 것 같다.  
