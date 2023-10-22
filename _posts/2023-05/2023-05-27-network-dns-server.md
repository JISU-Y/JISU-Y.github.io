---
layout: single
title: "[CS] Network - DNS server"
categories: CS
tag: [cs, network, dns, route53, aws]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Network - DNS 서버

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 AWS - Route53이 뭔데?

사실 Route53 자체를 알아보려다 제너럴하게 DNS에 관련되어 알아보게 됨.

</aside>

## Route53

“DNS service”

> Amazon Route 53과 같은 DNS 서비스는 전 세계에 배포된 서비스로서, `www.example.com`과 같이 사람이 읽을 수 있는 이름을 `192.0.2.1`과 같은 숫자 IP 주소로 변환하여 컴퓨터가 서로 통신할 수 있도록 합니다.  
> [출처]([AWS - Route53](https://aws.amazon.com/ko/route53/what-is-dns/))

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
👉 DNS?

Domain Name Service

ip 주소와 사람이 이해할 수 있는 영어 알파벳으로 된 주소를 매핑.

- domain name: `naver.com` 과 같은 web site address
- host name: `www.`, `blog.`, `study.`, `news.`, `sports.` 등등 (sub-domain)
</aside>

## 도메인 등록

`www.runhae.com` 이 도메인을 사용하고 싶으면 어디다가 말해야 할까?

⇒ 도메인을 인터넷 상에 등록해야 함.

### 등록대행자 (Registrar)

요청한 도메인 네임을 도메인 서버에 등록해줄 수 있도록 해줌.

ex) 가비아, goDaddy, Route53 등등

- 도메인 사는 중  
  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-05/01-buying-domain.jpg)

### 등록소 (Registry)

도메인 네임이 저장되는 공간

ex) gTLD(.com, .net, .org …) / ccTLD(.kr, .jp, .cn, .us …)

- Top-level domain이라고도 함.
  - 일반 최상위 도메인(generic top-level domain, gTLD): 조직 계열에 따라 다르게 사용
    [도메인 목록](https://ko.wikipedia.org/wiki/%EC%9D%BC%EB%B0%98_%EC%B5%9C%EC%83%81%EC%9C%84_%EB%8F%84%EB%A9%94%EC%9D%B8)
  - 국가 코드 최상위 도메인(country code top-level domain, ccTLD): 국제적으로 나라 혹은 특정 지역에 따라 사용
    [도메인 목록](https://ko.wikipedia.org/wiki/%EA%B5%AD%EA%B0%80_%EC%BD%94%EB%93%9C_%EC%B5%9C%EC%83%81%EC%9C%84_%EB%8F%84%EB%A9%94%EC%9D%B8)
- .com 등록소는 .com에 대한 도메인들을 가지고 있다.

⇒ 여기에 도메인을 등록하려면 우리는 구매대행자(Route53)을 통해서 도메인 구매 및 설정

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
👉 잠깐! Domain의 hierarchy

`www` . `runhae` . `com` . `root`

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-05/01-dns-hierarchy.gif)

- **.root**: 가장 상위 도메인. 어떤 도메인이든 root를 가지고 있음. 사실 이름은 따로 없음. 빈 문자열임.
- **.com**: Top-level domain. .com의 도메인들을 가지고 있음.
- **.runhae**: domain name. 도메인 이름.
- **www**: sub-domain. `blog.` , `study.` , `shop.` 등등
</aside>

## Domain Name Resolution

**주소창에 주소를 입력하면?**

유저가 주소창에 `www.runhae.com`을 치면 그 주소 IP가 어떻게 되는지.

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-05/01-dns-name-resolution-drawio.png)

### DNS server에 요청

처음 접속한 경우 `www.runhae.com` 이 어디인지 알 수 없다.

Local DNS 서버 (네임 서버)에 요청한다 (해당 도메인과 호스트명의 IP를 가지고 있는지)

- ☝️ Local DNS 서버는 어디에 있는 것?  
  로컬 DNS 서버는 통신사 마다 저장하는 곳 존재
  그러나 사용자가 임의로 변경 가능!  
  ex) IP 주소를 변경하여 구글에 접근이 더 빠른 주소로 변경 가능  
  ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-05/01-dns-address.jpg)

**캐싱되어 있는 게 있다면**

