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





어쩌면 마크업 언어라고 보는 것이 정확한 판단일 수 있지요. 다만, "프로그래밍 가능한 언어다"라고 생각하며, 지금도 "Hello world" 정도 출력하는 프로그램을 만들 수 있다고 봅니다. HTML 을 잘 안다는 것을 개발자의 스킬 중에 하나로 보아도 된다고 생각하구요.

"프로그래밍 언어는 컴퓨터 시스템에서 구동시키는 소프트웨어를 작성하기 위한 언어다."라고 본다면, "현재는 브라우져란 런타임에서 'Hello world'를 출력할 수 있다." 정도로 판단합니다. 그래서 어쩌면 "프로그래밍 언어일 수도 있겠다." 정도가 저의 판단이랍니다. 혹자들은 "선언적 프로그래밍 언어"라고 하는데, 이 부분도 충분히 공감하고 웹 페이지를 보여주기 위한 프로그래밍 언어로 생각하기도 한답니다. 저도 이런 부분 때문에 정확히 판단할 수 없네요.

echo "<p>Hello world</p>" > index.html ; Start-Process -FilePath Chrome -ArgumentList file:///C:/Users/novem/index.html

<<Powershell>>

다만, 인터넷에 돌아다니는 밈들을 보니, HTML 을 스킬로 취급하지 않아 보이네요. 이 부분이 저는 아쉬운 부분이랍니다. 너무 언어를 잘 만들어서 스펙을 볼 필요 없이 쉽기 때문에 HTML을 잘 한다고 스킬로 쓸 수 없는 듯 하지만, 실제로 HTML 을 잘 안다는 것은 A4 용지 800 페이지 분량의 스펙을 잘 안다는 이야기로 판단해야 할 수도 있기에, 잘 하는 언어 스킬로 넣어도 되지 않나 생각이 됩니다.

장난끼가 발동해서 HTML 도 프로그래밍 가능한 언어로 만들어 버릴까 생각했는데, 
"연말은 휴식을 취하며 내년을 준비하자"라는 게으름이 밀려와서, 대략 아이디어만 공유해봅니다.

HTML SPEC 에서 확인할 수 있듯이 HTML 로 커스텀 태그를 만들 수 있답니다.

<programming>

    <declare name="x" value="1" />

    <declare name="y" value="2" />

    <function name="add" parameter="x, y">

        <programming-add return="x+y" />

    </function>

    <declare name="result">

        <exec type="function" target="add">

            <parameter type="variable" target="x" />

            <parameter type="variable" target="y" />

        </exec>

    </declare>

    <exec type="stdio" target="printf">

         <parameter type="string" value="result %d\n" />

         <parameter type="variable" target="result" />

    </exec>

</programming>

https://html.spec.whatwg.org/#custom-elements