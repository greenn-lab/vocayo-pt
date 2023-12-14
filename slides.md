---
title: Vocayo (Browser extension)
theme: eloc
transition: fade-out
presenter: build
mdc: true
page: 1
---

# Vocayo

Browser Extension
<div class="text-xl">for Chrome</div>


<div class="text-sm mt-20"><code>vocabulary</code> 에서 유래한 말로, "어휘사전" 확장 프로그램</div>

<!--
안녕하세요. 지난 4주, 정확하게는 5주에 걸쳐서 이번 스터디를 진행 했습니다.
각자의 맡은 바 역할들이 있음에도 불구하고, 참여와 관심으로 함께한 분들께 감사를 드립니다.

진행한 것은 브라우저 익스텐션, 확장프로그램 입니다.
웹사이트에서 모르는 영어 단어를 만났을 때, 바로 뜻을 찾아 볼 수 있도록 하는 기능을 가진 건데요.
보케블러리를 "a.k.a. 보카" 라고 발음하는 것과 신나는 추임세 "요우~" 에서 착안해서 "보카요" 라고 이름 붙여 봤습니다.

시작해 보겠습니다.

-->


---
transition: slide-left
---

# goal

- **공식 문서 읽기** - 새로운 개발 플랫폼에 접근법
- **타 서비스 API** - HTTP 를 활용하는 다양한 방법
- **Serverless** - Cloud Platform 활용
- **One thing** - 단 하나의 기능을 단 한 달에!

<!--
우리가 목표로 한 것은 이 3가지 였습니다.

첫번째로, 브라우저 확장프로그램이라는 새로운 플랫폼을 다루는 것이었고.
둘째로, 흔히 쓰는 HTTP 프로토콜을 웹이 아닌 다른 클라이언트에서 진행하는 것과
셋째, 요즘 핫 하다는 클라우드 플랫폼을 활용해서 단어들을 누적, 정리 해보자는 것 이었습니다.

진행 결과 아쉬운 점도 있고, 과녁 설정 안했던 의외의 것을 얻기도 했던 과정 이었는데요.
세세한 과정은 계속 이어가면서 말씀 드릴게요. 
-->


---
transition: slide-left
---
# production

https://github.com/timbel-net/vocayo

<img v-click src="https://raw.githubusercontent.com/timbel-net/vocayo/feat-gwang-yang/docs/vocayo-chrome-extension/sample.gif" />

<!--
실제 완성한 프로덕션 입니다.

소스는 깃헙에 적재했어요. 사내에 깃랩이 있으나 현재 이 PT와 함께 관리하기 위해
클라우드의 서버리스를 고려해서 퍼블릭한 저장소가 필요 했습니다.

캡쳐는 있어보이게 타임지에서 했네요. 중요한 건 그게 아니고.
특정 웹사이트의 컨텐츠에서 모르는 영어 단어가 있으면 마우스로 드래그 또는 더블클릭해서 단어 단위 선택을 하는데,
그렇게 선택이 된 키워드를 추출해서 네이버 사전에 비정상적인 API 요청으로 응답을 받아 오고요.
그 결과는 해당 위치에 레이어로 띄워서 표출해 주는 내용입니다.

참고로, 사전 서비스는 API 가 없습니다. 사전의 뜻풀이가 저작권이 있어서란 인터넷 풍문과 사전 서비스에 
대형 사전 출판사의 로고가 떡하니 박혀있는 걸 봐선 제공할 수 있는 API 가 없을 법도 해 보입니다.

아무튼, 그런 이유로 HTTP API 를 사용하는 방식에서 좀 다른 노선을 취하게 된 부분이 있었습니다.
-->


---
transition: slide-up
---
# [manifest.json](https://developer.chrome.com/docs/extensions/reference/manifest?hl=ko)


<!--
브라우저 확장프로그램의 개발을 진행하는 건, 모르는 새로운 개발 플랫폼의 접근이었고,
그 시작이 이 매니패스트.json 파일 이었습니다.

이 파일은 확장프로그램에서 필요한 리소스, 사용범위, 클라이언트 PC의 자원에 접근할 수 있는 권한 등등이
정의된 내용이고, 자세한 명세는 링크를 걸어두었습니다.

이어서 관련된 설명이 이어지는데 거의 모두가 개발 공식문서의 링크를 가지고 있어요.
현대의 개발에서 새로운 플랫폼에 접근한다는 게, 공식문서를 잘 살펴보는 것으로 A to Z 가 된다는 걸 말씀 드리고 싶습니다.  
-->

