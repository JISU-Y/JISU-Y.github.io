---
layout: single
title: "[Project] React 개인 프로젝트 React with Typescript 연습"
categories: Project
tag: [Project, React, 개인 프로젝트, 토이 프로젝트, Typescript]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

# Project

**React 개인 프로젝트 React with Typescript 연습**

## React 개인 프로젝트

### React with Typescript

#### cra with typescript

```
yarn create react-app --template typescript <폴더명>
```

create react-app과 생성하려는 폴더명 사이에 template typescript를 넣어준다.

```jsx
import React, { useState } from "react"
import logo from "./logo.svg"
import "./App.css"

interface IState {
  people: {
    name:string
    age: number
    url: string
    note?: string // string | undefined
  }[]
}

function App() {
  // 배열 안에 object 해놓으면 state는 알아서 타입을 고정해둔다.
  // 또한, 이 state를 사용할 때에는 해당 object의 property가 아니면 사용할 수 없도록 해준다.
  const [people, setPeople] = useState([{
    name: 'a',
    url: '',
    age: 36,
    note: 'whatever'
  }, {
    name: 'b',
    url: '',
    age: 30,
  }])
  // 만약 빈 배열로 선언했다면 state는 안에 어떤 타입이 들어가는지 알 길이 없다.
  // 따라서 useState 사이에 배열 안에 object 타입을 지정해준다.
  // 하지만 너무 길어지기 때문에 위에서 interface로 지정해주어서 사용하는 것이 좋다.
  const [people, setPeople] = useState<{age: number, name: string}[]>([])

  // interface로 미리 타입을 지정해준 것을 사용한다.
  // IState에 있는 people의 types을 사용할 것
  const [people, setPeople] = useState<IState["people"]>([])

  return (
    <div className="App">
      <h1>People Invited to my party</h1>

    </div>
  )
}

export default App

```

```jsx
// people을 prop으로 받으려고 할 때 type을 지정해주어야 한다.
// people은 밖에서 지정한 타입과 동일하기 때문에 그대로 가져와서 IProps으로 사용하고,
// 디스트럭처링 옆에 : IProps 로 사용할 수 있다.
const List = ({ people }: IProps) => {
  return <div>List</div>;
};

// props 안에 사용하는 것이 불편하다면
// 컴포넌트명에서 바로 props 타입을 지정해 줄 수 있다.
// FC는 functional component
// IProps는 위에서 정의한 types
const List: React.FC<IProps> = ({ people }) => {
  return <div>List</div>;
};
```

```jsx
const List: React.FC<IProps> = ({ people }) => {
  // : JSX.Element[]를 하면 무조건 요소를 return 해주도록 해야하므로
  // 써주면 return을 안 썼을 경우 error를 띄워준다.
  const renderList = (): JSX.Element[] =>
    people.map((person) => (
      <li className="List">
        <div className="List-header">
          <img className="List-img" src={person.url} alt="profile" />
          <h2>{person.name}</h2>
        </div>
        <p>{person.age} years old</p>
        <p className="List-note">{person.note}</p>
      </li>
    ));

  return <ul>{renderList()}</ul>;
};
```

```jsx
import { IState as IProps } from "../App"; // 이런 식으로 types를 모아둔 곳에서 import 받아서 사용한다.

// interface IProps {
//   people: {
//     name: string
//     age: number
//     url: string
//     note?: string
//   }[]
// }
```

interface를 계속 해서 같은 것을 선언해줄 수는 없기 때문에
types를 지정해 둔 곳에서 export하여 사용하는 곳에서 import하여 사용한다.

```jsx
import React, { useState } from "react"
import { IState as Props } from "../App"

interface IProps {
  people: Props["people"] // 밖에서 가져온 Props types
  setPeople: React.Dispatch<React.SetStateAction<Props["people"]>> // setPeople (App.js에서 정의한 setPeople에 hover 해보면 이러한 타입을 가지고 있어 복붙하면 된다.)
}
// SetStateAction 안에 있던 이 type은 곧 Props['people']과 같다.
// {
//     name: string;
//     age: number;
//     url: string;
//     note?: string | undefined;
// }[]

// Props를 지정해준 다음 아래와 같이 사용하면 된다
const AddToList: React.FC<IProps> = ({ people, setPeople }) => {
  const [input, setInput] = useState({
    name: "",
    age: "",
    note: "",
    img: "",
  })

  // event이기 때문에 React.ChangeEvent<HTMLInputElement> 타입을 가져다가 사용한다.
  // 그런데 textarea는 element가 다르기 때문에 or로 type을 추가해주어야 한다.
  // 그리고 인수 뒤에 붙는 : void는 이 함수가 아무것도 반환하지 않는다는 것
  const handleChange = (e: React.ChangeEvent<HTMLInputElement | HTMLTextAreaElement>): void => {
    setInput({
      ...input,
      [e.target.name]: e.target.value,
    })
  }

  const handleClick = (): void => {
    if (!input.name || !input.age || !input.img) return // empty value early return

    setPeople([
      ...people,
      {
        name: input.name,
        age: parseInt(input.age), // input에서 받은 것은 string 타입이다 (input 에서 type='number'로 설정해도 string으로 된다.)
        url: input.img, // img: input.img 변수도 다르게 쓰면 바로 알려준다 (setPeople에서는 url 프로퍼티가 있지 img 프로퍼티는 없다.)
        note: input.note,
      },
    ])

    setInput({
      name: "",
      age: "",
      note: "",
      img: "",
    })
  }

  return (
    <div className="AddToList">
      <input type="text" placeholder="Name" className="AddToList-input" value={input.name} onChange={handleChange} name="name" />
      <input type="text" placeholder="Age" className="AddToList-input" value={input.age} onChange={handleChange} name="age" />
      <input type="text" placeholder="Image Url" className="AddToList-input" value={input.img} onChange={handleChange} name="img" />
      <textarea placeholder="Notes" className="AddToList-input" value={input.note} onChange={handleChange} name="note" />
      <button className="AddToList-btn" onClick={handleClick}>
        Add to List
      </button>
    </div>
  )
}

export default AddToList

```
