---
title: CSS 디버깅
slug: Learn/CSS/Building_blocks/Debugging_CSS
translation_of: Learn/CSS/Building_blocks/Debugging_CSS
---
<div>{{LearnSidebar}}{{PreviousMenuNext("Learn/CSS/Building_blocks/Styling_tables", "Learn/CSS/Building_blocks/Organizing", "Learn/CSS/Building_blocks")}}</div>

<p>때로는 CSS 를 작성할 때 CSS 가 예상한 대로 동작하지 않는 문제가 발생합니다. 아마도 특정 선택자가 요소와 일치해야 하지만, 아무일도 일어나지 않거나 박스의 크기가 예상과 다릅니다. 이 기사에서는 CSS 문제를 디버깅하는 방법에 대한 지침을 제공하고 모든 최신 브라우저에 포함된 DevTools 가 진행 상황을 찾는 데 어떻게 도움이 되는지 보여줍니다.</p>

<table class="learn-box standard-table">
    <tbody>
    <tr>
        <th scope="row">전제조건:</th>
        <td>
            <p>기본 컴퓨터 활용 능력, <a href="https://developer.mozilla.org/en-US/Learn/Getting_started_with_the_web/Installing_basic_software">기본 소프트웨어 설치</a>, <a href="https://developer.mozilla.org/en-US/Learn/Getting_started_with_the_web/Dealing_with_files">파일 작업</a> 에 대한 기본 지식,  HTML 기본 사항 (<a href="/en-US/docs/Learn/HTML/Introduction_to_HTML">HTML 소개</a> 학습) 및 , CSS 작동 방식 이해 (<a href="/en-US/docs/Learn/CSS/First_steps">CSS 첫 번째 단계</a> 학습)</p>
        </td>
    </tr>
    <tr>
        <th scope="row">목적:</th>
        <td>브라우저 DevTools 의 기본 사항과 CSS 의 간단한 검사 및 편집방법 배우기.</td>
    </tr>
    </tbody>
</table>

<h2 id="브라우저_개발자_도구_DevTools_사용_방법">브라우저 개발자 도구 (DevTools) 사용 방법</h2>

<p> <a href="/en-US/docs/Learn/Common_questions/What_are_browser_developer_tools">브라우저 개발자 도구란</a> 기사는 다양한 브라우저 및 플랫폼에서 도구를 사용하는 방법을 설명하는 최신 안내서입니다. 대부분 특정 브라우저에서 개발하도록 선택할 수 있으므로, 해당 브라우저에 포함된 도구에 가장 익숙해지지만, 다른 브라우저에서 해당 도구에 액세스하는 방법을 알아야합니다. 여러 브라우저간에 다른 렌더링이 표시되는 경우 도움이됩니다.</p>

<p>또한 DevTools 를 작성할 때 브라우저가 다른 영역에 집중하도록 선택했음을 알 수 있습니다. 예를 들어, Firefox 에는 CSS 레이아웃으로 시각적으로 작업하기위한 훌륭한 도구가 있으며, <a href="/en-US/docs/Tools/Page_Inspector/How_to/Examine_grid_layouts">그리드 레이아웃</a>, <a href="/en-US/docs/Tools/Page_Inspector/How_to/Examine_Flexbox_layouts">Flexbox</a> 및 <a href="/en-US/docs/Tools/Page_Inspector/How_to/Edit_CSS_shapes">Shapes</a> 를 검사하고 편집할 수 있습니다. 그러나, 모든 브라우저마다 유사한 기본 도구가 있습니다. 예: 페이지의 요소에 적용된 속성 및 값을 검사하고 편집기에서 변경하는 데 사용됩니다.</p>

<p>이 수업에서는 CSS 작업을 위한 Firefox DevTools 의 유용한 기능을 살펴봅니다. 이를 위해 <a href="https://mdn.github.io/css-examples/learn/inspecting/inspecting.html">예제 파일</a> 을 사용하겠습니다. 따라하고 싶다면, 새 탭에 로드하고 위에 링크된 기사에 설명된대로 DevTools 를 여십시오.</p>

<h2 id="DOM_vs_소스_보기">DOM vs 소스 보기</h2>

