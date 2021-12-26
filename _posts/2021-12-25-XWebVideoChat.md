---
layout: post
title: "X Web video chat"
subtitle: "X Web Video Chat 개발기"
cover: /assets/images/posts/1639878329632.jpeg
date: "2021-12-25 09:18:00"
categories: posts
tags: ["X Web video chat"]
---

## X WEB VIDEO CHAT HISTORY #1

이제 WEBRTC로! 당분간 여러 파일럿을 진행하게 되는 운명인 듯 합니다.
요구사항에 부합되는 오픈 소스를 찾다가 없어서 "하나 만들자!"라고 결론 내렸습니다. 그런 결정을 내렸다면 뒤돌아보지 않고 밀어 부치는데, 오늘은 뒤를 돌나보게 됩니다.

다들 잘 지내나? 나는 굉장히 보고 싶은데, 다들 제 마음과 같을까요? 같이 일하기 보다는 잘 되기를 기원하며 살고 있다는 것! 저도 까칠까칠하지만 만만치 않은 까칠한 분들이라! 잘 살고 있다면 그만이죠.

예전처럼 밀어 부칠 생각에 신나기도 하고 겁도 나네요. 이제 그 때처럼 퍼포먼스가 날까? 하긴 파일럿이라 걱정은 없네요. 😀

REACT 안쓰려 했는데, 킄 😀, 다만, TUN, STUN 서버는 오픈 소스로 프런트만 진행해볼 겁니다. 간단한 백엔드하고...

## X WEB VIDEO CHAT HISTORY #2

"WEBRTC"를 사용한 "VIDEO WEB CHAT"에 대한 파일럿을 직접 개발하기로 하였습니다. 무모해 보이지만 가 보는 거죠. 그래서 당분간 계획했던 것을 모두 미루어 보려고 합니다. GITHUB 을 돌아다니다 보니 WEB VIDEO CHAT 에 대한 좋은 레퍼런스가 하나 두 개 뿐이 없더군요. 여튼, 잘 만들어지면 GITHUB 의 스폰서 기능도 사용해 볼까요? 흐흐흐흐! 기대에 차 있는 만큼 좋은 퀄리티를 내지 못하겠죠. 그저 파일럿이니까? 다만, 파일럿의 품질만큼 개발이 되었으면 좋겠습니다. 핫!

## X WEB VIDEO CHAT HISTORY #3

당분간은 InVision Studio 로 작업을 하기로 하였습니다. InVision Studio 를 허풍을 좀 떨어서 백 만년 만에 사용하는 것 같은데, 익숙하지 않으니 Adobe XD 의 결제의 유혹을 이겨내야만 했답니다. 😭 약 두 시간 가량 쓰다 보니 금방 익숙해지네요. 😀 좋은 툴이지만, 아쉽게도 많이 느리네요. 😭 아마도, Electron 기반으로 만들어서 일 듯 보입니다. InVision 에 대한 생각은 많이 느리다. 결제하면 빠를까? 고민스럽지만, 당분간은 무료의 범위 내에서 작업하는 것으로 생각하고 있답니다. InVision 도 쓰다 보니 익숙해지네요.

대략, WEBRTC 에 대한 PROTOTYPE 작업을 마쳤습니다. React 으로 Single Page App 을 만들려 했지만, 저는 하드보일드! 보일러플레이트 방식으로 구현하려 합니다. Node 까지 필요 없어 보이긴 합니다. 차후에 React 으로 감싸면 되지 않을까 싶은데, React 과 Bootstrap 이 그리 어울리는 것 같지 않아서, React UI Lib 를 찾아보는 작업도 해봐야겠네요. 당분간은 회사 일에 올인하려 합니다. WEBRTC 오랜 만에 작업하려 하니 좋네요. 핫!

결론은

1. InVision
2. Boilerplate
3. Bootstrap

정도로 작업하려 한다. 뭐 그런 이야기입니다. 핫!

어제, React, Webpack 설정하면서 좋아라 했는데, 뭐 인생이 그러한 것이죠. 하드보일드, Boilerplate 방식으로! 씁쓸하지만 C 를 개발하고 싶은데, 이해되시나요? 저는 C 개발자로 먹고 살려고 했는데, 어찌... 돈 벌어 먹는 것은 C 가 아니네요. 흑! 😭 흠, 결국은 Docker 까지 까는군요. 핫!

