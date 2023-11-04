---
layout: single
title: "[SEO] SEO 어떻게 하라는 걸까?"
categories: SEO
tag: [SEO]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# SEO와 그 최적화 방법

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
💡 SEO가 뭔지는 많이 외웠지만 설정하는 방법, 최적화하는 방법은 완전 백지 수준.
</aside>

## SEO

### SEO란?

서치 엔진 옵티마이제이션? 검색 엔진 최적화..  
내 사이트가 검색 결과에 잘 뜨도록 설정해주는 거..?

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
☝ SEO는

사용자가 구글, 네이버 등 검색 엔진에서 어떤 키워드를 검색 할때 검색 결과에 잘 노출되도록 하는 최적화 방법.

</aside>
<span style='color: gray;'>  
\*검색 엔진 마다 다를 수 있음.
</span>

### SEO하면 뭐가 좋지?

- 검색 결과에 잘 노출된다.
  - 어떻게 잘 뜨는 거지?  
    ⇒ 구글봇 이라는 친구가 잘 긁어가서 검색 결과 상위에 노출시켜 줌.
- 광고, 마케팅 비용을 들이지 않고도 비즈니스에 고객을 상당수 유치할 수 있다.
  - 점유율이 한번 유지되면 경쟁사는 이걸 뛰어 넘는 데에 두세배가 넘는 리소스를 투입해야 한다고 한다.

## SEO 최적화 그래서 어떻게 하지?

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
👉 SEO 최적화 방법

- 고품질의 컨텐츠 생성 → 전문 마케터의 영역
- 테크니컬 SEO → 개발자의 영역 (+ 기획, 디자이너)
</aside>

SEO를 하려면 **search engine**에 대한 이해도가 필요하다.

예를 들어 구글 SEO는

- 모바일을 중요시함.
- 컨텐츠가 매우 중요.
- 메타 태그, 타이틀 태그, 캐노니컬 태그 등등
- 페이지 로딩 속도

비교하자면 네이버는

- 이전에는 정기적인 포스팅(매일 하나씩)  
  -> 하지만 이제는 양질의 콘텐츠가 더 중요해졌다고 함.
- 경험 중심의 컨텐츠인지, 방문자 반응이 좋은지.
- 다양한 카테고리 보다 하나의 전문성있는 카테고리 컨텐츠가 더 좋다고 함.

[출처: SEO (검색엔진 최적화) 구글, 네이버 가이드 총정리 2023](https://seo.tbwakorea.com/blog/seo-guide-2022/)

### 고품질의 컨텐츠 생성

좋은 컨텐츠의 기준 및 양산 방법

- 제목 태그 중요
  - 타겟 키워드 명시
  - 타겟 키워드 하나만
  - 설득력 있는 제목 작성
  - 이미지 첨부 및 저장 시 설명을 포함하여 네이밍
- 내부 링크
  - 인터널 링크. 내부 컨텐츠와 연결
  - 사이트 내 다른 페이지의 링크를 컨텐츠에 추가한다.
  - 오래되고 인기 많은 컨텐츠에 새로운 컨텐츠 링크
- 고품질의 컨텐츠가 테크적인 부분 보다 더더 중요.
  - 참조가 많이 되기 위해(백링크) 좋은 컨텐츠 제작 필요
  - 시리즈 형식 바이럴 잘 됨.
    ex) 돈 많이 벌기 시리즈 (1) ..

### 테크니컬 SEO

- 구글봇 크롤링, 페이지 인덱싱
  - XML 사이트맵
  - 404 페이지
  - 301 리디렉션
  - 중복 컨텐츠 방지
    - content=noindex,follow 붙여주면 됨.
    - 혹은 robots.txt에서 크롤러 차단
    - 캐노니컬 태그 사용 → (`rel="canonical"`) 원조 페이지라는 것을 알려줌.
- 위 사항 이외에 추가 사항 고려 필요
  - 웹 사이트 구조  
    ![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2023-09/02-homepage-structure.png)
    - 잘못 설계 시 크롤링 및 인덱싱 문제 발생
    - 사이트 구조는 일반적으로 depth가 깊지 않아야 한다.
    - 깊으면 모든 페이지가 인덱싱이 되지 않을 수도 있다.
  - URL 구조
    - URL 네이밍
  - 웹 사이트 로딩 속도
    - 구글 서치 콘솔, 라이트하우스, 구글 인사이트
  - 메타 태그

## 그래서 당장 해볼 수 있는 건 뭘까?

<aside style='background-color : gold; opacity: 0.8; padding: 10px 20px'>
👉 방법

- 사이트맵 및 메타 태그 추가 및 수정
- 코어 웹 바이탈 검사 및 관련 이슈 수정
- 접근성 및 적절한 태그 사용
</aside>

### 사이트맵

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
☝ <strong>사이트맵이란?</strong>

사이트에 있는 페이지, 동영상 및 기타 파일과 그 관계에 관한 정보를 제공하는 파일

</aside>

사이트맵(XML)은 웹 크롤러가 사이트를 인식하는데 맵 역할을 해주어 인덱싱이 잘 되게 도와준다.  
(더 효율적으로 크롤링)

