---
title: React 개발 환경 구성 (react, webstorm)
date: 2020-02-08 01:00:00
tags:
  - reactjs
  - konvajs
  - canvas
  - whiteboard
categories:
  - React
thumbnail:
---

개발 tool은 **webstorm** 을 사용하고, react는 **create-react-app** 을 통해 생성하도록 하겠습니다.
* npm  5 버전 이상 설치되어 있어야 합니다.

1. Create React App
2. eslint 설정
3. 상대경로 지옥 피하기. jsconfig.json 설정.
4. Unresoulved function or method describe() waring 수정


## Create React App
[create-react-app](https://github.com/facebook/create-react-app) 을 이용해서 react app 구성을 하겠습니다.
```bash
npm install -g create-react-app
npx create-react-app react-konva-whiteboard
cd react-konva-whiteboard
```

정상적으로 생성 되었다면 아래와 비슷한 구조로 되어 있어야 합니다. create-react-app 버전 마다 조금 다를 수 는 있지만 크게 상관은 없습니다.
```
react-konva-whiteboard
├── README.md
├── node_modules
├── package.json
├── .gitignore
├── public
│   ├── favicon.ico
│   ├── index.html
│   ├── logo192.png
│   ├── logo512.png
│   ├── manifest.json
│   └── robots.txt
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── index.css
    ├── index.js
    ├── logo.svg
    ├── serviceWorker.js
    └── setupTests.js
```


## eslint 설정
create-react-app 으로 생성을 했다면 eslint 가 기본으로 설치되어 있습니다. 
eslint 설정은 **.eslintrc.json** 또는 package.json 에 할 수 있습니다. 
개인적으로 별도의 설정 파일로 분리하는 것을 선호 하기 때문에 .eslintrc.json 을 만들겠습니다. root directory에 생성하시면 됩니다. package.json 이랑 같은 위치입니다.
그리고 **eslint-config-airbnb** 를 사용하도록 하겠습니다.

1. eslint-config-airbnb 설치
```bash
npx install-peerdeps --dev eslint-config-airbnb
```
위 와 같이 설치를 하면 package.json 에서 추가된 dependency를 확인 하실 수 있습니다.
```json
  "devDependencies": {
    "eslint": "^6.6.0",
    "eslint-config-airbnb": "^18.0.1",
    "eslint-plugin-import": "^2.18.2",
    "eslint-plugin-jsx-a11y": "^6.2.3",
    "eslint-plugin-react": "^7.14.3",
    "eslint-plugin-react-hooks": "^1.7.0"
  }
```

2. .eslintrc.json 에 extends 추가  

```json
{
  "extends": [ 
    "react-app", 
    "airbnb", 
    "airbnb/hooks"
  ]
}
```
3. webstorm 설정  

eslint 설정을 활성화 해 줍니다.
File > Settings > Languages & Frameworks > JavaScript > Code Quality Tools > ESLint
![webstorm eslint 설정](/images/react/eslint-webstorm.png)

4. Fix eslint problems
아래 그림과 같이 라인 단위 혹은 현재 파일의 eslint problems 을 고칠 수 있습니다.
![fix eslint problem](/images/react/fix-eslint.png)
하지만 자주 발생 하기 때문에 개인적으로는 **alt + f** shortcut 을 등록해 두고 사용합니다.
![fix eslint problem shortcut](/images/react/eslint-shortcut.png)



## 상대경로 지옥 피하기. jsconfig.json 설정. 
react 개발을 하다보면 import 할 때 상대 경로를 사용해야 하는데 해당 위치를 기억하고 매번 찾아야 하기 때문에 매우 불편합니다.
```
import MyComponent from '../../../etc/MyComponent'
```
babel module resolver를 통해서 alias 를 지정해서 해결 할 수 있지만, create-react-app 은 기본적으로 설정을 custom 할 수 없기 때문에 eject 를 해야지만 가능합니다.
(eject 하지 않고 커스텀 설정하는 방법은 [react-app-rewired](https://github.com/timarney/react-app-rewired) 를 보시면 됩니다.)
하지만 jsconfig.json 를 통해서 쉽게 해결 할 수 있습니다. vscode 설정 방법은 많이 나와있는데, webstorm 설정 방법은 찾이 어려워 tip으로 추가해 봅니다.

1. 프로젝트 root 에 jsconfig.json 을 생성합니다. 

```json
{
  "compilerOptions": {
    "baseUrl": "src"
  },
  "include": ["src"]
}
```

2. webstorm 에서 src directory 를 **Resource Root** 로 설정해 줍니다. 폴더 아이콘이 보라색으로 변경된 것을 볼 수 있습니다.
![Mark Directory as Resource Root](/images/react/jsconfig.png)

테스트 해보기 위해 App 컴포넌트를 src/compononts 밑으로 옮겨 보겠습니다.
```
└── src
    ├── components
    │   ├── App.css
    │   ├── App.js
    │   └── App.test.js
    ├── index.css
    ├── index.js

```
index.js 에서 App.js 를 참조하고 있으니 수정해 보겠습니다. 
```js
// AS-IS 상대 경로
import App from './components/App';

// TO-BE 절대 경로
import App from 'components/App';
```

3. eslint problem fix
정상적으로 동작은 하지만 eslint에 의해서 **import/no-unresolved** problem 경고가 나옵니다.
.eslintrc.json 에 rule 예외를 적용합니다.
```json
{
  "extends": ...,
  "rules": {
    "import/no-unresolved": 0
  }
}
```

## Unresoulved function or method describe() waring
test code 를 작성 하다보면 잘 동작은 하지만,
describe, it, dexpect 등에 밑 줄이 그어지고 경고 메시지가 나옵니다.
![Unresoulved function or method describe()](/images/react/unresolved-function.png)

File > Settings > Languages & Frameworks > JavaScript > Libraries
에서 **Download 버튼**을 누르고 **jest** 를 찾아서 download 해줍니다.
![download jest library](/images/react/jest-library.png)

**@types/jest** 에 체크 되어있는 것을 확인하고 Apply 를 눌러 적용해 줍니다. 
**webstorm 을 재시작** 하고 나면 경고가 없어진 것을 확인 하실 수 있습니다.


### day1 마무리
webstorm에 기본적인 react 개발 환경 설정을 해 보았습니다. 당연히 vccode 등 다른 tool에서 개발하셔도 상관없습니다.

이제 본격적으로 react whiteboard 를 만들어 보도록 하겠습니다.