윈도우즈에서는 불편한 도커군요.

https://lnkd.in/eQhqQBZH

docker run -dit --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4

아래처럼 하니 invalid reference format 이라고 하는군요. 이럴 때는 풀 패스를 써 주시는 것으로 ....

저는 ...

```shell
docker run -dit --name my-apache-app -p 8090:80 -v C:\Users\aeuclid\Desktop\Workspace\novemberizing\chat\docs:/usr/local/apache2/htdocs/ httpd:2.4
```

위처럼 사용한답니다. 최근에 받은 노트북이 윈도우즈여서 리눅스를 깔고 싶은 충동에 사로 잡혀 있답니다.

## X WEB VIDEO CHAT #4

오랜만에 인비전을 사용하여 프로토타입을 만들고 그것을 바탕으로 부트스트랩과 HTML로 간단하게 퍼블리셔가 하는 작업을 해봅니다. 저야! 전문 디자이너는 아니고 먹고 살려니 대충 없으면 이쁘지는 않아도 그 정도 수준에서 만들어 내는 거죠.

여튼, 회사 파일럿을 준비하면서 간단하게 WEB VIDEO CHAT 오픈 소스를 만들어 보려 하는데, 대략, 1주일,... 다른 것을 모두 미루어 놓고 일주일 동안 여기에 몰입하기로 하였습니다.

여튼, 이제 데이터 분석하고 백엔드를 가볍게 꾸미고, 프런트 작업을 하면 끝일 것이라 생각이 되네요.

1. 디자인 관점의 설계 및 구현

   1.1. 프로토타이핑

   1.2. HTML 작업

2. 프로그래밍 관점의 설계 및 구현

   2.1. 백엔드 작업

   2.2. 프런트엔드 작업

3. 작업 완료

혼자 하는 것에 익숙해져서 그렇지만 대략 흐름은 이렇게 구현이 되어가는 듯 보입니다. 각각의 사람마다 혹은 각각의 팀마다 성격이 다 틀려서 고유의 개발 방법을 가지고 있겠지만, 제 생각에는 여기서 크게 벗어나지 않을 것 같습니다. 믿거나 말거나.

PS. 프로젝트 이름은 X VIDEO WEB CHAT 이라고 명칭을 정했답니다. 핫!

<div id="carouselFourthCaptions" class="carousel slide" data-bs-ride="carousel">
  <div class="carousel-indicators">
    <button type="button" data-bs-target="#carouselFourthCaptions" data-bs-slide-to="0" class="active" aria-current="true" aria-label="Slide 1"></button>
    <button type="button" data-bs-target="#carouselFourthCaptions" data-bs-slide-to="1" aria-label="Slide 2"></button>
    <button type="button" data-bs-target="#carouselFourthCaptions" data-bs-slide-to="2" aria-label="Slide 3"></button>
  </div>
  <div class="carousel-inner">
    <div class="carousel-item active">
      <img src="/assets/images/posts/1639878329393.jpeg" class="d-block w-100" alt="Class Diagram">
      <div class="carousel-caption d-none d-md-block">
        <h5 class="text-white">Class Diagram</h5>
        <p>클래스 다이어그램</p>
      </div>
    </div>
    <div class="carousel-item">
      <img src="/assets/images/posts/1639878329440.jpeg" class="d-block w-100" alt="ERD">
      <div class="carousel-caption d-none d-md-block">
        <h5 class="text-white">ERD</h5>
        <p>ERD</p>
      </div>
    </div>
    <div class="carousel-item">
      <img src="/assets/images/posts/1639878329632.jpeg" class="d-block w-100" alt="Activity Diagram">
      <div class="carousel-caption d-none d-md-block">
        <h5 class="text-white">Activity Diagram</h5>
        <p>액티비티 다이어그램</p>
      </div>
    </div>
  </div>
  <button class="carousel-control-prev" type="button" data-bs-target="#carouselFourthCaptions" data-bs-slide="prev">
    <span class="carousel-control-prev-icon" aria-hidden="true"></span>
    <span class="visually-hidden">Previous</span>
  </button>
  <button class="carousel-control-next" type="button" data-bs-target="#carouselFourthCaptions" data-bs-slide="next">
    <span class="carousel-control-next-icon" aria-hidden="true"></span>
    <span class="visually-hidden">Next</span>
  </button>