<p>새로운 사용자를 개발자 도구 (DevTools) 로 체험할 수 있는 것은  웹 페이지의 <a href="/en-US/docs/Tools/View_source">소스 보기</a> 를 보거나 서버에 넣은 HTML 파일을 볼 때 표시되는 것과 개발자 도구의 <a href="/en-US/docs/Tools/Page_Inspector/UI_Tour#HTML_pane">HTML 창</a>에서 볼 수 있는 것의 차이입니다. 소스 보기를 통해 볼 수 있는 것과 거의 비슷해 보이지만 몇 가지 차이점이 있습니다.</p>

<p>렌더링 된 DOM 에서 브라우저가 잘못 작성된 HTML 를 수정했을 수 있습니다. <code>&lt;h2&gt;</code> 를 열고 <code>&lt;/h3&gt;</code>, 로 닫는 등의 요소를 잘못 닫은 경우, 브라우저는 수행하려는 작업을 파악하고 DOM 의 HTML 은 <code>&lt;h2&gt;</code> 를 <code>&lt;/h2&gt;</code> 로 올바르게 닫습니다. 또한 브라우저에서는 모든 HTML 를 표준화하고, DOM 은 JavaScript 로 변경한 내용도 표시합니다.</p>

<p>소스 보기는 서버에 저장된 HTML 소스 코드입니다. 개발자 도구의 <a href="/en-US/docs/Tools/Page_Inspector/How_to/Examine_and_edit_HTML#HTML_tree">HTML tree</a> 는 특정 시점에 브라우저가 렌더링하는 내용을 정확하게 보여주므로, 실제로 진행중인 상황에 대한 통찰력을 제공합니다.</p>

<h2 id="적용된_CSS_검사">적용된 CSS 검사</h2>

<p>페이지에서 요소를 마우스 오른쪽/ctrl 키를 누른 채 클릭하고 <em><strong>검사 (Inspect)</strong> </em>를 선택하거나, 개발자 도구 디스플레이 왼쪽의 HTML tree 에서 요소를 선택하십시오. <code>box1</code> class 의 요소를 선택하십시오; 이것은 테두리 박스가 그려진 페이지의 첫 번째 요소입니다.</p>

<p><img alt="The example page for this tutorial with DevTools open." src="https://mdn.mozillademos.org/files/16606/inspecting1.png" style="border-style: solid; border-width: 1px; height: 1527px; width: 2278px;"></p>

<p>HTML 오른쪽의 <a href="/en-US/docs/Tools/Page_Inspector/UI_Tour#Rules_view">Rules view</a>를 보면 해당 요소에 적용된 CSS 속성과 값을 볼 수 있습니다. <code>box1</code>클래스에 직접 적용된 규칙과 상자가 조상으로부터 상속받은 CSS(이 경우 <code>&lt;body&gt;</code>)를 볼 수 있습니다. 이것은 예상하지 못한 일부 CSS가 적용되는 경우에 유용합니다. 아마도 부모 요소에서 상속되고 있고 이 요소의 컨텍스트에서 덮어쓰는 규칙을 추가해야 합니다.</p>

<p>또한 속기 속성을 확장하는 기능도 유용합니다. 이 예에서는 <code>margin</code> 약어가 사용되었습니다.</p>

<p><strong>보기를 확장하려면 작은 화살표를 클릭하여 다양한 속성과 해당 값을 표시합니다.</strong></p>

<p><strong>해당 패널이 활성 상태일 때 규칙 보기에서 값을 켜고 끌 수 있습니다. 이 패널 위에 마우스를 가져가면 확인란이 나타납니다. <code>border-radius</code>와 같은 규칙의 확인란을 선택 취소하면 CSS가 적용을 중지합니다.</strong></p>

<p>예를 들어 레이아웃이 잘못되어 어떤 속성이 문제일때 이것을 사용하여 A/B 비교를 수행하여 규칙을 적용했을 때 더 나은지 여부를 결정하고 디버그에 도움을 줄 수 있습니다.</p>

<h2 id="Editing_values">값 편집</h2>

<p>속성을 켜고 끄는 것 외에도 해당 값을 편집할 수 있습니다. 다른 색상이 더 잘 보이는지 확인하고 싶거나 크기를 조정하고 싶습니까? DevTools를 사용하면 스타일시트를 편집하고 페이지를 다시 로드하는 데 많은 시간을 절약할 수 있습니다.</p>

