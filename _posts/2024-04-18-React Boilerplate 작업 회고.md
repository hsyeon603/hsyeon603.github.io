---
layout: post
title: 'React Boilerplate 작업 회고'
categories: project
tags: [react, toy project, styled-components]
---

안녕하세요, 블로그의 실질적 첫 포스팅을 React Boilerplate 작업에 대한 회고로 작성하게 되었습니다.
보일러플레이트는 계획중이던 토이 프로젝트의 프로젝트 초기 환경 설정을 진행하면서 앞으로 진행할 프로젝트들의 편의성을 위해 만들었습니다. React를 알게 되면서 React에 사용하기 적합한 스타일링 방법이 Sass일지 Styled-components일지 생각을 종종 했었는데 이번 기회에 좀 더 깊게 생각해볼 수 있었습니다. 이 글에서는 Sass 코드를 Styled-components로 재작성하며 겪은 내용을 담고 있습니다.

React 없이 작업하던 기존의 프로젝트들은 보통 CSS나 Sass로 작성하곤 했기에 화면 요소를 생성한 후 스타일을 입히는 방식에서 스타일이 입혀진 화면 요소를 생성한다는 Styled-components의 CSS in JS 기조가 새롭게 다가왔습니다. 가장 먼저 든 의문은 '그럼 class를 사용하지 않는 건가?' 하는 생각이었습니다. CSS나 Sass는 보통 class나 id를 기준으로 코드를 작성하기 때문이었죠. 그래서 무작정 Styled-components 문법대로 컴포넌트를 만들어 화면에 띄워봤습니다.

결과는,

![개발자도구 이미지](/assets/image/image.png)<br/>

클래스명이 무척 아름답네요.

[공식 문서](https://styled-components.com/docs/basics#motivation)를 확인해보니 Styled-components의 클래스명은 고유하며 자동 생성되니 버그는 걱정말라고 합니다. 그렇다니 믿고 작업을 진행했습니다.

---

### 폰트 적용

가장 먼저 '이건 어떻게 하지?' 싶었던 건 폰트 적용입니다. 평소같으면 웹폰트를 head 태그에 넣었겠지만 왠지 느낌적으로 다른 방법을 사용해야 하지 않을까 싶었습니다. Styled-components 문서를 뒤져보니 globalStyle을 적용하는 API를 제공하고 있기에 @font-face로 적용했습니다. 당장은 boilerplate라 사용하는 폰트 수가 적어 파일을 프로젝트에 위치시켰지만, 폰트 수가 많아지는 경우도 고려해봐야할 것 같습니다.

### 문법

Sass는 여러 유용한 문법들을 제공하지만 Styled-components에서는 제공하지 않는 문법들이 있었습니다.

1. 변수 선언: Sass의 변수 선언은 css의 변수 선언으로 작성해 globalStyle에 적용했습니다.

```
<!-- Sass -->
$black: #000;

<!-- Styled-components -->
css`
  :root {
    --color-black: #000;
  }
`
```

2. 내장 함수: percentage와 같은 내장함수는 직접 계산식을 작성했습니다.

```
<!-- Sass -->
width: percentage($i / $md-columns);

<!-- Styled-components -->
width: ${($mdCount / grid.lgColumns) * 100}%;
```

3. 반복문: 개인적으로 반복문을 무척 유용하게 썼는데, Styled-components는 지원하지 않더군요.. 최선이 아닐 수 있지만 일단 props를 활용해봤습니다.

```
<!-- Sass -->
@for $i from 1 through $sm-columns {
    .col-sm-#{$i} {
      width: percentage($i / $sm-columns);
    }
  }

<!-- Styled-components -->
${({ theme, $mdCount }) =>
    $mdCount &&
    theme.responsive.md(`
    width: ${($mdCount / grid.lgColumns) * 100}%;
  `)}
```

4. mixin: Sass에서 mixin은 재사용 가능한 스타일을 정의하기 위한 방법입니다. position center를 일일이 적용하던 시절로 돌아갈 순 없기에 Styled-components 문서를 다시 뒤져보니 ThemeProvider라는 개념을 찾았습니다. 컴포넌트를 최상위 컴포넌트에 적용하고, 컴포넌트 트리 어디서든 호출할 수 있다는 내용에 옳다구나 싶어 바로 적용했습니다.<br/>

```
<!-- Sass -->
@mixin pos-center($type: absolute) {
  @if ($type == fixed or $type == absolute) {
    position: $type;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
}

<!-- Styled-components -->
export const position = {
  center: (type: PositionValue = "absolute") => `
		position: ${type};
		left: 50%;
		top: 50%;
		transform: translate(-50%, -50%);
	`,
};
```

### 그 외

웹브라우저의 기본 스타일을 초기화하기 위해 적용하는 reset.css나 normalize.css와 같은 경우 각각 [styled-reset](https://www.npmjs.com/package/styled-reset), [styled-normalize](https://www.npmjs.com/package/styled-normalize) 라이브러리를 활용하면 편리합니다. 저는 normalize만 적용해 globalStyle에 추가했습니다.

---

###

작업해놓고 보니 과연 이게 잘 만든 보일러플레이트인가 싶은 생각이 들지만, 쓰면서 더 좋은 방법을 알게 되면 차차 고쳐나가려 합니다. 보일러플레이트 소스코드는 [여기](https://github.com/hsyeon603/boilerplate-react)에서 확인하실 수 있습니다. 잘못된 내용이 나 개선 방안이 있다면 깃헙 이슈로 작성해주시면 감사하겠습니다.

여기까지 읽어주셔서 감사합니다!