</div>

## X WEB VIDEO CHAT #5 DIAGRAM & DATABASE CONCEPT

비디오 채팅을 만들면서 먼저 클래스 다이어그램(CLASS DIAGRAM)부터 그려 봅니다. 그리고 그것을 바탕으로 상태차트(STATEMACHINE DIAGRAM) 다이어그램을 그려냅니다.

예전에는 USE CASE 다이어그램부터 그렸는데, UML 의 USE CASE 다이어그램은 왠지 정감이 안가네요.

이렇게 그려놓고 보니 상대방과의 채팅을 원하는 VIEW 와 상대방으로부터 채팅 요청이 온 VIEW 를 만들지 않았군요. POPUP 으로 프로토타입을 만들고 구현해볼 생각입니다.

개발 방법에서 어디로든 점프할 수 있는 것이죠.

컨셉 - 설계 - 구현

간단하게 세 단계라고 하면 구현에서 컨셉으로, 설계에서 컨셉으로 움직이는 것이 가능하죠. 돌아가는 것은 당연한 일입니다. 잘못갔거나 혹은 엉뚱한 길로 갔거나, 혹은 뭐 놓고 갔다면, 돌아가야지요. 그런데, 컨셉에서 구현으로 가는 것은 조금 조심해야 합니다. 혼자 구현할 때는 머리 속에서 설계를 하면 되지만 여러 명이 구현할 때는 서로 말 맞춰야 하는 것이 설계가 아닐까 하네요.

여튼, 저는 혼자 구현하지만 차후에 같이 구현하는 것을 바탕으로 깔고 있기 때문에, 혼자서 북치고 장구치더라도 지킬 것은 지켜 보려는 것입니다. 근데, 회의는 어떻게 혼자 시뮬레이션하지요? 핫!

<div class="d-none d-lg-block">
<img src="/assets/images/posts/1639958015100.jpeg" class="d-block" alt="사용자를 등록하는 화면">

<img src="/assets/images/posts/1639958015120.jpeg" class="d-block" alt="사용자 방 리스트">

<img src="/assets/images/posts/1639958015055.jpeg" class="d-block" alt="채팅 화면">
</div>

<div class="d-block d-lg-none">
<img src="/assets/images/posts/1639958015100.jpeg" class="d-block w-100" alt="사용자를 등록하는 화면">

<img src="/assets/images/posts/1639958015120.jpeg" class="d-block w-100" alt="사용자 방 리스트">

<img src="/assets/images/posts/1639958015055.jpeg" class="d-block w-100" alt="채팅 화면">
</div>

## X WEB VIDEO CHAT #6 RE-PROTOTYPE

언제나 사람은 뒤로 돌아갈 줄 알아야 합니다. 흐흐흐! 잘못되었다면 잘못된 곳부터 다시 혹은 그것만 고치고 원자리로 돌아와서 다시 진행... 여튼, LIST VIEW 와 POPUP 을 프로토타이핑을 하고 이제 백엔드부터 구현해 보려 합니다. 아자아자! 일주일 안에 파일럿! 빡세기 일하는 스타일 아닌데... 쩝! 핫!