- 주소를 바로 반환.

**캐싱이 되어 있지 않으면**

- root name server에 요청  
  .com 이므로 root는 .com 네임 서버 내에 주소가 있다는 것을 알려줌.
  - ☝️ Root Name Server는 어디에 있는 것?  
    전세계에 13개 서버 수 제한.  
    그러나 애니캐스트 어드레싱을 통해 1034개의 서버 수 (한국에도 있음)  
    ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-05/01-root-name-server.png)
- .com name server  
  runhae.com이 있음.
  그게 등록 대행자 Route53에 매핑이 되어 있다는 것을 알려줌.
- Route53 name server  
  여기서 우리가 설정해두었던 도메인 이름(runhae.com)과 ip 주소가 매핑되어 있음.
  (`www.runhae.com` - `A` - `12.123.24.356`)

## Caching

매번 DNS server 및 Root name server 등 에 물어보는 것 보다 어딘가 cache에 저장되어 있으면 훨씬 빠를 것.

### DNS Server caching

DNS server에 DNS 요청을 보내 도메인 이름을 해당 IP 주소로 확인

DNS 서버는 이전에 해당 도메인 이름을 확인한 경우 캐시된 IP 주소로 응답

⇒ 효율성 및 성능 개선

👉 <strong>TTL (Time-to-Live) - 타임 투 리브</strong>

> DNS 레코드의 변경사항이 적용될 때까지 걸리는 시간(초)

- TTL을 길게 잡으면 캐시를 그만큼 길게 가지고 있기 때문에 언제 접속해도 빠르게 보여줄 수 있음.
- 대신 안정화가 안되어 있거나 자주 바뀌는 도메인이라면 TTL을 짧게 가져가야 변경된 사이트를 serving 할 수 있음.

### Local DNS Caching

각 개인 컴퓨터마다 IP 주소를 캐싱

DNS 서버에 요청 시 로컬 DNS 캐시에 응답 값을 저장.

⇒ 효율성 및 성능 개선

OS 자체 혹은 개인이 수동으로 지우거나 주기적으로 지우도록 설정 가능.

## DNS Record

> 도메인에 매핑된 IP 주소 및 해당 도메인에 대한 요청 처리 방법에 대한 정보 제공

### A Record

도메인 네임에 IP 주소를 가지고 있는 레코드

- 도메인 - 서버 IP 직접 연결 (직통 연결이므로 빠름)

### MX Record

'메일 교환'(MX) 레코드

- 이메일을 이메일 서버로 전송

### CNAME

도메인이나 하위 도메인을 다른 도메인으로 전달. IP 주소를 제공하지 않음

- 도메인을 별명으로 연결
- IP가 유동적일 때 사용할 수 있음

**예시**

`blog.runhae.com`에 `runhae.com`을 CNAME 값으로 사용하여 레코드 set

DNS 서버는 `blog.runhae.com`으로 요청이 오면 실제로 `runhae.com`의 IP 주소를 A 레코드를 통해 반환

→ 따라서 blog.로 들어와도 runhae.com의 페이지가 보여짐.

\*그러나 실제로 blog.의 해당 IP 주소에 직접 연결하면 runhae.com이 아닌 blog가 보여진다.

\*MX 및 NS 레코드는 CNAME 레코드를 가리킬 수 없음.

### 참고 링크

[생활코딩](https://www.youtube.com/watch?v=AnViePe2mj8&list=PLuHgQVnccGMCas8a4f0uIg5X4uERoG6gb&index=1&ab_channel=%EC%83%9D%ED%99%9C%EC%BD%94%EB%94%A9)

[얄팍한 코딩사전](https://www.youtube.com/watch?v=6fc9NAQkcv0&ab_channel=%EC%96%84%ED%8C%8D%ED%95%9C%EC%BD%94%EB%94%A9%EC%82%AC%EC%A0%84)

[Networking Class](https://www.youtube.com/watch?v=m03E2s_lW2w&t=39s&ab_channel=NetworkingClass)

[CODNS](http://www.codns.com/b/B05-115)

[Cloudflare](https://www.cloudflare.com/ko-kr/learning/dns/dns-records/)

[AWS - Route53](https://aws.amazon.com/ko/route53/what-is-dns/)

<hr>