<p><strong><code>box1</code>을 선택한 상태에서 테두리에 적용된 색상을 표시하는 견본(색상이 지정된 작은 원)을 클릭합니다. 색상 선택기가 열리고 몇 가지 다른 색상을 시도할 수 있습니다. 페이지에서 실시간으로 업데이트됩니다. 비슷한 방식으로 테두리의 너비나 스타일을 변경할 수 있습니다.</strong></p>

<p><img alt="DevTools Styles Panel with a color picker open." src="https://mdn.mozillademos.org/files/16607/inspecting2-color-picker.png" style="border-style: solid; border-width: 1px; height: 1173px; width: 2275px;"></p>

<h2 id="Adding_a_new_property">새 속성 추가</h2>

<p>DevTools를 사용하여 속성을 추가할 수 있습니다. 상자가 <code>&lt;body&gt;</code> 요소의 글꼴 크기를 상속하는 것을 원하지 않고 고유한 특정 크기를 설정하기를 원합니까? CSS 파일에 추가하기 전에 DevTools에서 이것을 시도할 수 있습니다.</p>

<p><strong>규칙에서 닫는 중괄호를 클릭하여 새 선언 입력을 시작할 수 있습니다. 이 시점에서 새 속성 입력을 시작할 수 있으며 DevTools는 일치하는 속성의 자동 완성 목록을 표시합니다. <code>font-size</code>를 선택한 후 시도하려는 값을 입력합니다. + 버튼을 클릭하여 동일한 선택기로 규칙을 추가하고 거기에 새 규칙을 추가할 수도 있습니다.</strong></p>

<p><img alt="The DevTools Panel, adding a new property to the rules, with the autocomplete for font- open" src="https://mdn.mozillademos.org/files/16608/inspecting3-font-size.png" style="border-style: solid; border-width: 1px; height: 956px; width: 2275px;"></p>

<div class="blockIndicator note">
    <p><strong>참고</strong>: 규칙 보기에는 다른 유용한 기능도 있습니다. 예를 들어 유효하지 않은 값이 있는 선언에는 줄이 그어 표시됩니다. 자세한 내용은 <a href="/en-US/docs/Tools/Page_Inspector/How_to/Examine_and_edit_CSS">CSS 검사 및 수정</a>에서 확인할 수 있습니다.</p>
</div>

<h2 id="Understanding_the_box_model">box model 이해</h2>

<p>이전 강의에서 <a href="/en-US/docs/Learn/CSS/Building_blocks/The_box_model">박스 모델</a>과 변경되는 대체 상자 모델이 있다는 사실에 대해 논의했습니다. 요소의 크기가 요소에 제공한 크기와 패딩 및 테두리를 기반으로 계산되는 방식입니다. DevTools는 요소의 크기가 어떻게 계산되는지 이해하는 데 실제로 도움이 될 수 있습니다.</p>

<p><a href="/en-US/docs/Tools/Page_Inspector/UI_Tour#Layout_view">레이아웃 보기</a>는 선택한 요소의 상자 모델 다이어그램과 함께 요소가 배치되는 방식을 변경하는 속성 및 값. 여기에는 요소에 명시적으로 사용하지 않았지만 초기 값이 설정된 속성에 대한 설명이 포함됩니다.</p>

<p>이 패널에서 세부 속성 중 하나는 요소가 사용하는 상자 모델을 제어하는 <code>box-sizing</code> 속성입니다.</p>

<p><strong><code>box1</code> 및 <code>box2</code> 클래스가 있는 두 상자를 비교합니다. 둘 다 동일한 너비(400px)가 적용되지만 <code>box1</code>이 시각적으로 더 넓습니다. 레이아웃 패널에서 <code>content-box</code>를 사용하고 있는 것을 볼 수 있습니다. 이것은 요소에 지정한 크기에 패딩 및 테두리 너비를 추가하는 값입니다.</strong></p>

<p><code>box2</code> 클래스가 있는 요소는 <code>border-box</code>를 사용하므로 여기에서 요소에 지정한 크기에서 패딩과 테두리를 뺍니다. 즉, 상자가 페이지에서 차지하는 공간이 지정한 정확한 크기(이 경우 <code>width: 400px</code>)입니다.</p>

<p><img alt="The Layout section of the DevTools" src="https://mdn.mozillademos.org/files/16609/inspecting4-box-model.png" style="border-style: solid; border-width: 1px; height: 1532px; width: 2275px;"></p>

