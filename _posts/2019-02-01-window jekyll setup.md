---
layout: post
title: Windows에서 Jekyll + Github 이용해서 개인 블로그 만들기
category: Jekyll
---

개발하면서 기억해야될 것들이나, 나중에 또 쓸거같은것들을 메모장이나 즐겨찾기에 관리하기에는 뭔가 너저분하고.. 기존 포털 블로그를 이용하기에는 잡다한기능이 너무 많고 커스터마이징이 힘들어서 고민을 하던중 jekyll + github 조합이 딱이라는 생각이 들었다.

Ruby는 처음 접하는 언어이고 window 환경에서 사용하기 위해 약간의 삽질을 했던 경험을 기록해서 시작하는분들에게 도움이 되고자 한다.

Github은 가입되어 있다고 가정하고 진행한다.

### 1. Git 설치하기

Windows에서 github을 이용하기 위해서는 git이 설치되어야 한다. 아래 링크를 클릭하면 자동으로 다운받을 수 있다. 별다른 주의사항은 없고 그냥 설치하면 된다.

<a href="https://git-scm.com/download/win">https://git-scm.com/download/win</a>

### 2. Ruby 설치하기

Ruby는 인터프리터 형식으로 실행되는 고기능 스크립트 언어이자 뛰어난 객체 지향적 언어이다. Jekyll은 Ruby기반이기 때문에 jekyll에 앞서 윈도우에 Ruby를 설치한다.

<a href="https://rubyinstaller.org/downloads/">https://rubyinstaller.org/downloads/</a>

위 링크로 들어가보면, 다운로드 링크가 여러개 있는데 우리는 DEVKIT도 필요하므로 WITH DEVKIT 목록에서 자신의 윈도우 환경에 맞는 버전을 다운받는다. (본인은 Ruby+Devkit 2.5.3-1 (x64)로 진행함)

루비도 별다른 설정 변경없이 기본값으로 설치를 쭉쭉 진행하면 된다. 설치가 완료되면 cmd 창으로 계속 진행되는데, 입력하라고 할때마다 enter를 쳐주면 알아서 진행된다.

### 3. Jekyll 및 기타 gem들 설치하기

Jekyll은 HTML, Markdown 같은 마크업 언어로 글을 작성하면 정해 놓은 규칙에 따라 다양한 레이아웃으로 정적 웹사이트를 만들어 주는 정적 사이트 생성 엔진 이다.

Ruby를 설치했으니 이제 Jekyll과 블로그를 만들기 위해 필요한 기타 gem들을 설치한다. gem이란 node.js의 npm이나 python의 pip와 같은 개념으로 생각하면 된다.

cmd 창으로 이동 한 후 다음을 순서대로 설치한다.

```
gem install jekyll
gem install minima
gem install bundler
gem install jekyll-feed
gem install tzinfo-data
```

### 4. Git repository 생성하기

자신의 github repository에 새로운 repository를 만든다. 이때 주의할점은 repository명은 반드시 username.github.io로 만들어야 한다는 것! (깃헙의 username) 그래야만 gitpage를 구성해준다. 추가 설명은 github 공식 사이트 <a href="https://pages.github.com/">https://pages.github.com//</a> 를 참고한다.

### 5. 개발환경

이제 준비는 다 끝났다. repository를 관리할 폴더를 만들고 이동 후 그 경로에서 다음을 입력해서 4번에서 만든 repository를 가져온다.

```
git clone https://github.com/username/username.github.io
```

clone된 폴더로 들어가서 다음을 입력한다.

```
cd username.github.io
jekyll new .
```

git에 반영하기 전에 로컬 개발환경에서 확인해보자. 지킬 서버를 실행한다

```
jekyll serve
```

브라우저를 실행하고 주소창에 127.0.0.1:4000 혹은 localhost:4000을 입력하면 생성된 지킬 페이지를 미리 볼 수 있다. 로컬로 개발하면서 실시간으로 변경사항을 확인 할 수 있다.

### 6. gitpage에 반영하기

이제 로컬의 변경을 github page로 반영 해 보자.

```
git add .
git commit -m "first"
git push origin master
```

위 명령들을 순서대로 입력해 준다. 그리고 브라우저에서 username.github.io를 입력 한 뒤, 정상적으로 반영되었는지 확인한다.
