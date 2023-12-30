HTML 은 프로그래밍 언어인가?

저는 HTML 만으로도 충분한 프로그래밍 언어라고 생각한답니다. 보다 정확히 얘기하면 많은 사람들이 프로그래밍 언어라고 부를 수 있는 것조차 정의할 수 있는 언어라고 생각한답니다. 변수 선언, 산술 연산 등도 정의가능한 언어다.

어딘가는 마크업 언어라고 하며 "HTML은 프로그래밍 언어가 아니라 마크업 언어입니다. HTML은 웹사이트의 모습을 기술하기 위한 마크업 언어로, 문서의 내용 이외의 문서의 구조나 서식 같은 것을 포함합니다. HTML은 변수, 조건문, 반복 루프가 없기 때문에 프로그래밍 언어가 아닙니다." 라고 하고, 어딘가에서는 선언적 프로그래밍 언어로 봐주기도 하네요. 위키백과에 따르면 "가장 이상한 프로그래밍 언어의 예는 완전히 선언형이라는 것이다. HTML은 순서대로 일어나는 사건이 없기 때문에 진정한 선언형이다. 자바스크립트를 추가하면 순서대로 화면을 바꿀 수 있기 때문에 선언형의 순수함을 잃는다. 인터페이스 기술 언어(IDL)은 계산법을 명시하지 않고 관계를 명시하기 때문에 주로 선언형이다. 그러나 이 두 가지 예 모두 아무것도 계산하지 않기 때문에 실제 프로그래밍 언어인지는 전혀 분명하지 않다." 또, 위키에 따르면 "명령형 프로그램은 알고리즘을 명시하고 목표는 명시하지 않는 데 반해 선언형 프로그램은 목표를 명시하고 알고리즘을 명시하지 않는 것이다." 이라고 하네요.

간단하게, HTML 로 많은 이들이 부르는 프로그램을 짤 수 있도록 한다면, 이제 프로그래밍 언어라고 부를 수 있지 않을까요?

그래서, 직접 프로그래밍 가능성을 보여주는 장난끼 가득한 토이 프로젝트를 진행해 봅니다.

첫번째, 접근은 Hello world 출력입니다. 아마도 모든 프로그래밍 언어들이 갖추어야 할 첫번째 기능이 아닐까요? 🤪

echo "<p>Hello world</p>" > index.html ; Start-Process -FilePath Chrome -ArgumentList file:///C:/Users/novem/index.html

<<Powershell>>

간단하게 만들 수 있네요.

그런데, 이것만 가지고 프로그래밍 언어라고 하기에는 뭔가 부족해 보여서, 조금 시간을 투자해보기로 했답니다. 코드를 짜듯이 형식와 규격에 맞추어서 산술연산을 하는 프로그램을 태그를 통해서 만들어 보자.

두번째, 산술 연산이 가능한 프로그램을 태그를 통해서 정의하자. 그리고 화면에 출력될 때 정의한 프로그램의 결과를 출력해주자.

<sean-programming>
    <sean-declare name="x" value="1"></sean-declare>
    <sean-declare name="y" value="2"></sean-declare>
    <sean-function name="add" parameter="x, y" return="x + y"></sean-function>
    <sean-declare name="result">
        <sean-exec type="function" target="add">
            <sean-parameter type="variable" target="x"></sean-parameter>
            <sean-parameter type="variable" target="y"></sean-parameter>
        </sean-exec>
    </sean-declare>
    <sean-exec type="stdio" target="printf">
            <sean-parameter type="string" value="result %d"></sean-parameter>
            <sean-parameter type="variable" target="result"></sean-parameter>
    </sean-exec>
    <sean-return type="variable" target="result"></sean-return>
</sean-programming>

위 처럼 한땀 한땀 한 라인의 코드를 정의해보았습니다. 역시 정확하게 3을 출력해주는군요. 🤪

HTML 스펙에는 사용자 태그를 정의할 수 있도록 합니다. 저도 아직 스펙을 다 읽어 보지 않고 필요한 부분만 읽어 보아서, 아직은 HTML 을 차마 잘하는 언어로 넣을 수는 없지만, 언젠가 HTML 스펙을 완독하고 HTML 로 다양한 것들을 만들 수 있다면, HTML 을 저의 스킬 페이지에 한줄 추가하는 것으로 🤪 HTML4 에서는 스키마를 정의할 수 있었는데, HTML5 는 스키마를 정의할 수 있는지 잘 모르겠지만, 사용자 스키마를 정의하고 그곳에 사용자 엘레먼트를 정의할 수 있다면, 프로그래밍 언어라고 부르는 것에 큰 문제도 없어 보입니다. 지금의 프로그래밍 가능성을 보여준 것으로도 괜찮지 않을까요?

인터넷에 언제부터인가 HTML is a programming language. 라는 인터넷 밈들이 돌아다디던데, 프로그램을 만들 수 있도록 한다면, 프로그래밍 언어라고 할 수도 있어 보이고요. 선언형 프로그래밍 언어로 보는 것도 좋아 보이고, 이 밈은 정말 재미있으면서도 저를 어렵게 하네요. 🤔

여튼, 저는 HTML 로 명령형 프로그램이 할 수 있는 것들을 간단하게나마 보여주었기 때문에, 저는 앞으로 HTML 을 프로그래밍 언어로 보려고 합니다. 🤪

그리고 HTML 을 잘한다고 한다면, 자신의 스킬 셋에 HTML을 넣어 두는 것도 무방하다고 봅니다.

https://html.spec.whatwg.org/#custom-elements