<div class="blockIndicator note">
    <p><strong>참고</strong>: <a href="/en-US/docs/Tools/Page_Inspector/How_to/Examine_and_edit_the_box_model">박스 모델 검사 및 검사</a>에서 자세히 알아보십시오.</p>
</div>

<h2 id="Solving_specificity_issues">특이성 문제 해결</h2>

<p>개발 중에, 특히 기존 사이트에서 CSS를 편집해야 하는 경우 일부 CSS를 적용하는 데 어려움을 겪을 수 있습니다. 무엇을 하든지 요소는 CSS를 사용하지 않는 것 같습니다. 여기에서 일반적으로 발생하는 것은 보다 구체적인 선택기가 변경 사항을 재정의한다는 것입니다. 여기서 DevTools가 정말 도움이 될 것입니다.</p>

<p>예제 파일에는 <code>&lt;em&gt;</code> 요소로 래핑된 두 단어가 있습니다. 하나는 주황색으로 표시되고 다른 하나는 핫핑크로 표시됩니다. 우리가 적용한 CSS:</p>

<pre class="brush: css">em {
  color: hotpink;
  font-weight: bold;
}</pre>

<p>스타일시트의 그 위에는 <code>.special</code> 선택기가 있는 규칙이 있습니다.</p>

<pre class="brush: css">.special {
  color: orange;
}</pre>

<p>특정성에 대해 논의한 <a href="/en-US/docs/Learn/CSS/Building_blocks/Cascade_and_inheritance">캐스케이드 및 상속</a> 강의에서 기억하시겠지만, 클래스 선택기는 다음보다 더 구체적입니다. 요소 선택기이므로 이것이 적용되는 값입니다. DevTools는 특히 정보가 거대한 스타일시트의 어딘가에 묻혀 있는 경우 이러한 문제를 찾는 데 도움이 될 수 있습니다.</p>

<p><strong><code>.special</code> 클래스로 <code>&lt;em&gt;</code>을 검사하면 DevTools에서 주황색이 적용되는 색상임을 보여주고 또한 em에 적용된 <code>color</code> 속성은 그어진 선으로 표시됩니다. 이제 클래스가 요소 선택기를 재정의하는 것을 볼 수 있습니다.</strong></p>

<p><img alt="Selecting an em and looking at DevTools to see what is over-riding the color." src="https://mdn.mozillademos.org/files/16610/inspecting5-specificity.png" style="border-style: solid; border-width: 1px; height: 1161px; width: 2275px;"></p>

<h2 id="Find_out_more_about_the_Firefox_DevTools">Firefox DevTools에 대해 자세히 알아보기</h2>

<p>여기 MDN에 Firefox DevTools에 대한 많은 정보가 있습니다. 주요 <a href="/en-US/docs/Tools">DevTools 섹션</a>을 살펴보고 이 강의에서 간략하게 다룬 내용에 대한 자세한 내용은 <a href="/en -US/docs/Tools/Page_Inspector#How_to">방법 안내</a>.</p>

<h2 id="Debugging_problems_in_CSS">CSS의 디버깅 문제</h2>

<p>DevTools는 CSS 문제를 해결할 때 큰 도움이 될 수 있으므로 CSS가 예상대로 작동하지 않는 상황에 처했을 때 어떻게 해결해야 할까요? 다음 단계가 도움이 될 것입니다.</p>

<h3 id="Take_a_step_back_from_the_problem">문제에서 한발 물러나서</h3>

<p>코딩 문제, 특히 CSS 문제는 좌절감을 줄 수 있습니다. 솔루션을 찾는 데 도움이 되는 온라인 검색 오류 메시지가 표시되지 않는 경우가 많기 때문입니다. 답답해지면 잠시 문제에서 한 발짝 떨어져 보세요. 산책을 하거나, 술을 마시거나, 동료와 이야기를 나누거나, 잠시 동안 다른 일을 하십시오. 문제에 대한 생각을 멈추면 솔루션이 마술처럼 나타나는 경우가 있고, 그렇지 않더라도 기분이 상쾌할 때 작업하기가 훨씬 수월합니다.</p>

<h3 id="Do_you_have_valid_HTML_and_CSS">유효한 HTML과 CSS가 있습니까?</h3>