---
transition: slide-up
---
# service_worker
```json {5}
{
  ...,

  "background": {
    "service_worker": "vocayo-background.js"
  }
}
```
<a class="text-sm text-right w-[640px]" href="https://developer.chrome.com/docs/extensions/develop/concepts/service-workers/basics?hl=ko" target="_blank">공식문서</a>

- `service_worker` 웹어플리케이션과 별개로 독립적으로 실행되는 서비스<br>(네이버 사전으로 API 요청을 보내게 될 거에요.)


<!--
이전의 매니패스트 내부에 서비스 워커 항목인데요.
브라우저 확장프로그램은 브라우저와 별개로 실행될 수도 있어요. 예를들면 프로그래시브 웹 앱. 일컫어 PWA 같이 오프라인에서 동작하는 애플리케이션이나
브라우저가 꺼져있어서 OS 차원에서 제공하는 알림을 받는 다거나. 왜, 메신저 닫아둬도 메세지 알림 받는 거 같은 거요.
그런식으로 독립적으로 실행되는 애플리케이션이 있을 수 있거든요.

그래서 이 서비스 워커라는 게, 그런 방식에 대응되는 거에요.
아까 네이버 사전 서비스에 비공식적인 방식으로 HTTP 접근을 하는데, 그 이유가 브라우저 API 를 통하지 않아야만
CORS. 즉 크로스 오리진 리소스 쉐어링 이라는 것에 발목을 잡히지 않기 때문이에요.
브라우저 API로 http 접근을 하면, 도메인이 같은 곳에서만 접근이 되는 CORS 정책에서 벗어나는 방법이 
이 서비스 워커에서 가능하기 때문입니다.

물론 확장프로그램 API의 내부 어떤 방식에 의해서, 접근 퍼미션 항목에 통신 허용 도메인을 정의해야하는 또다른 권한 정의 부분이 있지만
원천적으로 CORS 로 차단되는 브라우저 API 에서는 불가능한 얘기 입니다.
-->


---
transition: slide-up
---
# content_scripts
```json {5-7}
{
  ...,

  "content_scripts": [{
    "matches": [ "<all_urls>" ],
    "js": [ "vocayo-content.js" ],
    "css": [ "vocayo-content.css" ]
  }]
}
```
<a class="text-sm text-right w-[640px]" href="https://developer.chrome.com/docs/extensions/reference/manifest/content-scripts?hl=ko" target="_blank">공식문서</a>

- `matches` 에 부합되는 URL의 사이트 매칭
- `js` 매칭된 사이트에서 실행될 javascript
- `css` 매칭된 사이트에서 실행될 css files


<!--
컨텐츠 스크립트 항목은요.
특정 웹사이트에 접근했을 때, 동작이 필요한 부분을 정의하는 거에요.

어떤 사이트에서든, 모르는 영어 단어가 보이면 우리 보카요가 실행해 줘야 하니까,
그 동작을 구현하는 내용이 필요 하잖아요. 그걸 정의한 거에요.

역시, 공식문서 링크가 있고요. 살펴 봐야만 더 나아갈 수 있습니다.
-->

---
transition: slide-left
---
# host_permissions
```json{5-6}
{
  ...,

  "host_permissions": [
    "https://dict.naver.com/",
    "https://en.dict.naver.com/"
  ]
}
```
<a class="text-sm text-right w-[640px]" href="https://developer.chrome.com/docs/extensions/develop/concepts/declare-permissions?hl=ko#host-permissions" target="_blank">공식문서</a>

네이버 사전에 API 요청을 보내게 되는 부분에서 [CORS](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS) 등의 문제를 벗어나는데 필요

<!--
아까 장황하게 설명했던 부분과 겹치는 부분이 있어서 크게 설명은 드리지 않겠습니다.
CORS 정책이 없이 Http API를 호출하는 대신, 최소한의 권한 설정 같은 거라고 생각하시면 될 것 같아요. 
-->


---
transition: slide-left
---
# implementation
- Typescript
  - 손쉬운 API 명세 확인
  - Intellisense 기능으로 힌트 확인
  - 미지의 API에 접근을 용이하게 하는 방법
