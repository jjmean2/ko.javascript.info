
# 모듈 소개

개발하는 애플리케이션의 크기가 커질수록 파일을 여러 개로 쪼개야 할 필요가 생깁니다. 이 때 쪼개진 파일 각각을 '모듈'이라고 부릅니다. 모듈은 대게 클래스 하나 혹은 복수의 함수로 구성된 라이브러리 하나로 구성됩니다.

자바스크립트 생태계는 오랫동안 언어 수준에서 지원하는 모듈 관련 문법 없이 성장해 왔는데, 이 점이 문제가 되진 않았습니다. 초기 스크립트는 크기도 작고 간단했기 때문에 모듈의 필요성이 대두되지 않았기 때문입니다.

하지만 스크립트의 크기가 점차 커지고 복잡해지기 시작하자, 자바스크립트 커뮤니티는 모듈이라는 개념을 도입해 코드를 체계화하려는 여러 가지 시도를 하게 됩니다. 필요할 때마다 언제든지 모듈을 로드해 주는 특별한 라이브러리를 사용해서 말이죠.

예:

- [AMD](https://en.wikipedia.org/wiki/Asynchronous_module_definition)는 가장 오래된 모듈 시스템 중 하나입니다. [require.js](http://requirejs.org/)라는 라이브러리를 통해 처음 개발되었습니다.
- [CommonJS](http://wiki.commonjs.org/wiki/Modules/1.1)는 Node.js 서버를 위해 만들어진 모듈 시스템입니다.
- [UMD](https://github.com/umdjs/umd)는 여러 모듈 시스템을 함께 사용하기위해 만들어졌습니다. AMD와 CommonJS와 호환됩니다.

위 모듈 시스템은 오래된 스크립트에서 찾아볼 수 있는데, 이제는 역사의 뒤안길로 사라져가고 있습니다.

2015년에 모듈 시스템이 표준에 등재되었기 때문입니다. 이때 이후로 모듈 관련 문법이 계속 진화를 거듭하였고, 이제는 대부분의 주요 브라우저와 Node.js에서 모듈 시스템을 지원합니다. 이제 모듈이 뭔지 본격적으로 알아보도록 합시다.  

## 모듈이란?

모듈은 단순히 하나의 파일을 의미합니다. 스크립트 하나가 바로 하나의 모듈이 됩니다.

한 모듈에서 다른 모듈을 불러올 수 있습니다. 특수한 지시자 `export`와 `import`를 이용하면 한 모듈에서 만든 기능을 다른 모듈에서 불러올 수 있어 모듈 간 기능을 공유할 수 있습니다. 

- `export` 키워드를 변수나 함수 앞에 붙이면, 파일 외부에서 현재 모듈의 변수나 함수에 접근할 수 있습니다.
- `import` 키워드는 다른 모듈에서 기능을 가져올 때 사용합니다.

파일 `sayHi.js` 내부에 있는 함수를 외부로 내보내는(export) 예시를 살펴보도록 하죠.

```js
// 📁 sayHi.js
export function sayHi(user) {
  alert(`Hello, ${user}!`);
}
```

이제 다른 파일에서 위 함수를 가져와(import) 사용할 수 있습니다.

```js
// 📁 main.js
import {sayHi} from './sayHi.js';

alert(sayHi); // 함수
sayHi('John'); // Hello, John!
```

본 튜토리얼은 실행 환경에 상관없이 자바스크립트 언어 자체에 대해 다루고자 하였으나, 현재는 브라우저를 데모 환경으로 사용 중이므로, 브라우저에서 어떻게 모듈을 사용하는지 알아보도록 하겠습니다.

모듈은 특별한 키워드와 기능을 제공하므로, `<script type="module">`을 사용해 해당 스크립트가 모듈인지의 여부를 브라우저에게 알려주어야 합니다.

As modules support special keywords and features, we must tell the browser that a script should be treated as module, by using the attribute `<script type="module">`.

Like this:

[codetabs src="say" height="140" current="index.html"]

브라우저는 자동으로 해당 모듈을 가져오고 평가한 후, 해당 모듈의 스크립트를 실행합니다.

## 모듈의 핵심 기능

그냥 "일반적인" 스크립트와 모듈의 차이는 무엇일까요?

브라우저와 서버 사이드 자바스크립트 환경에서 모두 적용되는 모듈의 기능은 다음과 같습니다. 

### "엄격 모드"로 실행됩니다

모듈엔 항상 기본적으로 `엄격 모드가 적용(use strict)`됩니다. 그래서 선언되지 않은 변수에 값을 할당하면 에러가 발생합니다. 

```html run
<script type="module">
  a = 5; // error
</script>
```

### 모듈 수준 스코프

각 모듈은 자기만의 스코프를 갖습니다. 모듈 내부에서 정의한 변수와 함수는 다른 스크립트(모듈)에서 접근할 수 없습니다.

아래 예시를 통해 이를 알아봅시다. 두 개의 스크립트를 임포트 한 상황입니다. `user.js`에서 선언한 변수 `user`를 `hello.js`에서 사용하려고 하니 에러가 발생합니다.

[codetabs src="scopes" height="140" current="index.html"]

외부에 공개하려는 모듈은 `export` 해줘야 하고, 내보내진(export) 모듈을 가져와 사용하려면 `import` 해줘야 합니다.

`user.js`를 `index.html`가 아닌 `hello.js` 안에서 바로 import 해줘야 원하는 대로 `user`를 사용할 수 있습니다.

이제 정상적으로 동작하네요.

[codetabs src="scopes-working" height="140" current="hello.js"]

브라우저 환경에서도 각 `<script type="module">` 마다 독립적인 모듈 스코프가 존재합니다.

```html run
<script type="module">
  // user는 모듈 안에서만 접근 가능합니다
  let user = "John";
</script>

<script type="module">
  *!*
  alert(user); // Error: user is not defined
  */!*
</script>
```

부득이하게 브라우저 환경에서 window 레벨의 "전역(global)" 변수를 사용해야 한다면, `window` 객체에 변수를 명시적으로 할당하고, `window.user`로 접근하면 됩니다. 하지만 정말 필요한 경우에만 이 방법을 사용하길 권유합니다.

### 모듈은 최초 임포트 시 단 한 번만 평가됩니다.

동일 모듈이 여러 곳에서 사용되더라도 모듈 내 코드는 최초 1회만 실행됩니다. 최초 실행 이후 다른 모듈에서 이 모듈을 임포트 합니다. 

이런 작동방식은 중요한 결과를 초래합니다. 예제로 알아봅시다.

alert 창을 띄워주는 코드를 담고 있는 모듈을 여러 곳에서 임포트 해보았습니다. alert 창은 단 한 번만 나타납니다.

```js
// 📁 alert.js
alert("Module is evaluated!");
```

```js
// 같은 모듈을 여러 파일에서 임포트함

// 📁 1.js
import `./alert.js`; // 모듈이 평가됩니다!

// 📁 2.js
import `./alert.js`; // (아무 일도 발생하지 않음)
```

현업에서 최상위 레벨 모듈은 대게 초기화 용도로 사용합니다. 재사용하고 싶은 무언가에 대한 데이터 구조를 만들고, 그 구조에 내용을 미리 채워 넣어 모듈을 만듭니다. 그리고 이 모듈을 내보내죠.

좀 더 어려운 예제를 살펴보도록 하겠습니다.

이 모듈에선 객체를 내보내고 있습니다.

```js
// 📁 admin.js
export let admin = {
  name: "John"
};
```

여러 파일에서 이 모듈을 임포트해도 앞에서 설명한바 처럼 모듈은 한 번만 평가됩니다. `admin` 객체가 하나 만들어지고, 이 모듈을 임포트 하는 모든 파일에 `admin` 객체가 전달됩니다.

각 파일에서 전달받은 `admin` 객체는 유일무이합니다. 모두 동일한 객체를 받습니다.

```js
// 📁 1.js
import {admin} from './admin.js';
admin.name = "Pete";

// 📁 2.js
import {admin} from './admin.js';
alert(admin.name); // Pete

*!*
// 1.js와 2.js 모두 같은 객체를 받습니다.
// 1.js에서 변화된 객체가 2.js에서도 보이는군요.
*/!*
```

So, let's reiterate -- the module is executed only once. Exports are generated, and then they are shared between importers, so if something changes the `admin` object, other modules will see that.

Such behavior allows to *configure* modules on first import. We can setup its properties once, and then in further imports it's ready.

For instance, `admin.js` module may provide certain functionality, but expect the credentials to come into the `admin` object from outside:

```js
// 📁 admin.js
export let admin = { };

export function sayHi() {
  alert(`Ready to serve, ${admin.name}!`);
}
```

In `init.js`, the first script of our app, we set `admin.name`. Then everyone will see it, including calls made from inside `admin.js` itself:

```js
// 📁 init.js
import {admin} from './admin.js';
admin.name = "Pete";
```

Another module can also see `admin.name`:

```js
// 📁 other.js
import {admin, sayHi} from './admin.js';

alert(admin.name); // *!*Pete*/!*

sayHi(); // Ready to serve, *!*Pete*/!*!
```

### import.meta

`import.meta` 객체는 현재 모듈에 대한 정보를 제공합니다.

실행 환경에 따라 정보의 내용은 다릅니다. 브라우저 환경에선 스크립트의 url정보를 가져올 수 있습니다. 스크립트 태그로 가져온 모듈이라면, 현재 웹페이지의 url 정보를 얻을 수 있습니다.

```html run height=0
<script type="module">
  alert(import.meta.url); // script url (인라인 스크립트가 있는 html 페이지의 url)
</script>
```

### In a module, "this" is undefined

사소한 기능이지만, 설명의 완전성을 위해 이 내용을 언급하고 넘어가야 할 것 같습니다.

모듈이 아닌 스크립트의 전역 객체엔 값이 있는데 반해, 모듈 스코프의 `this`는 undefined입니다.

```html run height=0
<script>
  alert(this); // window
</script>

<script type="module">
  alert(this); // undefined
</script>
```

## 브라우저 특정 기능

There are also several browser-specific differences of scripts with `type="module"` compared to regular ones.

자바스크립트 초심자나 브라우저 환경에서 자바스크립트를 사용하지 않고 있다면 이 내용은 넘어가셔도 됩니다.

### 모듈의 지연 실행

Module scripts are *always* deferred, same effect as `defer` attribute (described in the chapter [](info:script-async-defer)), for both external and inline scripts.

따라서 아래와 같은 특성을 보입니다.
- 외부 모듈 스크립트 `<script type="module" src="...">`를 다운로드 할 때 브라우저의 HTML 처리가 중단되지 않습니다. 다른 리소스와 함께 병렬적으로 불러와지죠.
- HTML 문서가 완전히 준비될 때까지 모듈 스크립트는 대기 상태에 있다가, 이후에 실행됩니다. 모듈의 크기가 아주 작아서 HTML보다 빨리 불러와 졌더라도 말이죠.
- 스크립트의 상대적 순서가 유지됩니다. 문서상 위쪽의 스크립트부터 차례로 실행됩니다.

이런 특징 때문에 모듈 스크립트는 항상 로드가 완전히 완료된 HTML 페이지를 "볼 수 있습니다". 모듈 아래에 정의한 HTML 요소에도 접근할 수 있죠.

예시:

```html run
<script type="module">
*!*
  alert(typeof button); // object: 스크립트는 아래쪽의 button 요소를 '참조'할 수 있습니다.
*/!*
  // 모듈 스크립트는 지연 실행되기 때문에, 페이지가 모두 로드되고 난 다음에 실행됩니다.
</script>

Compare to regular script below:

<script>
*!*
  alert(typeof button); // Error: 버튼은 undefined 상태입니다. 해당 요소가 스크립트 아래에 정의되었기 때문입니다.
*/!*
  // 일반 스크립트는 페이지 내 나머지 요소가 처리되기 이전에 실행됩니다.
</script>

<button id="button">Button</button>
```

두 번째 스크립트는 첫 번째 모듈 스크립트보다 먼저 실행된다는 것에 주의하시기 바랍니다. `undefined`가 먼저, `object`는 나중에 출력됩니다.

이는 모듈 스크립트의 지연 실행 때문입니다. 문서 전체가 처리되기 전까지 모듈 스크립트는 실행되지 않습니다. 반면 일반 스크립트는 바로 실행됩니다. 그래서 위와 같은 결과가 나타납니다.

모듈을 사용 하면, HTML 페이지가 로드되고 난 다음에 자바스크립트 모듈이 실행된다는 점에 항상 유의해야 합니다. 사용자는 자바스크립트 애플리케이션이 준비되기 전에 페이지에 노출될 수 있습니다. 페이지 내 어떤 기능은 자바스크립트가 로딩되기 전까지 동작하지 않을 수도 있는데, 이렇게 되면 사용자에게 혼란을 주게 됩니다. 모듈 스크립트를 불러오는 동안, 투명 오버레이나 "로딩 인디케이터(loading indicator)"를 보여주어 사용자의 혼란을 예방해 줍시다. 

### 인라인 스크립트 비동기 처리

async 속성(`<script async type="module">`)은 인라인 스크립트와 외부 스크립트 모두에 적용할 수 있습니다(역주: async속성은 모듈이 아닌 스크립트의 경우, 외부 스크립트 파일을 불러올 때만 유효합니다). async 속성이 붙은 스크립트는 임포트하려는 모듈을 모두 처리하면 다른 스크립트나 HTML 처리와 상관없이 즉시 실행됩니다.

아래 스크립트엔 `async` 속성이 붙었기 때문에, 다른 스크립트나 HTML이 처리되길 기다리지 않습니다.

임포트 작업(`./analytics.js`을 가져옴)이 끝나면 해당 모듈은 바로 실행됩니다. HTML 분석이 끝나지 않거나, 다른 스크립트가 대기 상태에 있더라도 말이죠.

It performs the import (fetches `./analytics.js`) and runs when ready, even if HTML document is not finished yet, or if other scripts are still pending.

That's good for functionality that doesn't depend on anything, like counters, ads, document-level event listeners.

```html
<!-- all dependencies are fetched (analytics.js), and the script runs -->
<!-- doesn't wait for the document or other <script> tags -->
<script *!*async*/!* type="module">
  import {counter} from './analytics.js';

  counter.count();
</script>
```

### 외부 스크립트

외부 모듈 스크립트엔 두 가지 큰 특징이 있습니다.

1. 같은 `src` 속성값을 가진 외부 스크립트는 한 번만 실행됩니다.
    ```html
    <!-- my.js 스크립트는 한번만 페치(fetch) 및 실행됩니다. -->
    <script type="module" src="my.js"></script>
    <script type="module" src="my.js"></script>
    ```

2. 외부 사이트와 같은 다른 오리진에서 모듈 스크립트를 불러오려면 <info:fetch-crossorigin> 챕터에서 설명한 바와 같이 [CORS](mdn:Web/HTTP/CORS) 헤더가 필요합니다. 외부 모듈이 저장되어있는 원격 서버가 페치를 허용함을 나타내는 `Access-Control-Allow-Origin: *` 헤더(`*` 대신 페치를 허용할 도메인을 명시할 수도 있음)를 제공해야만 외부 모듈을 불러올 수 있습니다.
    ```html
    <!-- another-site.com이 Access-Control-Allow-Origin을 지원해야만 외부 모듈을 불러올 수 있습니다.-->
    <!-- 그렇지 않은 경우, 스크립트는 실행되지 않습니다.-->
    <script type="module" src="*!*http://another-site.com/their.js*/!*"></script>
    ```

    이 특징은 보안을 강화해 줍니다.

### "경로가 없는" 모듈은 사용할 수 없습니다

브라우저 환경에선, `import` 지시자는 반드시 상대 혹은 절대 URL 앞에 와야 합니다. 경로가 없는 모듈은 허용되지 않습니다.

아래 예제에서 `import`는 무효합니다.
```js
import {sayHi} from 'sayHi'; // Error!
// './sayHi.js'와 같이 모듈이 어디에 있는지 경로를 지정해 주어야 함
```

Node.js나 번들 툴은 경로가 없는 모듈을 허용합니다. 경로 없이도 해당 모듈을 찾수 있는 방법을 알기 때문이죠. 하지만 브라우저에선 아직까진 경로 없는 모듈을 지원하지 않습니다.

### 호환성을 지원해주는 "nomodule"

오래된 브라우저는 `type="module"`을 해석하지 못합니다. 이 브라우저들은 알려지지 않은 타입의 스크립트를 만나면 이를 무시하고 넘어갑니다. 대비책으로 `nomodule` 속성을 사용하면 호환성을 유지할 수 있습니다.

```html run
<script type="module">
  alert("모던 브라우저를 사용하고 계시군요.");
</script>

<script nomodule>
  alert("type=module을 해석할 수 있는 브라우저는 nomodule속성이 있는 스크립트는 넘어갑니다. 따라서 이 alert문은 실행되지 않습니다.")
  alert("오래된 브라우저를 사용하고 있다면, 이 alert문이 실행됩니다.");
</script>
```

## 빌드 툴

In real-life, browser modules are rarely used in their "raw" form. Usually, we bundle them together with a special tool such as [Webpack](https://webpack.js.org/) and deploy to the production server.

One of the benefits of using bundlers -- they give more control over how modules are resolved, allowing bare modules and much more, like CSS/HTML modules.

Build tools do the following:

1. Take a "main" module, the one intended to be put in `<script type="module">` in HTML.
2. Analyze its dependencies: imports and then imports of imports etc.
3. Build a single file with all modules (or multiple files, that's tunable), replacing native `import` calls with bundler functions, so that it works. "Special" module types like HTML/CSS modules are also supported.
4. In the process, other transforms and optimizations may be applied:
    - Unreachable code removed.
    - Unused exports removed ("tree-shaking").
    - Development-specific statements like `console` and `debugger` removed.
    - Modern, bleeding-edge JavaScript syntax may be transformed to older one with similar functionality using [Babel](https://babeljs.io/).
    - The resulting file is minified (spaces removed, variables replaced with shorter named etc).

번들링 툴을 사용하는 경우, 스크립트는 하나 혹은 여러 개의 파일로 번들링 됩니다. 이때, 번들링 전 스크립트에 있던 `import/export`문은 특별한 번들러 함수로 대체됩니다. 번들링 과정을 거친 스크립트엔 `import/export`가 없기 때문에 `type="module"` 역시 필요 없어집니다. 모듈이 아닌 일반 스크립트로 취급할 수 있게 되죠. 

```html
<!-- bundle.js는 웹팩과 같은 툴로 번들링 과정을 거친 스크립트라고 가정합시다. -->
<script src="bundle.js"></script>
```

That said, native modules are also usable. So we won't be using Webpack here: you can configure it later.

## 요약

지금까지 배운 내용의 핵심을 추려보도록 하겠습니다.

1. 하나의 모듈은 하나의 파일입니다. 브라우저에서 `import/export` 지시자를 사용하려면 `<script type="module">`같은 type 속성이 필요합니다. 모듈은 아래와 같은 특징을 지닙니다.
    - 지연 실행 됩니다.
    - 인라인 모듈 스크립트도 비동기 처리 할 수 있습니다.
    - 다른 오리진(도메인, 프로토콜, 포트)에서 외부 스크립트를 불러오려면, CORS 헤더가 필요합니다.
    - 중복된 외부 스크립트는 무시됩니다.
2. 모듈은 자신만의 스코프를 갖습니다. 모듈간 기능 공유는 `import/export`로 할 수 있습니다.
3. 항상 엄격 모드로 실행(`use strict`)됩니다.
4. 모듈 내 코드는 단 한번만 실행됩니다. 한번 Export된 모듈은 모든 임포터(importer)에서 공유합니다.

대게 각 모듈은 하나의 기능을 담고 있습니다. `export` 키워드를 사용해 이 기능을 해당 모듈 밖에서도 사용할 수 있도록 합니다. 내보내진 기능이 필요한 모듈은 `import` 키워드를 사용해 해당 기능을 사용하게 됩니다. 브라우저에선 모듈을 불러오고 평가하는 과정을 자동으로 수행해 줍니다.

실제 애플리케이션을 출시할 땐 성능 향상 등의 이점 때문에 [웹팩](https://webpack.js.org)과 같은 번들러를 사용합니다.

다음 챕터에선 모듈에 대한 여러 가지 예시를 좀 더 살펴보도록 하겠습니다. 어떻게 모듈을 내보내고 임포트하는지 알아보도록 하겠습니다.