<p>브라우저는 CSS와 HTML이 올바르게 작성되기를 기대하지만, 브라우저도 매우 관대하며 마크업이나 스타일시트에 오류가 있는 경우에도 웹페이지를 표시하기 위해 최선을 다할 것입니다. 코드에 실수가 있는 경우 브라우저는 의도한 바를 추측해야 하며 사용자가 염두에 둔 것과 다른 결정을 내릴 수 있습니다. 또한 두 가지 다른 브라우저가 두 가지 다른 방법으로 문제에 대처할 수 있습니다. 따라서 좋은 첫 번째 단계는 유효성 검사기를 통해 HTML 및 CSS를 실행하여 오류를 찾아 수정하는 것입니다.</p>

<ul>
    <li><a href="https://jigsaw.w3.org/css-validator/">CSS Validator</a></li>
    <li><a href="https://validator.w3.org/">HTML validator</a></li>
</ul>

<h3 id="Is_the_property_and_value_supported_by_the_browser_you_are_testing_in">테스트 중인 브라우저에서 속성과 값을 지원합니까?</h3>

<p>브라우저는 이해하지 못하는 CSS를 무시합니다. 사용 중인 속성이나 값이 테스트 중인 브라우저에서 지원되지 않으면 아무 것도 중단되지 않지만 해당 CSS는 적용되지 않습니다. DevTools는 일반적으로 어떤 방식으로든 지원되지 않는 속성과 값을 강조 표시합니다. 아래 스크린샷에서 브라우저는 {{cssxref("grid-template-columns")}}의 하위 그리드 값을 지원하지 않습니다.</p>

<p><img alt="Image of browser DevTools with the grid-template-columns: subgrid crossed out as the subgrid value is not supported." src="https://mdn.mozillademos.org/files/16641/no-support.png" style="height: 397px; width: 1649px;"></p>

<p>또한 MDN의 각 속성 페이지 하단에 있는 브라우저 호환성 표를 볼 수 있습니다. 이것들은 해당 속성에 대한 브라우저 지원을 보여주며, 속성의 일부 사용에 대한 지원이 있고 다른 것은 지원하지 않는 경우 종종 세분화됩니다. 아래 표는 {{cssxref("shape-outside")}} 속성에 대한 호환성 데이터를 보여줍니다.</p>

<p>{{compat("css.shape-outside")}}</p>

<h3 id="Is_something_else_overriding_your_CSS">다른 것이 CSS를 무시합니까?</h3>

<p>여기서 특정성에 대해 배운 정보가 매우 유용할 것입니다. 당신이 하려고 하는 것을 무시하는 더 구체적인 무언가가 있다면, 당신은 무엇을 해결하려고 하는 매우 실망스러운 게임에 들어갈 수 있습니다. 그러나 위에서 설명한 대로 DevTools는 CSS가 적용되는 내용을 보여주고 이를 재정의하기에 충분히 구체적인 새 선택기를 만드는 방법을 알아낼 수 있습니다.</p>

<h3 id="Make_a_reduced_test_case_of_the_problem">문제의 축소된 테스트 사례 만들기</h3>

<p>위 단계를 수행해도 문제가 해결되지 않으면 추가 조사가 필요합니다. 이 시점에서 가장 좋은 것은 축소된 테스트 케이스로 알려진 것을 만드는 것입니다. "문제를 줄인다"는 것은 정말 유용한 기술입니다. 자신과 동료의 코드에서 문제를 찾는 데 도움이 되며 버그를 보고하고 더 효과적으로 도움을 요청할 수 있습니다.</p>

<p>축소된 테스트 사례는 관련 없는 주변 콘텐츠와 스타일이 제거된 가장 간단한 방법으로 문제를 보여주는 코드 예제입니다. 이는 종종 레이아웃에서 문제가 있는 코드를 제거하여 해당 코드나 기능만 보여주는 작은 예제를 만드는 것을 의미합니다.</p>

<p>축소된 테스트 사례를 만들려면:</p>

