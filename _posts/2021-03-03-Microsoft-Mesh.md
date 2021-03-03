---
layout: post
title:  "Microsoft Mesh"
tags: MR
comments: true
---

어릴적 스타워즈를 통해 Sci-Fi 에 입문을 했다. 광선검, 포스, R2D2, 홀로그램, 우주 탐험 등 너무 흥미로운 요소들이 많아서 맨날 비디오를 돌려봤던 기억이있다. 

이후 15년 정도가 흘러서 스티븐 스필버그 아저씨의 Ready Player One을 보고 스타워즈를 돌려보던 7살 무렵의 기억이 떠올랐다. 그리고 궁금해졌다. AR/VR 기술이 나온지는 10년도 넘었는데 아이언맨의 자비스, 스타워즈의 홀로그램, Ready Player One 속 가상 세계는 어디까지 개발이 됐을까. 

이때 검색을 열심히 한 결과 Facebook, Google, Microsoft, Samsung 같은 대기업들이 Meta-verse와 MR/VR/AR 기술에 투자를 한다는 것 정도는 알아낼 수 있었다. 

그리고 추후에 창업(?) 비스무리 한 것을 한다면 메타버스 분야도 괜찮겠다라는 생각을 했다.

사실 이 포스트를 쓰게 된 것은 오늘 뜬 [기사](https://mspoweruser.com/microsoft-mesh-platform-mixed-reality-apps/) 때문이다. 오늘 마이크로소프트가 Microsoft Mesh를 발표했다. 

Microsoft Mesh는 개발자들을 위한 툴이다. Mixed reality 앱을 만들 수 있게 해주는 플랫폼이라고 보면 된다. Mesh를 사용하면 화상 미팅을 3D로 할 수 있다. 심지어 상대방이 홀로그램이고 작업도 같이 할 수 있다.

#### Microsoft Mesh 소개 영상
<!-- 16:9 aspect ratio -->
<div class="responsive-embed responsive-embed-16by9">
  <iframe class="responsive-embed-item" src="https://www.youtube-nocookie.com/embed/Jd2GK0qDtRg"></iframe>
</div>

나는 개발자니까 조금 더 테크니컬 한 것을 알고 싶어서 관련 기사를 찾다가 마소 테크 블로그에 잘 정리된 글을 봤다. [참고](https://techcommunity.microsoft.com/t5/mixed-reality-blog/microsoft-mesh-a-technical-overview/ba-p/2176004). 이 포스트의 나머지는 이 포스트를 의역한 것에 불과하다.

기사를 간단히 요약하자면, 마소는 MR의 도약을 주의깊게 봤고 투자할 가치가 있다고 판단을 했다. 이후 MR의 기술적 한계를 보고 이를 극복하기 위해 Mesh를 개발했다는 것을 알 수 있다. 

여기서 중요한 것은 마소가 Mesh를 활용해 새로운 MR 소프트웨어를 만들어 낸 것이 아니라 하나의 서비스로 Azure 기반 앱/웹에서 쓸 수 있도록 오픈 했다는 것이다. 

마소가 Mesh를 통해 이루고자 하는 것은 MR 개발자들을 Azure로 끌어오기 위한 것임을 알 수 있다. (물론 다른 클라우드 업체에서 비슷한 플랫폼을 추후에 지원할 것으로 보인다)

확실히 마소가 최근 들어서 개발자들을 위한 움직임을 많이 보여주고 있다.

마소가 생각한 멀티 유저 환경에서 MR의 문제점은 크게 4가지였다.

1. 사람을 MR 환경에서 "사람답게" 구현하려면 상당한 시간과 자원이 든다
2. 서로 다른 기기에서 공유된 MR 시공간에 홀로그램을 안정적으로 표현하는 것은 기술적으로 상당히 어렵다
3. 현재 개발자들이 사용하는 포맷을 고퀄리티 3D MR 공간으로 불러오기 힘들다
4. 사람의 행동이나 표현을 MR 공간에서 동기화 하는 것은 어렵다

일단 마소의 Mesh는 이 문제점들을 해결하는데 포커스를 맞췄다.

Mesh는 크게 3가지 기능으로 구성됐다.
1. Multi-device를 지원한다. 
2. 개발자 플랫폼을 지원한다
3. Mesh-enabled Apps를 지원한다.

#### Multi-device 지원

HoloLens, Oculus 부터 시작해서 스마트폰과 PC까지 지원을 한다.

#### 개발자 플랫폼 지원

개발자 플랫폼은 Azure를 기반으로 작동하며 유저 인증/인가는 Azure Active Directory 와 Microsoft Accounts 등을 통해서 할 수 있다. 개발자로서 개발에만 집중 할 수 있도록 core infra의 billing이나 오디오/비디오 처리, 또는 live-state 관리를 신경쓰지 않아도 된다고 한다. 

이 같은 코어 인프라 외에도 앞서 말한 멀티 유저 환경에서 MR의 문제점을 해결하기 위한 기능들도 있다. 

1. Immersive presence: <br>멀티 유저 시나리오에서 가장 중요한 기능인 "사람을 어떻게 표현하는가?" 를 담당한다고 볼 수 있다. 기본적으로 3D 아바타가 있고 사용하는 기기에 따라서 photorealistic holoportation을 사용할 수도 있다. <br>
Photorealistic holoportation은 외부 센서를 사용해서 구현이 되며 MR 공간에 사용자를 홀로그램 형식으로 표현할 수 있다.
2. Spatial maps: <br> Mesh 이전에 공유된 MR 공간에 특정 홀로그램 물체를 표현하려면 연결된 각 기기의 로컬 MR 환경에 맞게 구현이 됐어야 했다. 그리고 구현된 물체는 사용하는 기기와 주변 환경에 따라 일정하지 않을 때가 많았다. <br> 하지만 Mesh를 사용하면 각 기기의 로컬 환경을 읽고 캐시해서 모든 기기가 같은 MR 공간을 사용하도록 한다고 한다. 장점은 홀로그램 물체를 좀 더 명확하게 위치 시킬 수 있고 모두가 똑같은 MR 환경에 있기 때문에 각 기기에서 표현되는 차이가 없다고 봐도 되는 것 같다. ~~천...천재들이네~~
3. Holographic rendering: <br>
Mesh의 가장 큰 장점 중 하나는 렌더링 시 로컬 stand-alone을 할지 아니면 마소의 클라우드 인프라를 사용할지 정할 수 있다는 것이다. <br>
이는 개발자에게 Latency (속도) vs Fidelity (퀄리티)의 자유를 준다. (MR 개발자가 아니라 정확히는 모르지만 로컬 stand-alone 렌더링 기법은 Mesh 앱을 구동하는 기기의 컴퓨팅 파워를 사용해서 렌더하는 것 같다)<br>
또한 이미 사용되고 있는 대부분의 3D 파일 포맷을 지원한다고 한다. 즉, 현재 개발자들이 가지고 있는 3D 데이터를 Mesh의 MR 환경에 홀로그램으로 구현이 가능하다.
4. Multi user sync: <br>
MR의 핵심은 물리적으로 떨어져 있어도 같은 시공간에 있다는 경험을 하는 것이다. Mesh는 Multi user sync를 통해 유저가 세계 어느 곳에 있든 <= 100ms 의 latency를 자랑한다고 한다.

또한 Mesh는 cross-platform SDK를 지원한다. 현재는 Unity와 C++, C#을 지원하고 추후에 Unreal, Babylon, React Native도 지원할 것이라고 한다. 

#### Mesh-enabled App
이미 Mesh를 활용한 몇개의 앱들이 마켓플레이스에 있다. 앞으로 파트너들과 협력해 더 많은 앱들을 개발할 예정이라고 한다.

### 결론

Mesh는 MR 개발을 쉽게 해주는 플랫폼이고 MR, VR, AR, Metaverse 에 관심이 있는 개발자라면 한 번 시도해볼만 하다고 생각한다.

나중에 취업을 하고 사이드 프로젝트로 이쪽 분야를 공부해도 괜찮겠다고 생각한다.