- [Extensions Reloader](https://chromewebstore.google.com/detail/extensions-reloader/fimgfedafeadlieiabdeeaodndnlbhid)
  - 브라우저 확장프로그램 개발 지원


<!--
실제 구현 코드를 설명할 차례 네요.

타입스크립트를 사용했어요. 워낙 유명하고, 크게 장벽이 없어서 다들 알고 계시고 하시겠지만 잠깐 설명을 하자면. 
타입스크립트. 스택오버플로우 최근 몇년간 가장 선호하는 개발 언어에 자바스크립트, 그리고 그 여파로 타입스크립트가
최상위권에 있잖아요. 자바스크립트는 동적인 UI를 다루기 위해 태동한 것이다 보니, 유연성에 크게 치우쳐 있고.
그런 자유로움이 리팩토링이나 API 명세를 정의하기에 모호한 부분들이 많다는 단점이 되기도 한거죠. 그 지점을 보완해 주는게 타입스크립트죠.
타입을 세세히 정의하고 신텍스(syntax)는 자바스크립트를 지향하는 슈퍼셋 이니까요.

그래서 새로운 개발 플랫폼, 생소한 API에 접근해야 하니까 정확히 정해진 타입으로 개발IDE가 지원해주는 인텔리센스 기능이나,
명세를 바로 찾아주는 기능을 사용하기에 타입스크립트가 적합하다고 판단 했어요.

그리고, 확장프로그램 개발할 때, 코드가 변경되면 브라우저 설정에서 플러그인을 리로드 해줘야하는 번거로움이 생기는 데,
그때 도움되는 익스텐션 리로더라는 또다른 앱이 있으면 생산성이 확~ 올라가거든요.

이렇게 구현에 사용한 것들을 말씀 드렸고. 
이제, 실제 코드를 확인해 보실까요? 
-->


---
transition: slide-up
---
# vocayo-background.ts
```ts{0|3-5|7}
// vocayo-background.ts
chrome.runtime.onMessage.addListener((keyword: string, _, response: (json?: any) => void) => {
    fetch(`https://en.dict.naver.com/api3/enko/search?m=mobile&lang=ko&query=${keyword}`)
        .then(resp => resp.json())
        .then(json => response(json))

    return true
})
```
<a class="text-sm text-right w-[1100px]" href="https://developer.chrome.com/docs/extensions/reference/api/runtime?hl=ko#event-onMessage" target="_blank">공식문서</a>

- <div v-click="1">네이버 사전 API에 요청을 보내고 응답을 받아요.</div>
- <div v-click="2">⚠️ 비동기 응답을 위해선 이벤트 함수가 <code>return true</code> 를 반환 <a href="https://developer.chrome.com/docs/extensions/develop/concepts/messaging?hl=ko" target="_blank">[참조]</a></div>


<!--
앞서 설명했었던, 서비스 워커에서 실행되는 코드에요.

네이버 사전 서비스를 네트워크 캡쳐링해서 URL을 뽑아냈고, 거기에 쿼리라는 파라미터에 원하는 키워드를 던지면 JSON 으로 뜻에 관련된 데이터를 받을 수 있는 내용 이에요.

이게 좀 헤맸던 부분 인데요. 결과적으로 공식문서가 답을 가지고 있었고, 대충 읽어서 놓친 부분이었어요.
비동기 요청의 특성상 요청을 보내고 이어지는 프로세스가 주르륵 흘러서, 결과없이 함수를 빠져나오는 상황에 망연자실 했었는데요.
결과적으로 리턴 트루가 확장프로그램 API에서 내부처리가 되면서 비동기 요청이 끝나는 지점을 기다려주는 신호가 된 내용입니다.


"답은 모두 공식문서에 있다"는 계시를 다시 새겼습니다.
물론 공식문서가 엉망인것들도 많지만, 그건 비주류의 문제이고.
현대의 개발 생태계에서 어플리케이션 인터페이스. API 서비스가 확장되고 성장하려면 잘 갖춰진 공식문서는,
필수라는 생각이 들었습니다.
-->

---
transition: slide-up
---
# vocayo-content.ts
```ts{0|2|8|9-10}
// vocayo-content.ts
const {anchorNode, anchorOffset: start, focusNode, focusOffset: close} = document.getSelection()!

