---
layout: single
title: "[Project] Etsy S3 배포 및 github actions 자동 배포"
categories: Project
tag: [Project, React, 개인 프로젝트, Typescript, 클론, react-hook-form]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**개인 프로젝트**

## React 개인 프로젝트 (Typescript)

### 배포

#### Amazon s3 배포

버킷 만들기

퍼블릭 엑세스 차단 해제

build 폴더 하위에 있는 모든 파일 및 폴더를 upload 한다.

정적 웹 사이트 호스팅 활성화

웹 페이지 주소 들어가서 배포 확인

github actions를 위해 추가로 설정해야 할 것

권한에 버킷 정책 생성 시

```
"s3:PutObject",
"s3:PutObjectAcl",
"s3:GetObject",
"s3:GetObjectAcl",
"s3:DeleteObject"
```

포함

객체 소유권 또한 ACL 활성화로 변경, 버킷 소유자 선호로 변경 (github에서도 해당 버킷으로 접근할 수 있도록)

#### github actions 자동 배포

먼저, 내 s3 버킷에 접근이 가능하도록 설정 => IAM 신규 사용자 생성

AWS 오른쪽 상단 id에 보안 자격 증명 (credentials)
-> 엑세스 관리 -> 사용자 -> 사용자 추가 (엑세스 키 - 프로그래밍 방식 액세스)

기존 정책 직접 연결에서 s3 검색

S3FullAccess 선택

태그 추가는 안해도 됨

사용자 만들기

아래와 같은 키 받기

액세스 키 ID:
AKIA3VIDVTE226CQYRBV

보안 액세스 키:
i+KfAFgMqCQnWBvZLFndGvyi6gFh/3WCcx/+owHf

해당 키를 github setting 에 secret을 사용

다시 볼 수 없기 때문에 잘 보관할 것

```yml
name: Deploy to Production

on:
  push:
    branches:
      - master # branch는 master로 설정

jobs:
  deploy:
    name: Build, Deploy to S3 bucket
    runs-on: [ubuntu-latest]

    strategy:
      matrix:
        node-version: [14.15.x]

    steps:
      - uses: actions/checkout@v2

      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v1
        with:
          node-version: ${{ matrix.node-version }}

      # yarn insatll 하는데 The engine "node" is incompatible with this module 오류 발생하여 engines을 ignore하기 위해 다음과 같이 설정
      - name: Yarn
        run: yarn install --ignore-engines

      # yarn build
      - name: Build
        run: yarn build

      # s3 설정
      - name: Transfer to S3 for serving static
        uses: jakejarvis/s3-sync-action@master
        with:
          args: --acl public-read --follow-symlinks --delete
        env:
          AWS_S3_BUCKET: "${{ secrets.AWS_BUCKET_NAME }}" # 내 s3 bucket 이름
          AWS_ACCESS_KEY_ID: "${{ secrets. AWS_IAM_MANAGER_KEY_ID }}" # key id
          AWS_SECRET_ACCESS_KEY: "${{ secrets.AWS_IAM_MANAGER_SECRET_ACCESS_KEY }}" # secret key
          AWS_REGION: "${{ secrets.AWS_BUCKET_REGION }}" # us-east (지역)
          SOURCE_DIR: "build" # 올릴 파일 위치(build된 결과물 있는 곳)
```

참고

https://yung-developer.tistory.com/111
https://velog.io/@bluestragglr/Github-Action%EC%9C%BC%EB%A1%9C-%EB%B0%B0%ED%8F%AC-%EC%9E%90%EB%8F%99%ED%99%94%ED%95%98%EA%B8%B0
https://velog.io/@seeh_h/AWS%EC%99%80-github-action%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%B4-%EB%B0%B0%ED%8F%AC-%EC%9E%90%EB%8F%99%ED%99%94-%ED%95%98%EA%B8%B0

<hr>
