---
layout: single
title: "[Javascript] Promise와 Promise 메서드"
categories: Javascript
tag: [Javascript, Promise, 비동기]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Promise와 Promise 메서드

## Promise란?

무근본의 promise 정의

    `Promise`는 비동기 처리 시 사용하는 객체로

    XMLHttpRequest를 이용해서 콜백을 넘겨서 사용했을 때보다
    가독성이 좋으며,
    콜백 헬이 일어나지 않고,
    promise를 반환하기 때문에 chaining(then, catch)를 이용해서 얻은 데이터를 손쉽게 사용할 수 있다.

라고 알고만 있었으나..

**MDN의 Promise 정의**

> `Promise`는 프로미스가 생성된 시점에는 알려지지 않았을 수도 있는 값을 위한 대리자로, 비동기 연산이 종료된 이후에 결과 값과 실패 사유를 처리하기 위한 처리기를 연결할 수 있습니다.
> 프로미스를 사용하면 비동기 메서드에서 마치 동기 메서드처럼 값을 반환할 수 있습니다. 다만 최종 결과를 반환하는 것이 아니고, 미래의 어떤 시점에 결과를 제공하겠다는 '약속'(프로미스)을 반환합니다.

정리하자면,

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
👉 Promise는 비동기 처리 시 Promise를 생성했을 때, 그 시점에서 없을 수도 있는 "response를 보장"해주는 객체
</aside>

위의 알아들을 수 없는 말들을 분석해보자.

1. 알려지지 않았을 수도 있는 값을 위한 **대리자**

   비동기 처리하려고 Promise를 생성한 시점에는 response가 없을 수 있다.  
   (당연히, api를 쏜다고 데이터가 막 바로 오는 것은 아니므로)

   따라서 내가 생성한 Promise는 비동기 요청을 하기 때문에 언젠가는 여기에 결과(값 혹은 에러)가 도착할 것이라고 Guarantee/약속 해주므로 이름이 Promise인 것이다.  
   (지금 생각하니 이름 너무 잘 지은 것 🥰)

2. 최종 결과를 반환하는 것이 아니고

   Promise를 사용하면 내가 사용하고 싶은 데이터가 바로 오는 것이 아니라고 나와있다.

   따라서 사용할 수 있는 데이터인지, 아닌지를 나타내주는 status 프로퍼티가 있다!

   - 대기(_pending)_: 이행하지도, 거부하지도 않은 초기 상태.
   - 이행(_fulfilled)_: 연산이 성공적으로 완료됨.
   - 거부(_rejected)_: 연산이 실패함.  
     → 이행과 거부를 묶어 settled라고 하는데, 이는 Promise의 상태가 아닌 그냥 편의성을 위한 키워드

3. 어떤 시점에 결과를 제공

   mdn의 설명

   > 대기 중인 프로미스는 값과 함께 이행할 수도, 어떤 이유(오류)로 인해 거부될 수도 있습니다. 이행이나 거부될 때, 프로미스의 `then` 메서드에 의해 대기열(큐)에 추가된 처리기들이 호출됩니다.
   > 이미 이행했거나 거부된 프로미스에 처리기를 연결해도 호출되므로, 비동기 연산과 처리기 연결 사이에 경합 조건은 없습니다.

   따라서, Promise를 사용해서 비동기 처리가 되어 결과가 오면(성공해서 fulfilled가 되거나 오류나서 rejected되면)
   그 때 Promise 메서드 then에 의해서 큐로 들어갔던 핸들러/콜백들이 처리된다.

   그런데 여기서 추가적으로 이미 fulfilled가 되고, rejected가 된 상태의 Promise라도 그에 상응하는 핸들러/콜백들을 처리할 수 있다.

   이러한 특성 때문에 Promise를 사용한다고 해서 무조건 그 때만 데이터를 사용할 수 있는 게 아닌 것이 된다!

그래서 Promise는 어떤 데이터를 사용할 수 있게 되는 정확한 시간보다는 **결과에 반응**하는 데에 더 관심이 있는 것이다.

또한, 이러한 특성을 이용해서 api로 비동기 병목현상들을 개선할 수 있게 되는 것이다. (아래에서 설명)

## Promise 메서드

메서드에는 all, allSettled, any, race가 있다.

이번에 코드로 구현하게 될 메서드는 allSettled이다.