<div id="carouselSixthCaptions" class="carousel slide" data-bs-ride="carousel">
  <div class="carousel-indicators">
    <button type="button" data-bs-target="#carouselSixthCaptions" data-bs-slide-to="0" class="active" aria-current="true" aria-label="Slide 1"></button>
    <button type="button" data-bs-target="#carouselSixthCaptions" data-bs-slide-to="1" aria-label="Slide 2"></button>
    <button type="button" data-bs-target="#carouselSixthCaptions" data-bs-slide-to="2" aria-label="Slide 3"></button>
    <button type="button" data-bs-target="#carouselSixthCaptions" data-bs-slide-to="3" aria-label="Slide 4"></button>
    <button type="button" data-bs-target="#carouselSixthCaptions" data-bs-slide-to="4" aria-label="Slide 5"></button>
  </div>
  <div class="carousel-inner">
    <div class="carousel-item active">
      <img src="/assets/images/posts/1639964057454.jpeg" class="d-block w-100" alt="사용자를 등록하는 화면">
      <div class="carousel-caption d-none d-md-block">
        <h5 class="text-black">Register</h5>
        <p class="text-black">사용자를 등록하는 화면</p>
      </div>
    </div>
    <div class="carousel-item">
      <img src="/assets/images/posts/1639964057460.jpeg" class="d-block w-100" alt="사용자 방 리스트">
      <div class="carousel-caption d-none d-md-block">
        <h5 class="text-black">User room</h5>
        <p class="text-black">사용자 방 리스트</p>
      </div>
    </div>
    <div class="carousel-item">
      <img src="/assets/images/posts/1639964057556.jpeg" class="d-block w-100" alt="채팅 화면">
      <div class="carousel-caption d-none d-md-block">
        <h5 class="text-black">Chat</h5>
        <p class="text-black">채팅 화면</p>
      </div>
    </div>
    <div class="carousel-item">
      <img src="/assets/images/posts/1639964057512.jpeg" class="d-block w-100" alt="채팅 화면">
      <div class="carousel-caption d-none d-md-block">
        <h5 class="text-black">Chat</h5>
        <p class="text-black">채팅 화면</p>
      </div>
    </div>
    <div class="carousel-item">
      <img src="/assets/images/posts/1639964057453.jpeg" class="d-block w-100" alt="채팅 화면">
      <div class="carousel-caption d-none d-md-block">
        <h5 class="text-black">Chat</h5>
        <p class="text-black">채팅 화면</p>
      </div>
    </div>
  </div>
  <button class="carousel-control-prev text-black" type="button" data-bs-target="#carouselSixthCaptions" data-bs-slide="prev">
    <span class="carousel-control-prev-icon text-black" aria-hidden="true"></span>
    <span class="visually-hidden">Previous</span>
  </button>
  <button class="carousel-control-next text-black" type="button" data-bs-target="#carouselSixthCaptions" data-bs-slide="next">
    <span class="carousel-control-next-icon text-black" aria-hidden="true"></span>
    <span class="visually-hidden">Next</span>
  </button>
</div>

## X WEB VIDEO CHAT #7 BACKEND

금요일 오전에 데모를 보고 싶다고 하여 오늘 내일 달려 보려고 합니다. 딱히 시간 맞출 수 없다고 보기는 합니다. 버그를 놔두고 진행한다면 비디오로 서로 이야기 하는 것 까지 가능할 것 같습니다.

이제 WEBRTC 의 HANDSHAKING 과정을 WEBSOCKET 을 이용해서 구현하면 BACKEND 는 버그는 있지만, WEBRTC 부분으로 넘어가게 되겠죠. 여튼 이러하든 저러하든 WEBRTC 구현이 시작되는군요.

<img src="/assets/images/posts/1640153104514.gif" alt="backend" style="width: 100%">

## X WEB VIDEO CHAT #8 대충 완성

뭐! 그렇죠. 대충 완성 시키는 거죠. 정확히 구현하려면 https 를 지원해야 하고, 그렇지 않으면 "unsafely-treat-insecure-origin-as-secure" 설정에서 호스트를 설정해야 하고, 또, 방화벽도 열어 놓아야 하고, TUN, STUN 없이 하려면 어쩔 수가 없군요. 여튼, 다음은 TUN 혹은 STUN 을 설정해보는 것으로 생각하고 있답니다. 그나저나,... google 서버를 잠시 테스트 용도로 사용할 수 있다고 하던데, 어찌 되는지 봐야겠지요.

PS. 나중에 WEBRTC 부분만 LIB 로 만드는 것도 좋아 보이네요. 주석은 없지만 현재 소스 공유합니다. IP 도 정적으로 박혀 있고, 주석도 없고, 설명도 없지만, 그래도 알아볼 사람들은 알아보겠죠. 농담입니다. 급하지 않아서 README 를 이쁘게 꾸며 봐야지요. 여튼, 공유는 합니다.

[github](https://github.com/novemberizing/chat)

급한 것은 파일럿이니까! 급한 것 먼저 하고 주석을 달아 놓겠습니다. 믿거나 말거나 ~ 아마도 LIB 로 차후에 업데이트 할 듯 보입니다. 믿거나 말거나 ~

<img src="/assets/images/posts/1640171887981.gif" alt="backend" style="width: 100%">

