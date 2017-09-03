---
title: golang interface. mockingl. dependency-injection. Test 하기 좋은 코드
date: 2017-04-09 23:38:00
tags:
  - 개발일지
  - golang
  - Duck Typing
categories:
  - ReactGo
  - golang
thumbnail:
---

### golang의 interface와 duck typing
Java 개발자로서 Golang을 처음 접했을때, 가장 당황스러웠던 것 중 하나가, 바로 interface 였다.  
java로 인터페이스와 구현 클래스를 작성한다면 다음과 같을 것이다.  
```Java
public interface Duck {
  void Quack();
}
public class DonaldDuck implements Duck {
  @override
  public void Quack() {
    System.out.Println("I'm Donald Duck");
  }
}
....
public static void main(String[] agrs) {
  Duck duck = new DonaldDuck();
}
```
**java** 는 `implements` 키워드를 통해 DonaldDuck class는 Duck interface의 구현체 임을 **명시적으로** 선언한다.  
하지만, **Go** 에서는 어느곳에도 명시적으로 선언하지 않는다. **암묵적으로** 만족만 시킨다면 interface의 구현체라고 생각할 수 있다.  
```Go
type Duck interface {
	Quack()
}
type DonaldDuck struct {
}
func (d DonaldDuck) Quack() {
	fmt.Println("Hello I'm Docal Duck. Quack Quack")
}
func (d DonaldDuck) Walk()  {
	fmt.Println("I can walk")
}

func main() {
	var donald Duck = new(DonaldDuck)
	donald.Quack()
}
```

프로그래밍 언어에서 이런 방식을 흔히 `Duck Typing` 이라고 부른다.  
> 만약 어떤 새가 오리처럼 걷고, 헤엄치고, 꽥꽥거리는 소리를 낸다면 나는 그 새를 오리라고 부를 것이다.  

내 마음대로 해석해 보자면, 부르는 사람의 입장에서는 실제로 어떤 새인지가 중요한게 아니라 **어떻게 행동하느냐** 에 따라 어떤 새인지 구분하겠다는 것이다.  

자바 개발자 입장에서 객체지향 적으로 바라보자면...  
한 객체가 다른 객체와 협력을 할때 그 객체가 내부적으로 실제로 어떻게 동작하는 가보다는 어떤 행동을 할 수 있는가 가 중요한 것이다.  
즉, 객체간 어떤 메시지로 협력을 구성할 수 있는지, 객체가 어떤 행위를 할 수 있는지가 중요한 것이다. 객체가 어떤 행위를 수행 할 수 있는 지를 정의하는 것으로 그 객체를 추상화 할 수 있다면, 그 객체와 협력하는 객체간 의존성을 낮출 수 있다.  

java에서 한 객체가 어떻게 행동하는지를 public하게 드러내는 방법이 바로 interface()를 사용하는 것이다.  
golang에서도 interface를 동일한 방식으로 사용함으로써 의존성을 낮추고 테스트하기 쉬운 코드를 만들 수 있다.  

### interface를 사용하여 개발
예를 들어, User 정보를 조회할 수 있는 `UserService`가 있고, UserService에서 Mysql에서 조회하는 `UserMysqlRepository`를 사용한다면, UserService는 UserMysqlRepository에 의존성을 갖게 될 것이다.  
만약 Mysql이 다른 DB로 변경되거나 UserMysqlRepository의 메소드가 변경된다면 UserService도 수정이 필요해 진다.   
```Go
type UserMysqlRepository struct {}
func (r UserMysqlRepository) FindByName(name string) User  { ... }
type UserService struct {}
func (u UserService) SearchBy(name string) (user User) {
   user := UserMysqlRepository{}.FindByName(name)
   return
}
```

java에서는 이런경우를 위해 interface로 타입을 지정하고 DI(Dependency Injection)을 통해 의존성을 주입해 줌으로써 결합도를 낮춘다.  