- Promise.all

  > 주어진 모든 프로미스가 이행하거나, 한 프로미스가 거부될 때까지 대기하는 새로운 프로미스를 반환합니다.
  > 반환하는 프로미스가 이행한다면, 매개변수로 제공한 프로미스 각각의 이행 값을 모두 모아놓은 배열로 이행합니다. 배열 요소의 순서는 매개변수에 지정한 프로미스의 순서를 유지합니다.
  > 반환하는 프로미스가 거부된다면, 매개변수의 프로미스 중 거부된 첫 프로미스의 사유를 그대로 사용합니다.

  ![https://www.javascripttutorial.net/wp-content/uploads/2022/02/JavaScript-Promise.all-Fulfilled-1.svg](https://www.javascripttutorial.net/wp-content/uploads/2022/02/JavaScript-Promise.all-Fulfilled-1.svg)
  ⇒ 배열로 전달한 Promise들이 **모두 성공**(fulfilled)되거나 **하나라도 거부**(rejected)되면 프로미스를 반환해준다.

  - 모두 성공한 경우:  
    인자로 들어왔던 프로미스 순서대로 fulfilled된 Promise와 데이터가 들어있는 배열을 반환
  - 하나라도 거부된 경우:  
    곧 바로 그 거부된 프로미스를 반환

- Promise.allSettled (es2020 이상 / ie 지원 X)

  > 주어진 모든 프로미스가 처리(이행 또는 거부)될 때까지 대기하는 새로운 프로미스를 반환합니다.
  > `Promise.allSettled()`가 반환하는 프로미스는 매개변수로 제공한 모든 프로미스 각각의 상태와 값(또는 거부 사유)을 모아놓은 배열로 이행합니다.

  ![https://www.javascripttutorial.net/wp-content/uploads/2022/02/JavaScript-Promise.allSettled.svg](https://www.javascripttutorial.net/wp-content/uploads/2022/02/JavaScript-Promise.allSettled.svg)
  ⇒ 대기 상태(Pending)가 아닌 상태(fulfilled / rejected)를 포함하는 단어인 settled 상태를 뜻하며 Promise의 결과에 상관없이 모든 Promise가 처리하고 나서 프로미스 배열을 반환한다.
  이 메서드를 이용해서 반복되는 api call을 구현해보았다.

    <aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
    📌 조건:

  지금 조건은 데이터를 여러개 얻어야함.

  그러나 api는 한 번의 하나의 데이터만 주는 상황이라고 가정.

  (실제는 회사에서 사용했던 부분이라 변형하였습니다.)
    </aside>
    
    - 먼저, 그냥 for문을 돌려서 api(혹은 graphql mutation)을 실행했을 경우
        
        postAllData 함수에서 time을 세보았다.
        
        ```tsx
        console.time('post all');
        
        const successList = []
        
        for(id of IdList) { // 모든 IdList를 돌아
          const { data } = await updateData(id); // 하나씩 id를 넣어 api 하나하나 요청
      
          if(data) {
            successList.push(data.id)
          }
        }
        console.log(successList)
        
        console.timeEnd('post all');
        ```        
        
      - 결과
            
        post all - for: 1731.0380859375 ms
        
        console 또한 차례차례 찍힌다. (하나씩 api를 요청하고 데이터를 나타내므로)
        
    - Promise.allSettled를 사용한 경우
        
        allSettled function
        
        idList를 가지고 api call을 실행한 Promise 배열을 allSettled안에 넣는다.
        
        ```tsx
        const postAsyncFunc = await Promise.allSettled(
          idList.map(id => mutation({ id })),
        );
        ```
        
        ```tsx
        console.time('post all');
        
        const dataResponseList = await updateData(id);
        
        const dataFulfilledList = dataResponseList.filter(
          res => res.status === 'fulfilled',
        );
        
        if (dataFulfilledList.length > 0) {
          // api call 중 하나라도 성공한 경우 여기 로직
        }
        
        console.timeEnd('post all');
        ```

        - 결과

          post all - allSettled: 698.612060546875 ms

        그냥 for문을 돌려서 하나하나씩 api을 쏘았을 때와 비교하면 **엄청난 차이**가 발생한다.

        심지어 1000개, 10000개도 아닌 5개를 돌렸는데 이렇게 차이가 난다… 😱

        그냥 for문으로 돌려서 하나씩 쏘았을 경우, 사실 상 하나를 요청해서 응답을 받고, 응답이 오면 그 다음 요청을 하고 …. 이런 방식인 것이다. (여기에선 요청하고 답변을 받아야하므로)

        더 안좋은 것은 비동기 수행 개수가 많아질수록, 마케팅 이메일 송신일 경우 따로 응답 데이터가 필요하지 않다면 보내고, 보내고, 보내고,…

        서버 네트워크와 엄청나게 많은 연결을 갖게 되고 잘못하면 서버를 죽일수도 있다. 죽진 않더라도 엄청 느릴 것이다.

        그래서 병렬적으로 비동기 반복을 진행하고, 순서가 상관이 없는 경우에는 promise all 혹은 allSettled 와 같이 **promise api를 사용**하는 것이 거의 옳다고 볼 수 있겠다.

        **promise allSettled의 동작 방식**은

        처음 Id들을 다 가져와서 promise를 trigger 한다.

        trigger 해서 promise가 담긴 배열을 promise.allSettled안에 넣으면 병렬적으로 요청이 되고, 모든 HTTP 연결이 닫힐 때까지 기다린다. 이후 모두 settled가 되면(resolve, reject 관계 없이) 데이터를 마음대로 사용할 수 있는 것이다! ☺️

        **error handling은 catch로 할 수 없다.**

        반환 형태가 promise가 담겨있는 배열의 형태이므로 모든 비동기문이 처리가 되어도 error 코드가 될 수 없다. (심지어 모두 실패해도 catch에서 잡지 않는다. / error가 발생하지 않아서 catch를 찾지 않는다.)

        각각의 error를 알고 싶으면 reason 프로퍼티를 사용하면된다.

        **요약: 따발총 승**

- Promise.any

  > 주어진 모든 프로미스 중 하나라도 이행하는 순간, 즉시 그 프로미스의 값으로 이행하는 새로운 프로미스를 반환합니다.
  > ⇒ 인자로 들어온 프로미스 중 하나라도 fulfilled 되면 그 프로미스를 반환

- Promise.race
  > 주어진 모든 프로미스 중 하나라도 처리될 때까지 대기하는 프로미스를 반환합니다.
  > 반환하는 프로미스가 이행한다면, 매개변수의 프로미스 중 첫 번째로 이행한 프로미스의 값으로 이행합니다.
  > 반환하는 프로미스가 거부된다면, 매개변수의 프로미스 중 거부된 첫 프로미스의 사유를 그대로 사용합니다.
  > ⇒ 인자로 들어온 프로미스 중 하나라도 settled 되면 프로미스를 반환

### 참고 아티클

[https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global*Objects/Promise#인스턴스*메서드](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise#%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4_%EB%A9%94%EC%84%9C%EB%93%9C)[https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/allSettled](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise/allSettled)

<hr>
