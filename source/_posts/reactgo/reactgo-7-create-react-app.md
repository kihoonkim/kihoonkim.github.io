---
title: create & run & test React App
date: 2017-04-29 14:12:00
tags:
  - 개발일지
  - reactjs
categories:
  - ReactGo
  - reactjs
thumbnail:
---

처음 React.js 개발환경을 설정 할때를 떠올려보면 Babel, Webpack이나 test를 위한 라이브러리(Jest)를 설정하기 위해 수 많은 삽질과 시간을 들였던 것 같다. 특히 나와 같이 Front-End 기술에 익숙하지 않은 사람들은 이런 기술들이 어렵게 다가 올 수 있다.  

하지만, [create-react-app](https://github.com/facebookincubator/create-react-app)을 사용하여 React 프로젝트 생성하면 쉽게 만들고 설정할 수 있다.  

> Create React apps with no build configuration.

### create-react-app 설치 및 프로젝트 생성
```
$ npm install -g create-react-app
$ create-react-app my-first-react

Creating a new React app in C:\Users\kihoonkim\workspace\my-first-react.

Installing packages. This might take a couple minutes.
Installing react, react-dom, and react-scripts...

It looks like you are using npm 2.
We suggest using npm 3 or Yarn for faster install times and less disk space usage.

npm WARN optional dep failed, continuing fsevents@1.0.17
```

create-react-app으로 프로젝트를 생성하면 아래와 같은 구조를 만들어 준다. 매우 단순한 package.json을 볼 수 있다.  
![](/images/create-react-app-structure.PNG)  

설치 하고 나면 아래와 같은 메시지를 볼 수 있다. 하나씩 실행해 보자.  
![](/images/create-react-app-init.PNG)  

#### npm start
단순히 local dev server를 통해 실행한다.  
http://localhost:3000 을 통해 접속해 볼 수 있고, 소스 파일 수정시 자동으로 hot deploy 된다.  
```bash
$ cd my-first-react
$ npm start
---
> my-first-react@0.1.0 start C:\Users\kihoonkim\workspace\my-first-react
> react-scripts start
---
Starting the development server...

---
Compiled successfully!

The app is running at:

  http://localhost:3000/

Note that the development build is not optimized.
To create a production build, use npm run build.
```

#### npm run build
[webpack](https://webpack.github.io/) 을 통해서 컴파일, 압축 등을 한 후 static asset을 생성한다.  
**build** 디렉토리 하위에 파일이 생성된다.

```bash
$ npm run build

> my-first-react@0.1.0 build C:\Users\kihoonkim\workspace\my-first-react
> react-scripts build

Creating an optimized production build...
Compiled successfully.

File sizes after gzip:

  46.63 KB  build\static\js\main.00c83e70.js
  289 B     build\static\css\main.9a0fe4f1.css

The project was built assuming it is hosted at the server root.
To override this, specify the homepage in your package.json.
For example, add this to build it for GitHub Pages:

  "homepage": "http://myname.github.io/myapp",

The build folder is ready to be deployed.
You may serve it with a static server:

  npm install -g serve
  serve -s build
```

#### npm test
테스트 파일을 실행 시켜준다. 기본적으로 [jest](https://facebook.github.io/jest/)를 사용한다.  

```
$ npm test
---
> my-first-react@0.1.0 test C:\Users\kihoonkim\workspace\my-first-react
> react-scripts test --env=jsdom
---
RUN  src\App.test.js
---
PASS  src\App.test.js
 √ renders without crashing (16ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        0.385s, estimated 7s
Ran all test suites.

Watch Usage
› Press p to filter by a filename regex pattern.
› Press q to quit watch mode.
› Press Enter to trigger a test run.
```

#### npm run eject
react-scripts 를 통해 사용하던 dependencies나 설정 파일들을 현재 앱 디렉토리로 복사해 준다.
**react-scripts는 삭제되고, 절대로 다시 이전으로 되돌릴수 없다.**

```
$ npm run eject

> my-first-react@0.1.0 eject C:\Users\kihoonkim\workspace\my-first-react
> react-scripts eject

Are you sure you want to eject? This action is permanent. [y/N]
y
Ejecting...

Copying files into C:\Users\kihoonkim\workspace\my-first-react
  Adding config\env.js to the project
  Adding config\paths.js to the project
  .....

Updating the dependencies
  Removing react-scripts from devDependencies
  Adding autoprefixer to devDependencies
  Adding babel-core to devDependencies
  .....

Updating the scripts
  Replacing "react-scripts start" with "node scripts/start.js"
  Replacing "react-scripts build" with "node scripts/build.js"
  Replacing "react-scripts test" with "node scripts/test.js"

Configuring package.json
  Adding Jest configuration
  Adding Babel preset
  Adding ESLint configuration

Running npm install...
```

초기 상태 그대로 실행하면 아래와 같다.  
![](/images/create-react-app-start.PNG)  

초기 세팅을 했으므로 이제 개발이랑 테스트 삽질기를 작성해야겠다..