```jsx
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9" xmlns:news="http://www.google.com/schemas/sitemap-news/0.9" xmlns:xhtml="http://www.w3.org/1999/xhtml" xmlns:mobile="http://www.google.com/schemas/sitemap-mobile/1.0" xmlns:image="http://www.google.com/schemas/sitemap-image/1.1" xmlns:video="http://www.google.com/schemas/sitemap-video/1.1">
<url><loc>https://www.runhae.com</loc><lastmod>2023-08-22T08:14:44.269Z</lastmod><changefreq>daily</changefreq><priority>0.7</priority></url>
<url><loc>https://www.runhae.com/blog</loc><lastmod>2023-08-22T08:14:44.269Z</lastmod><changefreq>daily</changefreq><priority>0.7</priority></url>
<url><loc>https://www.runhae.com/project</loc><lastmod>2023-08-22T08:14:44.269Z</lastmod><changefreq>daily</changefreq><priority>0.7</priority></url>
</urlset>
```

### 구글 서치 콘솔 - 코어 웹 바이탈

- 구글이 매우 중요하게 생각하는 UX
  - 웹 사이트 로딩 속도 빠르게
  - UI/UX 친화적으로
  - 모바일 사용성 좋게

**로딩 속도**

- 이미지 고해상도 사용 시 압축 필요
- [구글 인사이트](https://pagespeed.web.dev/?utm_source=psi&utm_medium=redirect) 여기서 로딩 속도 확인하여 개선
- 인터랙티브
- 비주얼 스태빌리티

→ 순위에 엄청나게 많은 영향이 있는 것은 아니지만 중요한 부분으로 인식되어 리소스를 할당하여 개선하는 것을 권장.

### **접근성 및 적절한 태그 사용**

- 이미지 alt 어트리뷰트 이용
  - 검색 엔진 로봇은 이미지를 이해하기 위해 alt 텍스트 읽음.
- url에 키워드 포함
  - URL에 키워드가 들어가 있는 경우 그렇지 않은 URL보다 클릭수가 50% 더 높다고 함.
    ex) `www.example.com/blog/how-to-make-money` ↔ `www.aws239.example.wer/93djf48/ldkfsl&23o39slkjdf#main`
- 적절한 태그 사용
  - 사실 효과 미미하다고 함.  
    (제목을 나타내는 h1은 효과 있을 수도)
  - h1은 페이지 당 하나만 있는 것이 좋다고 함.
  - H1 / H2 : H2 태그는 무조건 사용. 관련성 높다고 인지.

### 이외에 추가적으로 해볼 수 있는 것들 많음.

<aside style='background-color : transparent; color: black; opacity: 0.8; border: 1px solid black; padding: 10px 20px'>
👉 참조

[구글 검색엔진 최적화(SEO) 신규 로직 67가지](https://careerly.co.kr/comments/74894?from=search-result&fromArea=tab-comment)

</aside>

- 검색 엔진은 질답 및 사용자와 인터랙션을 중요하게 생각한다고 함.
  - 리뷰 기능, 질의 응답 기능, 댓글 기능 등등
  - 자체 생산 컨텐츠가 아니더라도 오가닉 트래픽에 좋은 영향을 줌.
- 도메인에 타겟 키워드 앞에 배치
- 제목 태그에도 타겟 키워드 꼭 넣기
- 메타 디스크립션
  - 넣는 다고 무조건 도움되는 건 아님. 클릭률을 높일 수 있음.
- 컨텐츠 업데이트
  - 블로그 글 같은 포스팅은 주기적 컨텐츠가 중요.
  - 오타 수정 및 과거 포스팅도 상관 없음.
- 깨진 링크 수정 (404)

## 한다고 적용되나? 언제 효과를 볼 수 있나?

[출처: how-long-does-seo-take](https://ahrefs.com/blog/how-long-does-seo-take/)

위 아티클에 따르면 SEO 작업 변경 사항이 적용되기까지 보통 3-6개월 정도 걸린다고 함.

#### 오래걸리는 이유

그렇다면 왜 이렇게 SEO의 결과가 오래 걸릴까요?

1. 오래된 사이트가 신규 사이트에 비해서 백링크들이 훨씬 많기 때문
   - 보통의 검색 엔진은 백링크를 다른 웹사이트의 지원을 받는 양질의 콘텐츠 지표로 봄.
   - 신규 사이트의 경우 오래된 사이트 보다 SEO가 비교적 오래 걸림.
2. 경쟁
   - 혼자만 SEO하는게 아니고, 경쟁자들도 당연히 SEO하고 있음
3. 전략의 필요성
   - 중요 키워드중에서 경쟁이 약한 곳에 먼저 투자하면 조금씩 트래픽을 늘려나갈 수있음
4. 실행
   - 그냥 실행에 옮기는 게 더 빠르게 효과를 볼 수 있는 길.

## 그래서 이거 언제 다해?

[출처: 돈 안 들이고 트래픽과 매출을 얻는 테크니컬 SEO의 모든 것](https://ppss.kr/archives/263429)  
~~(사실 디지털 마케팅 회사 홍보 느낌도 나긴 하지만 어쨌든.. 큼..)~~

개발자 혼자 다 할 수 있는 거 아니다.  
오히려 안 좋다. 테크니컬 SEO는 아예 다른 스페셜 리스트 직군이다.

세팅 해봤자 꾸준히 해야 하는데, 일시적이고 그 사람 떠나면 공백이 생기고 다시 도루묵이 될 수도 있다.

처음에 잘 해놓으면 경쟁사는 그 몇배를 투자해야 할 정도로 힘을 써야 한다.

그래서 그냥 개발자 리소스를 쓰는 것 보다 에이전시에 맡기거나 스페셜리스트를 고용하는 편이 낫다.

개발자 혼자 끙끙 앓는 것은 금물!

### 참고 링크

https://wormwlrm.github.io/2023/05/07/SEO-for-Technical-Blog.html

https://seo-marketing.co.kr/8

https://seo-marketing.co.kr/42

https://seo-marketing.co.kr/43

<hr>
