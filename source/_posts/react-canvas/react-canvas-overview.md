---
title: React로 Whiteboard 만들기 overview
date: 2020-02-08 00:00:00
tags:
  - reactjs
  - konvajs
  - canvas
  - whiteboard
categories:
  - React
thumbnail:
---

React로 online whiteboard를 만들어 보려고 합니다. 

Web으로 whiteboard를 만드려면 [Canvas API](https://developer.mozilla.org/ko/docs/Web/HTML/Canvas/Tutorial/Basic_usage) 에 대한 이해가 있어야 하지만, [konva.js](https://konvajs.org/) 를 사용할 것이 기 때문에 깊이 알지 못해도 괜찮습니다. Canvas api 에 대해 자세히 알고 싶다면 [HTML Canvas Deep Dive](https://joshondesign.com/p/books/canvasdeepdive/title.html) 를 살펴 보시는 것도 좋을 것 같습니다.

하지만 React 에 대해서는 이미 알고 있다는 가정하에 진행합니다. React 문법에 대한 설명은 공식 문서를 참조하시면 좋을 것 같습니다.
- [Main Concepts](https://reactjs.org/docs/hello-world.html)
- [Advanced Guides](https://reactjs.org/docs/accessibility.html)
- [Hooks](https://reactjs.org/docs/hooks-intro.html)

그리고 ES2015+ 문법을 사용 하기 때문에 아래 문서를 가볍게 훑어 보시면 좋을 것 같습니다.
- [ES2015+ cheatsheet](https://devhints.io/es6)