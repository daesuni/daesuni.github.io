---
layout: post
title: Javascript 세미콜론(;) 써야하나, 말아야 하나?
category: Javascript/Jquery
---

Javascript에서 세미콜론은 필수가 아닌 선택사항이다. 그래서 stack overflow나 다양한 개발 커뮤니티에서는 이것에 대해 찬/반이 갈리곤 한다. 나 또한 어느 한 쪽으로 결정을 내리지 못한 상태이기 때문에 이 주제에 대해 생각해보고 정리하는 포스팅을 하기로 한다.

### 1. javascript에서의 세미콜론이란?

JavaScript는 문장의 끝을 세미콜론으로 구별하지만 필수로 요구하지 않는다. 이유는 인터프리터가 세미콜론이 필요한 곳이 있을 때 알아서 뒤에 추가하기 때문이다. 이것을 우리는 Automatic Semicolon Insertio(자동 세미콜론 삽입) 이라고 한다.<br/>
세미콜론을 사용하던 하용하지 않던 자유이지만, 규칙을 알고 있는것이 중요하다. 위에서 말했듯 세미콜론이 필수는 아니지만 쓰지 않았을 경우에 원치않는 결과나 에러를 얻을 수 있는 경우의 수가 있다.<br/>
글로만 설명하면 알아듣기 힘드니 아래의 예를 보자.

```javascript
// 내가 작성한 코드
function getStudent() {
  return
    {
      name: 'daesuni',
      class: '3-1'
    }
}

// javascript가 생각하는 코드
function getStudent() {
  return;
    {
      name: 'daesuni',
      class: '3-1'
    };
}
```

위의 예제를 보면 ```getStudent```라는 함수는 학생 개체를 반환하는 함수로 의도하고 작성했다. 하지만 javascript ASI는 끔찍하게도 개행이 있는 return 뒤에 세미콜론을 추가해 버리고 ```getStudent```의 리턴 결과를 받지 못한다.<br/>
그럼 ASI가 어떤어떤 상황에서 세미콜론을 추가하는지 알아보자.

### 2. ASI의 자동 세미콜론 삽입 규칙

JavaScript 파서는 소스 코드를 파싱하는 동안 다음과 같은 특정 상황을 발견하면 자동으로 세미콜론을 추가한다.

1. 줄바꿈이 되는 행의 마지막에
2. 행의 마지막이 ```}``` 이고 줄바꿈이 되어 다음 행이 시작될때
3. 파일의 끝에 도달할 때
4. ```return```이 있는 행의 마지막에
5. ```break```가 있는 행의 마지막에
6. ```throw```가 있는 행의 마지막에
7. ```continue```가 있는 행의 마지막에

일반적으로 위의 경우들이 많이 발생하지는 않지만 세미콜론을 쓰지 않을 때 잠재적인 오류의 가능성을 내포하고 있는 것이다.

https://bakyeono.net/post/2018-01-19-javascript-use-semicolon-or-not.html
https://medium.com/@deptno/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%BB%A8%EB%B2%A4%EC%85%98-%EC%84%B8%EB%AF%B8%EC%BD%9C%EB%A1%A0-%EC%9D%80-%ED%95%84%EC%9A%94%ED%95%9C%EA%B0%80-e30f31461224
https://flaviocopes.com/javascript-automatic-semicolon-insertion/
https://codeburst.io/why-i-prefer-to-use-semicolon-in-javascript-f00c303547
http://cassandrawilcox.me/when-to-use-semicolons-in-javascript/
https://www.clien.net/service/board/kin/8347999