if (anchorNode === focusNode) {
    const keyword = anchorNode?.textContent?.substring(start, close)?.trim()

    if (isWord(keyword)) {
        const response: Response = await chrome.runtime.sendMessage(keyword)
        const contents = response?.searchResultMap?.searchResultListMap.WORD.items
            .map(item => item.meansCollector[0].means[0].value).join('<hr/>')

        if (contents) {
            openPopup(contents, e.clientX, e.clientY)
        }
    }
}
```
<a class="text-sm text-right w-[1100px]" href="https://developer.chrome.com/docs/extensions/reference/api/runtime?hl=ko#method-sendMessage" target="_blank">공식문서</a>


- <div v-click="1">사이트에서 마우스로 선택된 텍스트를 읽어 들임<a><sup href="https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment" target="_blank">구조분해할당</sup></a></div>
- <div v-click="2">vocayo-background.js에 <code>onMessage</code>로 키워드 전달</div>
- <div v-click="3">응답 받은 내용 구성 (타입정의 NaverDictionary.d.ts)</div> 


<!--
웹서핑하면서 특정 사이트에서 실행된다는 컨텐츠 스크립트의 구현 코드입니다.

텍스트를 추출하는데요. 이에스2015플러스의 신택스(syntax) 중에 구조분해할당이 있는데, 가독성에 좋은 내용이라서 적극 활용해 봤습니다.

암튼, 사이트의 컨텐츠에서 선택된 텍스트를 추출해내고요.

그렇게 추출된 텍스트를 센드메세지 API를 통해서 서비스 워커로 전달합니다. 이미 지나온 내용이니까 서비스 워커에서 네이버 사전으로
요청을 날리는 건 다시 살피지 않을게요.
응답받은 JSON 이 좀 복잡해요. 네이버 사전에서 발음기호, 유의어 등등 엄청 많은 정보를 담아 보내더라고요. 그걸 잘 편집해서
표출하는 함수까지 호출 합니다.
-->


---
transition: slide-left
---
# vocayo-content.ts
```ts{0|9-17}
// vocayo-content.ts
const openPopup = (() => {
    const popup = document.createElement('div')
    popup.id = 'vocayo-popup'

    document.body.append(popup)

    return (content: string, x: number, y: number) => {
        popup.innerHTML = content
        popup.style.left = `${x}px`
        popup.style.top = `${y}px`
        popup.classList.add('open')

        document.body.addEventListener('mousedown', function closePopup() {
            document.body.removeEventListener('mousedown', closePopup)
            popup.classList.remove('open')
        })
    }
})()
```
<a class="text-sm text-right w-[900px]" href="https://developer.chrome.com/docs/extensions/reference/api/runtime?hl=ko#method-sendMessage" target="_blank">공식문서</a>

- <div>사이트에서 결과 보여주기</div>
- <div v-click="1"><a href="https://developer.mozilla.org/ko/docs/Web/JavaScript/Closures" target="_blank">클로저</a> 패턴으로 구현</div>


<!--
따라오기 힘드셨을지 모르겠네요. 다 왔습니다.
사이트의 컨텐츠에서 마우스 커서 포지션에 맞춰서 뜻을 표출해주는 코드에요. 익숙한 웹 API가 이렇게 반가울 수가 없네요.

아무튼, 이부분에서는 자바스크립트의 특성중에 클로저라는 게 있는데. 그걸 활용 했거든요.
가비지 컬렉터가 메모리를 쓸고 갈 때, 변수 참조는 남기고 변수 스콥. 그러니까 변수 활성화 영역은 닫힌 상태로 만들어서,
가비지 컬렉터의 빗자루를 피해가는 거에요. 

팝업(popup) 이라는 변수를 함수 안에서 선언하고,
함수의 리턴 부분에 다시 함수를 선언함으로써 클로저 상태를 만들어 냈습니다.
모르는 단어가 나올 때마다 HTML 요소가 여러번 생성되는 걸 방지할 수 있도록 이요. 
-->


---
transition: fade
---
# conclusion
- earning
  - 새로운 개발 플랫폼에 대한 접근법
- be desired
  - 목표했던 Serverless 를 활용하지 못함
  - 어쩌면 필요없는 플랫폼 지식 축적으로... 🤯 


<!--
이렇게 해서 구현까지 모두 마쳤습니다. 긴장감 넘치는 상황은 여기까지에요. 

얻은 부분은 새로운 개발 플랫폼에 적응하는 방법에 대해 이해를 해봤다는 거에요.
"편집이 곧 창조다" 라는 에디톨로지 학설을 주창하는 어떤 분이 생각나네요. 
새로운 언어, 새로운 라이브러리, 다양한 API 를 활용해서 생산성 높은 개발 방향을 설정하는 게,
현대 개발의 핵심요소 중 하나가 아닌가 생각해 볼 때, 이번에 배운 이런 내용은 탄탄한 기반을 잘 다질 수 있는 좋은 배경이 되는 게 아닐까 싶어요.

아쉬운 점은 목표했던 서버리스 환경을 활용하지 못했다는 점이에요.
한 달이라는 목표를 설정했기 때문에, 아쉬운대로 정리를 하는 게 맞다는 판단 입니다.
다음 주제에서 또 집중력있게 새로운 마인드셋으로 임하려면 아쉬운대로 마무리도 나쁘지 않다는 생각이고요.
다음 주제는 클라우드를 중심으로 한 내용이면 좋겠다는 바람 입니다. 
-->


---
---
# End.


<!--
이상. 발표를 마칩니다.
-->