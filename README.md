* [http://poiemaweb.com/js-spa](http://poiemaweb.com/js-spa)

# 1. Introduction

단일 페이지 어플리케이션(Single Page Application, SPA)는 모던 웹의 패러다임이다.

link tag를 사용하는 전통적인 웹 방식은 요청 시마다 정적 리소스가 다운로드되며 새로고침이 발생된다. SPA는 웹 애플리케이션에 필요한 모든 정적 리소스를 최초에 한번 다운로드하고 이후 사용자와의 인터랙션에 필요한 데이터는 서버로부터 전달받아 전체적인 트래픽을 감소시킨다. 또한 화면 전환 시, 새로고침이 발생하지 않아 네이티브 앱과 유사한 사용자 경험을 제공할 수 있다.

모바일의 사용이 증가하고 있는 현 시점에 트래픽의 감소와 속도, 사용성, 반응성의 향상은 매우 중요한 이슈이다. SPA의 핵심 가치는 사용자 경험(UX)을 향상에 있으며 부가적으로 애플리케이션 속도의 향상도 기대할 수 있어서 모바일 퍼스트(Mobile First) 전략에 부합한다.

모든 소프트웨어 아키텍처에는 trade-off가 존재한다. SPA 또한 모든 애플리케이션에 적합한 은탄환(Silver bullet)은 아니다. SPA가 가지는 구조적인 단점은 아래와 같다.

초기 구동 속도
: SPA는 웹 애플리케이션에 필요한 모든 정적 리소스를 최초에 한번 다운로드하기 때문에 초기 구동 속도가 상대적으로 느리다. 하지만 SPA는 웹페이지보다는 애플리케이션에 적합한 기술이므로 트래픽의 감소와 속도, 사용성, 반응성의 향상 등의 장점을 생각한다면 결정적인 단점이라고 할 수는 없다.

SEO(검색엔진 최적화) 문제
: SPA는 서버 렌더링 방식이 아닌 자바스크립트 기반 비동기 모델(클라이언트 랜더링 방식)이다. 따라서 SEO는 언제나 단점으로 부각되어 왔던 주제이다. 하지만 SPA는 정보의 제공을 위한 웹페이지보다는 애플리케이션에 적합한 기술이므로 SEO 이슈는 심각한 문제로 볼 수 없다. Angular 또는 React 등의 SPA 프레임워크는 서버 랜더링을 지원하는 SEO 대응 기술이 이미 존재하고 있어 SEO 대응이 필요한 페이지에 대해서는 선별적 SEO 대응이 가능하다.

# 2. SPA와 Routing

전통적인 웹 방식에서 SPA까지 발전하게 된 과정과 SPA의 라우팅(Routing)에 대하 살펴보도록 하자.

예제를 실행하기 위해 과정은 아래와 같다.

가상 서버를 실행하기 위해 [serve](https://www.npmjs.com/package/serve)를 설치한다.

```bash
$ npm install -g serve
```

소스코드를 clone한다.

```bash
$ git clone https://github.com/ungmo2/spa-example.git
$ cd spa-example
```

예제의 실행 방법은 아래와 같다.

```bash
# 전통적 링크 방식
$ npm run link
# AJAX 방식
$ npm run ajax
# Hash 방식
$ npm run hash
# PJAX 방식
$ npm run pjax
```

## 2.1 전통적 링크 방식

전통적 링크 방식은 link tag로 동작하는 기본적인 웹페이지의 동작 방식이다. 아래 코드를 살펴보자.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Link</title>
  <link rel="stylesheet" href="css/style.css">
</head>
<body>
  <nav>
    <ul>
      <li><a href="/">Home</a></li>
      <li><a href="service.html">Service</a></li>
      <li><a href="about.html">About</a></li>
    </ul>
  </nav>
  <section>
    <h1>Home</h1>
    <p>This is main page</p>
  </section>
</body>
</html>
```

link tag(`<a href="service.html">Service</a>` 등)을 클릭하면 href 어트리뷰트의 값이 URL의 path에 추가되어 주소창에 나타나고 해당 리소스를 서버에 요청된다.

이때 서버는 html로 화면을 표시하는데 부족함이 없는 완전한 리소스를 클라이언트에 응답한다. 이를 **서버 렌더링**이라 한다. 브라우저는 서버가 응답한 html을 수신하고 렌더링한다. 이때 이전 페이지에서 수신된 html로 전환하는 과정에서 전체 페이지를 새로 로딩하게 되므로 새로고침이 발생한다.

이 방식은 응답된 html로 JavaScript가 필요없이 빠르게 렌더링이 가능하며 페이지마다 고유의 URL이 존재하므로 SEO 대응에 아무런 문제가 없다. 하지만 중복된 리소스를 요청마다 수신해야 하며, 새로고침이 발생하여 사용성이 좋지 않은 단점이 있다.

위 예제를 실행하려면 아래의 명령어를 실행한다.

```bash
$ npm run link
```

## 2.2 AJAX 방식

전통적 링크 방식은 이전 페이지에서 수신된 html로 전환하는 과정에서 전체 페이지를 새로 로딩하게 되므로 새로고침이 발생한다. 간단한 웹페이지라면 문제될 것이 없겠지만 복잡한 웹페이지의 경우, 요청마다 중복된 HTML과 CSS, JavaScript를 매번 다운로드해야하므로 속도 저하의 요인이 된다.

전통적 링크 방식의 단점을 보완하기 위해 등장한 것이 Ajax(Asynchronous JavaScript and XML)이다. Ajax(Asynchronous JavaScript and XML)는 자바스크립트를 이용해서 비동기적(Asynchronous)으로 서버와 브라우저가 데이터를 교환할 수 있는 통신 방식을 의미한다.

서버로부터 웹페이지가 반환되면 화면 전체를 갱신해야 하는데 페이지 일부만을 갱신하고도 동일한 효과를 볼 수 있도록 하는 것이 Ajax이다. 페이지 전체를 로드하여 렌더링할 필요가 없고 갱신이 필요한 일부만 로드하여 갱신하면 되므로 빠른 퍼포먼스와 부드러운 화면 표시 효과를 기대할 수 있다

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Ajax</title>
  <link rel="stylesheet" href="css/style.css">
  <script src="js/index.js" defer></script>
</head>
<body>
  <nav>
    <ul id="navigation">
      <li><a id="home">Home</a></li>
      <li><a id="service">Service</a></li>
      <li><a id="about">About</a></li>
    </ul>
  </nav>
  <div class="app-root"></div>
</body>
</html>
```

위 예제를 살펴보면 link tag(`<a id="home">Home</a>` 등)에 href 어트리뷰트가 없으며 웹페이지의 내용이 일부 비어있는 것을 알 수 있다. 네비게이션이 클릭되면 link tag의 기본 동작을 prevent하고 Ajax을 사용하여 서버에 필요한 리소스를 요청한다. 요청된 리소스가 응답되면 클라이언트에서 웹페이지에 그 내용을 갈아끼워 html을 완성한다. 이는 불필요한 리소스 중복 요청을 방지할 수 있고, 새로고침이 없는 사용자 경험을 구현할 수 있다는 장점이 있다.

JavaScript의 구현은 아래와 같다.

```javascript
(function () {
  const root = document.querySelector('.app-root');
  const navigation = document.getElementById('navigation');

  function render(data) {
    const json = JSON.parse(data);
    root.innerHTML = `<h1>${json.title}</h1><p>${json.content}</p>`;
  }

  function renderHtml(html) {
    root.innerHTML = html;
  }

  function get(url) {
    return new Promise((resolve, reject) => {
      const req = new XMLHttpRequest();
      req.open('GET', url);
      req.send();

      req.onreadystatechange = function () {
        if (req.readyState === XMLHttpRequest.DONE) {
          if (req.status === 200) resolve(req.response);
          else reject(req.statusText);
        }
      };
    });
  }

  const route = {
    'home': function () {
      get('/data/home.json')
        .then(res => render(res));
    },
    'service': function () {
      get('/data/service.json')
        .then(res => render(res));
    },
    'about': function () {
      get('/data/about.html')
        .then(res => renderHtml(res));
    },
    otherwise(page) {
      root.innerHTML = `${page} Not Found`;
    }
  };

  function router(page) {
    (route[page] || route.otherwise)(page);
  }

  // AJAX 요청은 주소창의 url을 변경시키지 않으므로 history 관리가 되지 않는다.
  navigation.addEventListener('click', e => {
    if (!e.target || e.target.nodeName !== 'A') return;
    e.preventDefault();
    router(e.target.id);
  });

  // DOMContentLoaded은 HTML과 script가 로드된 시점에 발생하는 이벤트로 load 이벤트보다 먼저 발생한다. (IE 9 이상 지원)
  // 새로고침이 클릭되었을 때, 웹페이지가 처음 로딩되었을 때, 현 페이지(예를들어 loclahost:5002)를 서버에 요청한다. 이때 Home에 필요한 리소스를 Ajax 요청한다.
  window.addEventListener('DOMContentLoaded', () => router('home'));
}());
```

Ajax는 URL을 변경시키지 않으므로 주소창의 주소가 변경되지 않는다. 이는 브라우저의 뒤로가기, 앞으로가기, 새로고침 등의 **history 관리가 동작하지 않음을 의미한다.** 물론 코드 상의 history.back(), history.go(n) 등도 동작하지 않는다. 새로고침을 클릭하면 주소창의 주소가 변경되지 않기 때문에 언제나 첫페이지가 다시 로딩된다. 하나의 주소로 동작하는 Ajax 방식은 **SEO 이슈**에서도 자유로울 수 없다.

위 예제를 실행하려면 아래의 명령어를 실행한다.

```bash
$ npm run ajax
```

## 2.3 Hash 방식

AJAX방식은 불필요한 리소스 중복 요청을 방지할 수 있고, 새로고침이 없는 사용자 경험을 구현할 수 있다는 장점이 있지만 history 관리가 되지 않는 단점이 있다. 이를 보완한 방법이 Hash 방식이다.

Hash 방식은 URI의 fragment identifier(#service)의 고유 기능인 앵커(anchor)를 사용한다. fragment identifier는 hash mark 또는 hash라고 부르기도 한다.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>SPA</title>
  <link rel="stylesheet" href="css/style.css">
  <script src="js/index.js" defer></script>
</head>
<body>
  <nav>
    <ul>
      <li><a href="/">Home</a></li>
      <li><a href="#service">Service</a></li>
      <li><a href="#about">About</a></li>
    </ul>
  </nav>
  <div class="app-root">Loading...</div>
</body>
</html>
```

위 예제를 살펴보면 link tag(`<a href="#service">Service</a>` 등)의 href 어트리뷰트에 hash를 사용하고 있다. 즉 네비게이션이 클릭되면 hash가 추가된 URI가 주소창에 표시된다. 단, **URL이 동일한 상태에서 hash가 변경되면 브라우저는 서버에 어떠한 요청도 하지 않는다. 즉, hash는 변경되어도 서버에 새로운 요청을 보내지 않으며 따라서 페이지가 갱신되지 않는다.** hash는 요청을 위한 것이 아니라 fragment identifier(#service)의 고유 기능인 앵커(anchor)로 웹페이지 내부에서 이동을 위한 것이기 때문이다.

또한 hash 방식은 서버에 새로운 요청을 보내지 않으며 따라서 페이지가 갱신되지 않지만 페이지마다 고유의 **논리적 URL이 존재하므로 history 관리에 아무런 문제가 없다.**

JavaScript의 구현은 아래와 같다.

```javascript
(function () {
  const root = document.querySelector('.app-root');

  function render(data) {
    const json = JSON.parse(data);
    root.innerHTML = `<h1>${json.title}</h1><p>${json.content}</p>`;
  }

  function renderHtml(html) {
    root.innerHTML = html;
  }

  function get(url) {
    return new Promise((resolve, reject) => {
      const req = new XMLHttpRequest();
      req.open('GET', url);
      req.send();

      req.onreadystatechange = function () {
        if (req.readyState === XMLHttpRequest.DONE) {
          if (req.status === 200) resolve(req.response);
          else reject(req.statusText);
        }
      };
    });
  }

  const route = {
    '': function () {
      get('/data/home.json')
        .then(res => render(res));
    },
    'service': function () {
      get('/data/service.json')
        .then(res => render(res));
    },
    'about': function () {
      get('/data/about.html')
        .then(res => renderHtml(res));
    },
    otherwise() {
      root.innerHTML = `${location.hash} Not Found`;
    }
  };

  function router() {
    // url의 hash를 취득
    const hash = location.hash.replace('#', '');
    (route[hash] || route.otherwise)();
  }

  // 네비게이션을 클릭하면 uri의 hash가 변경된다. 주소창의 uri가 변경되므로 history 관리가 가능하다.
  // 이때 uri의 hash만 변경되면 서버로 요청을 수행하지 않는다.
  // 따라서 uri의 hash가 변경하면 발생하는 이벤트인 hashchange 이벤트를 사용하여 hash의 변경을 감지하여 필요한 AJAX 요청을 수행한다.
  // hash 방식의 단점은 uri에 불필요한 #이 들어간다는 것이다.
  window.addEventListener('hashchange', router);

  // DOMContentLoaded은 HTML과 script가 로드된 시점에 발생하는 이벤트로 load 이벤트보다 먼저 발생한다. (IE 9 이상 지원)
  // 새로고침이 클릭되었을 때, 웹페이지가 처음 로딩되었을 때, 현 페이지(예를들어 loclahost:5003/#service)를 요청하므로 index.html이 재로드되고 DOMContentLoaded 이벤트가 발생하여 router가 호출된다.
  window.addEventListener('DOMContentLoaded', router);
}());
```

hash 방식의 단점은 uri에 불필요한 #이 들어간다는 것이다. 일반적으로 hash 방식을 사용할 때 #!을 사용하기도 하는데 이를 [해시뱅(Hash-bang)](https://blog.outsider.ne.kr/698)이라고 부른다.

hash 방식은 과도기적 기술이다. HTML5의 Histroy API인 [pushState](https://developer.mozilla.org/ko/docs/Web/API/History_API)가 모든 브라우저에서 지원이 된다면 해시뱅은 사용하지 않아도 되지만 현재 pushState는 일부의 브라우저(IE 10 이상)에서만 지원이 가능하다.

또 다른 문제는 **SEO 이슈**이다. 크롤러는 검색엔진이 웹사이트의 콘텐츠를 수집하기 위해 HTTP 1.1과 URL 스펙(RFC-2396같은)을 따른다. 이러한 크롤러는 JavaScript를 실행시키지 않기 때문에 hash 방식으로 만들어진 사이트의 콘텐츠를 수집할 수 없다. 구글은 해시뱅을 일반 URL을 변경시켜 이 문제를 해결한 것으로 알려지지만 다른 검색 엔진은 hash 방식으로 만들어진 사이트의 콘텐츠를 수집할 수 없다.

위 예제를 실행하려면 아래의 명령어를 실행한다.

```bash
$ npm run hash
```

## 2.4 PJAX 방식

hash 방식의 가장 큰 단점은 SEO 이슈이다. 이를 보완한 방법이 HTML5의 Histroy API인 [pushState](https://developer.mozilla.org/ko/docs/Web/API/History_API)와 [popstate 이벤트](https://developer.mozilla.org/ko/docs/Web/Reference/Events/popstate)를 사용한 PJAX 방식이다. pushState와 popstate은 IE 10 이상에서 동작한다.

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>PJAX</title>
  <link rel="stylesheet" href="css/style.css">
  <script src="js/index.js" defer></script>
</head>
<body>
  <nav>
    <ul id="navigation">
      <li><a href="/">Home</a></li>
      <li><a href="/service">Service</a></li>
      <li><a href="/about">About</a></li>
    </ul>
  </nav>
  <div class="app-root">Loading...</div>
</body>
</html>
```

위 예제를 살펴보면 link tag(`<a href="/service">Service</a>` 등)의 href 어트리뷰트에 path를 사용하고 있다. 즉 네비게이션이 클릭되면 path가 추가된 URI가 서버로 요청된다. PJAX 방식은 네베게이션 클릭 이벤트를 캐치하고 preventDefault를 사용하여 서버로의 요청을 방지한다. 이후, href 어트리뷰트에 path을 사용하여 AJAX 요청을 하는 방식이다.

AJAX 요청은 주소창의 주소를 변경시키지 않아 history 관리가 불가능하다. 이때 사용하는 것이 pushState 메소드이다. pushState 메소드는 주소창의 url을 변경하고 url을 history entry로 추가하지만 요청하지는 않는다.

JavaScript의 구현은 아래와 같다.

```javascript
(function () {
  const root = document.querySelector('.app-root');
  const navigation = document.getElementById('navigation');
  const URL = 'http://localhost:5004';

  function render(data) {
    const json = JSON.parse(data);
    root.innerHTML = `<h1>${json.title}</h1><p>${json.content}</p>`;
  }

  function renderHtml(html) {
    root.innerHTML = html;
  }

  function get(url) {
    return new Promise((resolve, reject) => {
      const req = new XMLHttpRequest();
      req.open('GET', url);
      req.send();

      req.onreadystatechange = function () {
        if (req.readyState === XMLHttpRequest.DONE) {
          if (req.status === 200) resolve(req.response);
          else reject(req.statusText);
        }
      };
    });
  }

  const route = {
    '/': function () {
      get('/data/home.json')
        .then(res => render(res));
    },
    '/service': function () {
      get('/data/service.json')
        .then(res => render(res));
    },
    '/about': function () {
      get('/data/about.html')
        .then(res => renderHtml(res));
    },
    otherwise(path) {
      root.innerHTML = `${path} Not Found`;
    }
  };

  function router(path) {
    (route[path] || route.otherwise)(path);
  }

  // history entry가 변경되면 발생하는 이벤트
  // PJAX 방식은 hash를 사용하지 않으므로 hashchange 이벤트를 사용할 수 없다.
  // popstate event는 pushState에 의해 발생시키지 않는다.
  // 이전페이지 / 다음페이지 button 또는 history.back() / history.go(n)에 의해 발생한다.
  window.addEventListener('popstate', e => {
    // 이전페이지 / 다음페이지 button이 클릭되면 router를 호출
    // e.state는 pushState 메소드의 첫번째 인수
    router(e.state.path);
  });

  // 네비게이션을 클릭하면 주소창의 url이 변경되므로 서버로 요청이 전송된다.
  // preventDefault를 사용하여 이를 방지하고 history 관리를 위한 처리를 실시
  navigation.addEventListener('click', e => {
    if (!e.target || e.target.nodeName !== 'A') return;
    e.preventDefault();
    const path = e.target.href.replace(URL, '');

    // 주소창의 url은 변경되지만 요청하지는 않는다.
    history.pushState({ path }, null, path);
    // path에 의한 AJAX 요청
    router(path);
  });

  // 웹페이지가 처음 로딩되었을 때
  router('/');

  // DOMContentLoaded은 HTML과 script가 로드된 시점에 발생하는 이벤트로 load 이벤트보다 먼저 발생한다. (IE 9 이상 지원)
  // window.addEventListener('DOMContentLoaded', router);

  // 새로고침이 클릭되었을 때, 현 페이지(예를들어 loclahost:5004/service)가 서버에 요청된다.
  // 서버측에는 이에 응답하는 기능이 추가되어야 한다.
  // ex) app.get('/service', (req, res) => res.sendFile(path.join(__dirname + '/public/data/service.html')));
}());
```

PJAX 방식은 서버에 새로운 요청을 보내지 않으며 따라서 페이지가 갱신되지 않지만 페이지마다 고유의 URL이 존재하므로 history 관리에 아무런 문제가 없다. 또한 hash를 사용하지 않으므로 SEO에도 문제가 없다.

다만, 브라우저의 새로고침 버튼을 클릭하면 예를들어 loclahost:5004/service와 같은 요청이 서버로 전달된다. 이때 서버는 URL에 따라 해당 리소스를 HTML으로 클라이언트에 응답하여야 한다.

이는 **서버 렌더링 방식과 Ajax 방식이 혼재**되어 있는 것이다. 서버는 클라이언트의 request hader의 Accept가 'text/html'이면 HTML을 응답하고, request hader의 Accept가 'application/json'이면 필요 리소스만 JSON으로 응답하도록 구현하여야 한다. 예를 들어, 새로고침으로 브라우저에서 요청이 수행되면 request hader의 Accept는 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8'이고 서버는 HTML을 응답한다. AJAX 요청의 경우, [setRequestHeader 메소드](https://developer.mozilla.org/ko/docs/XMLHttpRequest/setRequestHeader)를 사용하여 응답될 데이터의 mine type을 json으로 지정한다.

이에 대한 구현 예제는 아래와 같다.

```javascript
// Client
function get(url) {
  return new Promise((resolve, reject) => {
    const req = new XMLHttpRequest();
    req.open('GET', url);
    // 서버에 JSON을 요청한다.
    req.setRequestHeader('Accept', 'application/json');
    req.send();

    req.onreadystatechange = function () {
      if (req.readyState === XMLHttpRequest.DONE) {
        if (req.status === 200) resolve(req.response);
        else reject(req.statusText);
      }
    };
  });
}

get('/service').then(res => render(res));
```

```javascript
// Server
const express = require('express');
const app = express();
const fs = require('fs');

app.get('/service', (req, res) => {
  res.format({
    // 새로고침에 의한 브라우저 요청
    'text/html': function(){
      res.sendFile(path.join(__dirname + '/public/data/service.html'));
    },
    // Ajax 요청
    'application/json': function(){
      res.send(JSON.parse(fs.readFileSync('./public/data/service.json', 'utf8')));
    },
    'default': function() {
      // log the request and respond with 406
      res.status(406).send('Not Acceptable');
    }
  });
});

app.listen(3000, function () {
  console.log('listening on http//localhost:3000');
});
```

PJAX 방식의 예제를 실행하려면 아래의 명령어를 실행한다.

```bash
$ npm run pjax
```

## 2.5 Conclusion

전통적 링크 방식에서 PJAX 방식까지 SPA의 발전 과정을 살펴보았다. 지금까지 살펴본 4가지 방식을 History 관리, SEO 대응, 사용자 경험, 서버 랜더링 관점애서 정리하면 아래와 같다.

| 구분          | History 관리 | SEO 대응    | 사용자 경험  | 서버 랜더링  |
|:-------------|:-----------:|:----------:|:---------:|:---------:|
| 전통적 링크 방식 | ◯           | ◯          | ✗         | ◯
| AJAX 방식     | ✗           | ✗          | ◯         | ✗
| Hash 방식     | ◯           | ✗          | ◯         | ✗
| PJAX 방식     | ◯           | ◯          | ◯         | △

모든 소프트웨어 아키텍처에는 trade-off가 존재한다. SPA 또한 모든 애플리케이션에 적합한 은탄환(Silver bullet)은 아니다. 애플리케이션의 상황을 고려하여 적절한 방법을 선택할 필요가 있다.

# Reference

* [브라우저 히스토리 조작하기](https://developer.mozilla.org/ko/docs/Web/API/History_API)

* [Using the HTML5 History API](https://css-tricks.com/using-the-html5-history-api/)

* [Making a Single Page App Without a Framework](https://tutorialzine.com/2015/02/single-page-app-without-a-framework)

* [Get URL and URL Parts in JavaScript](https://css-tricks.com/snippets/javascript/get-url-and-url-parts-in-javascript/)

* [해시뱅(#!)에 대해서](https://blog.outsider.ne.kr/698)

* [XMLHttpRequest.setRequestHeader()](https://developer.mozilla.org/ko/docs/XMLHttpRequest/setRequestHeader)