<ol>
    <li>마크업이 동적으로 생성되는 경우(예: CMS를 통해) 문제를 보여주는 출력의 정적 버전을 만드십시오. <a href="https://codepen.io/">CodePen</a>과 같은 코드 공유 사이트는 축소된 테스트 사례를 호스팅하는 데 유용합니다. 그러면 온라인에서 액세스할 수 있고 동료와 쉽게 공유할 수 있기 때문입니다. 페이지에서 소스 보기를 수행하고 HTML을 CodePen에 복사한 다음 관련 CSS 및 JavaScript를 가져와서 포함할 수 있습니다. 그 후에도 문제가 여전히 분명한지 확인할 수 있습니다.</li>
    <li>JavaScript를 제거해도 문제가 해결되지 않으면 JavaScript를 포함하지 마십시오. JavaScript를 제거해도 문제가 <em>해결되지 않는</em> 경우 가능한 한 많은 JavaScript를 제거하고 문제의 원인은 그대로 두십시오.</li>
    <li>문제에 기여하지 않는 HTML을 제거하십시오. 구성 요소 또는 레이아웃의 주요 요소를 제거합니다. 다시 말하지만, 여전히 문제를 보여주는 가장 작은 양의 코드를 찾아보십시오.</li>
    <li>문제에 영향을 주지 않는 CSS를 제거하세요.</li>
</ol>

<p>이 작업을 수행하는 과정에서 문제의 원인을 발견하거나 최소한 특정 항목을 제거하여 문제를 켜고 끌 수 있습니다. 무언가를 발견할 때 코드에 몇 가지 주석을 추가할 가치가 있습니다. 도움을 요청해야 하는 경우 이미 시도한 내용을 도움을 주는 사람에게 보여줄 것입니다. 이렇게 하면 들리는 문제와 해결 방법을 검색할 수 있는 충분한 정보를 얻을 수 있습니다.</p>

<p>여전히 문제를 해결하는 데 어려움을 겪고 있다면 테스트 케이스를 축소하면 포럼에 게시하거나 동료에게 보여줌으로써 도움을 요청할 수 있습니다. 도움을 요청하기 전에 문제를 줄이고 문제가 발생한 위치를 정확히 식별하는 작업을 완료했음을 보여줄 수 있다면 도움을 받을 가능성이 훨씬 더 높습니다. 경험이 더 많은 개발자는 문제를 신속하게 찾아내고 올바른 방향으로 안내할 수 있으며, 그렇지 않더라도 축소된 테스트 사례를 통해 빠르게 살펴보고 최소한 도움을 줄 수 있기를 바랍니다.</ 피>

<p>문제가 실제로 브라우저의 버그인 경우 축소된 테스트 사례를 사용하여 관련 브라우저 공급업체에 버그 보고서를 제출할 수도 있습니다(예: Mozilla의 <a href="https://bugzilla .mozilla.org">bugzilla 사이트</a>).</p>

<p>CSS에 대한 경험이 많을수록 문제를 더 빨리 파악하게 될 것입니다. 그러나 우리 중 가장 경험이 많은 사람들조차도 때때로 도대체 무슨 일이 일어나고 있는지 궁금해합니다. 체계적인 접근 방식을 취하고 축소된 테스트 사례를 만들고 다른 사람에게 문제를 설명하면 일반적으로 수정 사항을 찾을 수 있습니다.</p>

<p>{{PreviousMenuNext("Learn/CSS/Building_blocks/Styling_tables", "Learn/CSS/Building_blocks/Organizing", "Learn/CSS/Building_blocks")}}</p>

<h2 id="In_this_module">In this module</h2>

<ol>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Cascade_and_inheritance">Cascade and inheritance</a></li>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Selectors">CSS selectors</a>
        <ul>
            <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Selectors/Type_Class_and_ID_Selectors">Type, class, and ID selectors</a></li>
            <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Selectors/Attribute_selectors">Attribute selectors</a></li>
            <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Selectors/Pseudo-classes_and_pseudo-elements">Pseudo-classes and pseudo-elements</a></li>
            <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Selectors/Combinators">Combinators</a></li>
        </ul>
    </li>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/The_box_model">The box model</a></li>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Backgrounds_and_borders">Backgrounds and borders</a></li>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Handling_different_text_directions">Handling different text directions</a></li>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Overflowing_content">Overflowing content</a></li>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Values_and_units">Values and units</a></li>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Sizing_items_in_CSS">Sizing items in CSS</a></li>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Images_media_form_elements">Images, media, and form elements</a></li>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Styling_tables">Styling tables</a></li>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Debugging_CSS">Debugging CSS</a></li>
    <li><a href="/en-US/docs/Learn/CSS/Building_blocks/Organizing">Organizing your CSS</a></li>
</ol>