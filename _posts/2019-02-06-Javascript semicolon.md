---
layout: post
title: Javascript 세미콜론(;) 써야하나, 말아야 하나?
category: Javascript/JQuery
---

Javascript에서 세미콜론은 필수가 아닌 선택사항이다. 그래서 stack overflow나 다양한 개발 커뮤니티에서는 이것에 대해 찬/반이 갈리곤 한다. 나 또한 어느 한 쪽으로 결정을 내리지 못한 상태이기 때문에 이 주제에 대해 생각해보고 정리하는 포스팅을 하기로 한다.

### 1. javascript에서의 세미콜론이란?

JavaScript는 문장의 끝을 세미콜론으로 구별하지만 필수로 요구하지 않는다. 이유는 인터프리터가 세미콜론이 필요한 곳이 있을 때 알아서 뒤에 추가하기 때문이다. 이것을 우리는 **Automatic Semicolon Insertio**(자동 세미콜론 삽입) 이라고 한다.

세미콜론을 사용하던 하용하지 않던 자유이지만, 규칙을 알고 있는것이 중요하다. 위에서 말했듯 세미콜론이 필수는 아니지만 쓰지 않았을 경우에 원치않는 결과나 에러를 얻을 수 있는 경우의 수가 있다.

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

위의 예제를 보면 `getStudent`라는 함수는 학생 개체를 반환하는 함수로 의도하고 작성했다. 하지만 javascript ASI는 개행이 있는 return 뒤에 세미콜론을 추가해 버리고 `getStudent`의 리턴 결과를 받지 못한다.

그럼 ASI가 어떤어떤 상황에서 세미콜론을 추가하는지 알아보자.

### 2. ASI의 자동 세미콜론 삽입 규칙

JavaScript 파서는 소스 코드를 파싱하는 동안 다음과 같은 특정 상황을 발견하면 자동으로 세미콜론을 추가한다.

1. 줄바꿈이 되는 행의 마지막에
2. 행의 마지막이 `}` 이고 줄바꿈이 되어 다음 행이 시작될때
3. 파일의 끝에 도달할 때
4. `return`이 있는 행의 마지막에
5. `break`가 있는 행의 마지막에
6. `throw`가 있는 행의 마지막에
7. `continue`가 있는 행의 마지막에

일반적으로 위의 경우들이 많이 발생하지는 않지만 세미콜론을 쓰지 않을 때 잠재적인 오류의 가능성을 내포하고 있는 것이다.

### 3. 세미콜론이 필요하다는 의견들

<a target="_blank" href="https://github.com/tc39/ecma262">TC39(ECMA 인터내셔널의 ECMA 스크립트 담당 기술 위원회)</a>에서는 spec에 다음과 같이 명시하고 있다.

> In the circumstance that an assignment statement must begin with a left parenthesis, it is a good idea for the programmer to provide an explicit semicolon at the end of the preceding statement rather than to rely on automatic semicolon insertion.

할당문이 왼쪽 괄호로 시작해야 하는 상황에서, 프로그래머는 자동 세미콜론 삽입에 의존하기 보다는 앞의 문구 끝에 명시적인 세미콜론을 제공하는 것이 좋다.

세미콜론을 찬성하는 사람들은 오류 가능성을 내포하는 불완정성, 가독성 등을 이유로 세미콜론은 필요하다고 주장하고 있다. 이 사람들은 브라우저가 아닌 다른 프로그램에서 세미콜론이 없는 소스코드를 사용할 시 문제가 된다고 지적한다. 예로 javascript minifier에서 세미콜론이 없으면 에러가 발생한다.

- <a target="_blank" href="https://bytearcher.com/articles/plea-for-semicolons/">PANU PITKÄMÄKI</a>
- <a target="_blank" href="http://lucumr.pocoo.org/2011/2/6/automatic-semicolon-insertion/">Armin Ronacher</a>

### 4. 세미콜론이 필요없다는 의견들

많은 커뮤니티에서는 세미콜론이 필요없다는 의견 역시 팽팽하다. 이사람들은 ASI규칙을 인지하고 있다면  알고있으면 굳이 세미콜론을 쓸 필요가 없다. 따라서 코딩할 때 코드를 덜 써도 된다 등을 이유로 쓰지 않아도 된다고 주장하는 상황이다.

- <a target="_blank" href="https://medium.com/@goatslacker/no-you-dont-need-semicolons-148d936b9cf2">Josh Perez</a>
- <a target="_blank" href="https://feross.org/never-use-semicolons/">Feross Aboukhadijeh</a>

### 5. 그래서 써? 말아?

이 주제에 대해서는 아직도 의견이 분분하고 현재 진행중이다. 결론은 특별한 이슈가 없는 한 개인적 스타일인것 같다. 어떤 방식을 선택하든지 일관성있는 코드를 작성하는 것이 중요하다고 볼 수 있다. TC39에서 세미콜론 쓰기를 권고하고 있으니 나는 쓰는 방식으로 하겠다.
