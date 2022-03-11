---
layout: single
title: "[Course / TIL] Wanted Pre Onboarding FE Course 3주차 - 팀 과제 6 제출 / 발표"
categories: Course
tag: [Course, TIL, Wanted, PreOnboarding, Vue]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Course

**Wanted Pre Onboarding FE Course**

## 팀 프로젝트

### 구현

Vue를 이용하여 Tab 컴포넌트를 구현하였다.

#### 형제 컴포넌트 간 데이터 공유

- eventBus

형제 컴포넌트 끼리 데이터를 공유할 수 있도록 한다.

부모 컴포넌트를 공유하고 있는 컴포넌트끼리만 가능한 듯 하다.

```
A
- B
- C
  - D
```

B와 D는 바로 eventBus를 통한 데이터 공유가 되지 않아 D에서 C로 emit을 해준 후 B와 C의 상태를 공유하였다.

Tab component (즉, 위의 상황에서 D)

```jsx
<template>
  <button :class="[checkFocus, 'tab']" @click="onTabClick">
    <Icon
      :isDouble="tabOrder === 'tab1' ? true : false"
      :isCompany="tabOrder === 'tab3' ? true : false"
    />
    <span :class="[checkFocus, 'type']">{{ tabName }}</span>
  </button>
</template>

<script>
import Icon from './Icon.vue';

export default {
  name: 'TabComponent',
  components: {
    Icon,
  },
  props: {
    tabOrder: String,
    tabName: String,
    value: String,
  },
  computed: {
    checkFocus() {
      return this.tabOrder === this.value ? 'focus' : '';
    },
  },
  methods: {
    onTabClick() {
      this.$emit('input', this.tabOrder);
    },
  },
};
</script>
```

Tabs component (즉, 위의 상황에서 C)

```jsx
<template>
  <div>
    <div class="tab-container">
      <Tab
        :key="index"
        v-for="(tab, index) in tabs"
        :tabOrder="tab.order"
        :tabName="tab.content"
        v-bind="tab"
        v-model="currentTab"
      />
    </div>
  </div>
</template>

<script>
import { eventBus } from '../main.js';
import Tab from './tabs/Tab.vue';

export default {
  name: 'TabsComponent',
  components: {
    Tab,
  },
  props: {
    tabName: String,
  },
  data() {
    return {
      currentTab: 'tab1',
      tabs: [
        { order: 'tab1', content: '모두' },
        { order: 'tab2', content: '본인' },
        { order: 'tab3', content: '회사' },
      ],
    };
  },
  created() {
    this.$on('input', (tab) => {
      this.currentTab = tab; // 하위 컴포넌트 tab에서 받은 데이터
    });
  },
  watch: {
    currentTab() {
      eventBus.$emit('selectTab', this.currentTab); // currentTab이 바뀔때마다 emit
    },
  },
  computed: {
    current() {
      return this.tabs.find((el) => el.order === this.currentTab) || {};
    },
  },
};
</script>
```

Tabs component (즉, 위의 상황에서 B)

```jsx
<script>
import { eventBus } from '../main.js';
import { Radar } from 'vue-chartjs';
import data from '../assets/data/result.json';

export default {
  extends: Radar,
  name: 'RadarGraph',
  data() {
    return {
      myData: data.myData,
      samsung: data.samsung,
      kakao: data.kakao,
      lgcns: data.lgcns,
      currentTab: '',
    };
  },
  created() {
    eventBus.$on('selectTab', (tab) => {
      this.currentTab = tab; // Tabs에서 받은 tabOrder(tab1, tab2, tab3)가 들어온다.
    });
  },
  beforeDestroy() {
    eventBus.$off('selectTab'); // eventBus 객체는 항상 쌓이므로 off를 해주어야 한다.
  },
  watch: {
    currentTab() {
      console.log(this.currentTab); // 확인용
    },
  },
  mounted() {
    this.renderChart({
      labels: Object.keys(this.myData),
      datasets: [],
    });
  },
};
</script>
```

### 더블엔씨 과제 발표

#### 간단한 서비스 소개

**구현 과제 소개**

이번에 저희는 더블엔씨 과제를 수행했습니다.

충북 휴양림 데이터를 이용하여 메모와 함께 저장하는 과제였습니다.

**상세 기능**

기능을 말씀드리자면,

메인페이지와 목록 페이지로 나누어져 있어 목록 페이지에서는 먼저 충북 휴양림 데이터를 fetching하여 리스트로 나타냅니다.

이 목록에서 요소를 선택하여 모달을 띄운 다음 메모를 입력받은 후 저장을 하여 메인 페이지로 이동했을 때 휴양림 정보와 입력한 메모를 가진 요소가 있어야 합니다.

메인 페이지 목록 페이지 모두 무한 스크롤을 구현해야 합니다.

또한, 정보를 저장할 때 로컬 스토리지를 사용하여야 합니다.

동작마다 사용자에게 notification을 주어야 합니다. 저장 완료, 저장 실패, 삭제 완료 등등

추가적으로 메인 페이지에서 입력값을 받아 filtering 기능까지가 상세 기능입니다.

#### 사용 스택

react : React를 이용하여 개발하였습니다.

typescript: 처음으로 Typescript를 도입하여 코드 안정성을 조금 더했습니다.

emotion : styled-component와 사용성은 비슷하나 용량이 좀 더 가벼운 장점으로 선택을 하게 되었는데, 사용하고 보니 css prop을 이용해서 inline으로 css를 작성하여 편리하게 스타일을 확장할 수 있던 것이 좋았습니다.

추가적으로 기능 명세 정리 및 컨벤션은 노션을 이용하였습니다.

#### 배포

netlify 자동 배포

#### 주제별 발표

**웹 접근성**

저희는 웹 접근성을 고려하여 모달을 제작하였습니다.

일단 리스트에서 마우스를 사용하지 않고도 모든 유저가 잘 사용할 수 있도록 하는 것이 목적이었습니다.

따라서 다음과 같이 구현하고자 했습니다.

```
tab / Shift tab으로 클릭 가능 요소들 focus
Enter시 클릭 동작
Esc로 모달 끄기
모달 켜지면 모달 내에서만 tab이 돌아다니도록 하기
모달 끄면 마지막으로 focus 되어 있던 요소 그대로 focus
```

가 있었습니다.

**input rendering 개선**

그 다음 설계 후 공통으로 사용 가능한 Layout을 생성했습니다.

Header와 Bottom buttons, 돌봄 title과 step을 나타내주는 부분이 단계들이 모두 동일했기 때문에 Layout을 컴포넌트로 제작하여 children을 받아 유형 선택, 스케쥴 선택 등을 넣으면 되도록 구성하여 통일성을 높이고 효율성 높였습니다.

## 회고 (TIL)

**2022.03.11 Daily 회고**

✏오늘 한 일

- 팀 과제 수행 및 제출
- 발표 준비 및 발표
- 면접 대비 공부

⁉느낀 점

vue 나는 진득하니 앉아서 삽질하면서 배워야 할 것 같다. 2일 안에 끝낼 수 있는 역량은 못 되는 것 같다. 그리운 React...

발표는 잘 모르겠지만 발표 준비는 도움 많이 되는 것 같다. 리마인드도 된다.

면접 대비 공부하자

🎃현재 나의 상태

벼락치기보다는 꾸준함이다. 너무 꾸역꾸역하지는 말자.

다음 주 어떤 과제가 나올까 두려운 상태

<hr>