일단 위의 예시에서 UserRepository라는 interface를 만들어 보자.  
```Go
type UserRepository interface {
    FindByName(name string) User
}
```
의존성 주입의 가장 쉬운 방법은 실행시 파라메터로 넘겨주는 것이다.  
```Go
type UserService struct {}
func (u UserService) SearchBy(name string, repo UserRepository) (user User) {
   user = repo.FindByName(name)
   return
}
func main()  {
  UserService{}.SearchBy("test", UserMysqlRepository{})
}
```
또는
```Go
type UserService struct {
  repo *UserRepository
}
func (u *UserService) SearchBy(name string, repo UserRepository) (user User) {
   user := u.repo.FindByName(name)
   return
}
func main()  {
	UserService{repo:UserMysqlRepository{}}.SearchBy("test")
}
```
이와 비슷한 내용으로 더 잘 정리된 글([Dependency Injection in Golang](https://medium.com/@zach_4342/dependency-injection-in-golang-e587c69478a8))도 참조해 보면 좋을 것 같다.  

하지만, golang의 DI를 다루는 대부분의 예제는 위 블로그 수준에서 끝난다.  
java를 개발할때 spring을 사용하는 경우 framework레벨에서 의존성을 주입해 준다.  
덕분에 controller, service, infra(DB, Network..) 레이어를 분리할 수 있다.   

하지만 golang에서 앞의 예제처럼 작성한다면.. main 함수에서, 혹은 UserService보다 상위 레이어에서 UserMysqlRepository라는 infra 레벨의 레이어에 접근을 해야하는 문제가 있다.  
```Go
// User Rest API Request Handler
func UserHandler(w http.ResponseWriter, r *http.Request) {
  // HTTP handler에서 Mysql 까지 알아야 하나???
  UserService{repo:UserMysqlRepository{}}.SearchBy("test")
  ...
}
```

이 처럼 레이어간 경계를 위해서 외부에서 Dependency Injection을 해줄 수 있는 방법이 필요해졌다.  
golang의 Injection library로는 [facebookgo inject](https://github.com/facebookgo/inject)가 가장 많이 사용되는 것 같다. 간단히 설치해보고 테스트해 보자.
![https://golanglibs.com/category/dependency-injection?sort=top](/images/golanglibs_di.PNG)  

### inject library 사용  
glide 를 통해 간단히 설치 할 수 있다.  
```
~gogo >  glide get github.com/facebookgo/inject
```
**\`inject:""\`** 을 통해 의존성 주입을 할 곳을 정해주면 된다.  
그리고 inject.Graph를 통해 주입할 대상을 매핑해 주면된다.  
```Go
type User struct {
	name string
}

type UserService struct {
	repository UserRepository `inject:""`  // <<--- Injection
}
func (s *UserService) FindAll() (users []User) {
	s.repository.FindAll()
	return
}

type UserRepository interface {
	FindAll() []User
}

type UserMysqlRepository struct {}
func (repo *UserMysqlRepository) FindAll() []User {
	var users = []User { User{"ki"} }
	fmt.Println("This is Mysql Repository")
	return users
}

func main()  {
  var userService user.UserService
	inject.Populate(&user.UserGormRepository{}, &userService)
	fmt.Println(userService.FindAll())
}
```

UserService에 대한 단위 테스트를 작성하는 경우 실제 UserMysqlRepository를 통해 실행 될 필요는 없기 때문에, Mock 객체를 사용하여 테스트하는 것이 좋다.  
golang에서 지원하는 gomock을 설치해서 사용하면 된다.  
```
go get github.com/golang/mock/gomock
go get github.com/golang/mock/mockgen
```

아래와 같이 mockgen을 통해서 mock 객체를 generate하면 된다.  
```
gogo>mockgen --source ./user/user_repository.go --destination ./user/user_repository_mock.go
```
생성된 mock 객체를 보면 아래와 같이 만들어졌다.  
수정하지 말라고 나와있지만, package 명은 user로 변경해줘야 한다.  
```Go
// Automatically generated by MockGen. DO NOT EDIT!
// Source: ./user/user_repository.go

package mock_user  // ==> user

import (
	gomock "github.com/golang/mock/gomock"
)

// Mock of UserRepository interface
type MockUserRepository struct {
	ctrl     *gomock.Controller
	recorder *_MockUserRepositoryRecorder
}
......
```

`facebookgo/inject`와 `golang/gomock`을 사용한 전체 테스트 코드는 아래와 같다.  
NewMockUserRepository에서 리턴되는 값이 **\*MockUserRepository** 포인터이기 때문에, inject.Graph의 Value로 넘겨줄때 **&** 를 붙이면 안된다.  
```Go
package user

import (
	"testing"
	"github.com/facebookgo/inject"
	"github.com/golang/mock/gomock"
	"strings"
)

func TestFindAll(t *testing.T) {
	// mock
	ctrl := gomock.NewController(t)
	mockUserRepository := NewMockUserRepository(ctrl)

	// stub
	var users = []User { User{"test user"} }
	mockUserRepository.EXPECT().FindAll().Return(users)

	// inject
	var userService UserService
	inject.Populate(mockUserRepository, &userService)

	// test
	results := userService.FindAll()
	if strings.Compare("test user", results[0].name) != 0 {
		t.Errorf("expected %s, actual %s", "test user", results[0].name)
	}
}
```

UserService와 UserGormRepository간에 interface를 통해 메시지를 정의하고 사용함으로써 실제 구현체로부터 의존성을 분리할 수 있었다. Test 코드를 작성할때도 UserService에서 UserGormRepository를 사용하는 것이 아니라 MockUserRepository라는 Mock 객체를 inject 받아 사용함으로써 각 객체의 경계를 명확히 분리하여 테스트 할 수 있었다.  

사실 이번 삽질기를 작성하면서 golang을 너무 java 개발하듯이 하는 것은 아닌가 하는 고민이 들었다.  
실제 프로젝트에서도 이와 매우 유사하게 작성해 나가고 있긴 하다. go 언어의 패러다임이 무엇인지 아직도 잘 모르겠다....
좀 더 삽질해봐야 겠다.  
