---
layout: single
title: "[TIL] GraphQL 연습
categories: TIL
tag: [TIL, GraphQL]
toc: true
author_profile: true
sidebar:
  nav: "docs"
---

## TIL

**공부해야할 것들**

### GraphQL

회사에서는 GraphQL을 사용하므로 공부해야겠다고 생각했다.

코드 분석하는 데도 무슨 말인지 전혀 모르니 얼른 연습이라도 해보고 싶어서 30분~1시간 분량의 강의를 들었다.

강의: <https://www.youtube.com/watch?v=YyUWW04HwKY>

간단하게 알 수 있어서 매우 유용했다!

GraphQL 이론은 저번에 정리했었으므로 중복해서 정리하지는 않고,

이번에 들은 강의를 위주로 정리하려고 한다.

### server 구현

rest api는 서버에서 api를 제공하여 그 라우터를 사용해서 데이터를 모두 받아왔다.

graphQL에서는 서버에서 어떻게 구성을 해야 클라이언트에 제공을 해서 데이터를 사용할 수 있도록 하는지를 간단하게 알아보도록 한다.

#### 패키지 설치

```
npm install express-graphql graphql
```

위 패키지를 설치한다.

#### 서버 기본 설정

```jsx
const express = require("express");
const app = express();
const PORT = 6969;
const cors = require("cors");

// creating server (graphql)
app.use(cors());
app.use(express.json());

app.listen(PORT, () => {
  console.log("Server running");
});
```

#### graphQL 서버 설정

express-graphql에서 graphqlHTTP를 이용하여 /graphql의 엔드포인트를 가진 api를 만든다.

rest에서는 api를 만들 때마다 라우터가 생성되지만,  
graphql은 저 엔드포인트 하나면 된다.

이 엔드포인트에 스키마를 전달하고,  
추가적으로 graphiql(postman 처럼 GUI를 제공하여 query와 mutation을 짜고, 데이터를 확인해볼 수 있음)을 사용 여부를 설정할 수 있다.

```jsx
const express = require("express");
const app = express();
const PORT = 6969;
const cors = require("cors");

const schema = require("./Schemas/index");
const { graphqlHTTP } = require("express-graphql");

app.use(cors());
app.use(express.json());

// creating server (graphql)
app.use(
  "/graphql", // endpoint (graphql은 엔드포인트가 딱 하나)
  graphqlHTTP({
    schema, // 스키마
    graphiql: true, // graphql interface 사용하도록 하는 것
  })
);

app.listen(PORT, () => {
  console.log("Server running");
});
```

#### UserType

사용할 데이터의 타입을 지정한다. (type-definition)

```jsx
// typeDefinition
const graphql = require("graphql");
const { GraphQLObjectType, GraphQLInt, GraphQLString } = graphql;

const UserType = new GraphQLObjectType({
  name: "User",
  fields: () => ({
    id: { type: GraphQLInt },
    firstName: { type: GraphQLString },
    lastName: { type: GraphQLString },
    email: { type: GraphQLString },
    password: { type: GraphQLString },
  }),
});

module.exports = UserType;
```

#### Schema

graphqlHTTP에 전달할 스키마다.

스키마에서 query와 mutation을 정의한다.

**query**

query는 rest로 치자면 GET이다.  
data를 가져올 때, fetching할 시 사용한다.

```jsx
const graphql = require("graphql");
const {
  GraphQLObjectType,
  GraphQLSchema,
  GraphQLInt,
  GraphQLString,
  GraphQLList,
} = graphql; // graphQL Type 들
const userData = require("../MOCK_DATA.json"); // Data (여기서는 MOCK Data 사용)

const UserType = require("./TypeDefs/UserType"); // 정의해둔 UserType (firstname: String, lastName: String ...)

// query
const RootQuery = new GraphQLObjectType({
  name: "RootQueryType",
  fields: {
    // 모든 user를 가져오고자 할 때(지금은 MOCK_DATA에서 가져옴) 사용하는 query
    // (rest 였으면 하나의 라우터가 되는 것이다. 하지만 graphql이므로 라우터는 하나!)
    getAllUsers: {
      type: new GraphQLList(UserType), // (UserType의 배열을 받으므로 List(UserType))
      args: { id: { type: GraphQLInt } },
      resolve(parent, args) {
        return userData;
      },
    },
    // getUserById // 또 다른 query
  },
});
```

GraphQLObjectType 안에 객체를 넣어  
name에는 query이름을 지정하고,  
fields 안 객체에 클라이언트에 제공하고자 하는 query들을 나열하면 된다.

예시로는 모든 user의 정보를 배열로 가져오는 getAllUsers를 사용할 수 있도록 제공하도록 만든다고 해본다.

그러면 type은 UserType의 요소가 배열로 나열이 되어 있는 것이므로 GraphQLList로 타입을 지정한다.  
args에는 현재 여기서는 사용하지 않겠지만, id를 이용해서 밑의 resolve function에서 args.id로 사용할 수 있다.  
다음 resolve 함수를 지정해서 전달할 데이터를 return 한다.

#### Mutation

mutation은 Create, Update, Delete을 담당한다.

```jsx
const Mutation = new GraphQLObjectType({
  name: "Mutation",
  fields: {
    // mutations
    createUser: {
      type: UserType,
      args: {
        // id는 db에 저장할 때 생성해주니까 명시하지 않는다.
        firstName: { type: GraphQLString },
        lastName: { type: GraphQLString },
        email: { type: GraphQLString },
        password: { type: GraphQLString },
      },
      resolve(parent, args) {
        // DB logic here
        // DB가 따로 없으므로 userData에 받은 데이터를 배열에 추가하는 것으로 한다.
        userData.push({
          id: userData.length + 1,
          firstName: args.firstName,
          lastName: args.lastName,
          email: args.email,
          password: args.password,
        });
        return args;
      },
    },
    // updateUser
    // removeUser
  },
});
```

mutation 역시 fields 안에 사용할 mutations을 정의한다.

createUser라는 mutation을 제공한다고 할 때,  
type은 UserType으로 받을 것이기 때문에 UserType으로 지정하고,  
args에 사용자에게 데이터를 받아  
resolve에서 args.속성 으로 사용할 수 있도록 한다.

resolve에서는 실제 Database의 로직이 들어가면 된다.  
`DB.query('INSERT') ...` (SQL 모름) 이런식으로 DB에 데이터를 쓰는 로직을 작성하면 된다.  
그러나 여기서는 MOCK DATA를 사용하므로 가져온 userData의 마지막 요소 다음 자리에 하나씩 추가하는 것으로 구현한다.  
return도 사실 필요없으나 확인할 수 있도록 args를 return 한다.

#### 실제 사용 (GraphiQL)

**query**

다음과 같이 입력하면 데이터를 확인할 수 있다.

```jsx
query {
  getAllUsers {
    firstName
    lastName
    email
  }
}
```

결과:

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-04/09_01.png)

**mutation**

```jsx
mutation {
  createUser(firstName: "Pedro",
    lastName:"Machado",
    email: "likeand@subscribe.com",
    password: "notmyactualpassword") {
    firstName
    lastName
    email
  }
}
```

결과:

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-04/09_02.png)

### client 구현

자, 백엔드에서는 graphQL을 사용할 수 있도록 api를 저렇게 제공하고 있다.

그러면 프론트엔드에서는 어떻게 사용할 수 있을까!

보통 apollo-client를 이용하여 query와 mutation을 요청한다.

#### Apollo-client란?

> GraphQL와 클라이언트를 연결해주는 라이브러리
> graphql을 사용해 DB 데이터를 프론트엔드에서 CRUD할 수 있도록 해주는 라이브러리이다.
> 이를 사용하여 Redux를 사용하지 않고 전역 상태 관리가 가능하다.

#### Apollo-client 설치

```
npm install @apollo/cient graphql
```

#### App.js

```jsx
import "./App.css";
import {
  ApolloClient,
  InMemoryCache,
  ApolloProvider,
  HttpLink,
  from,
} from "@apollo/client";
import { onError } from "@apollo/client/link/error"; // apollo client에 error 라이브러리를 포함하고 있다.
import GetUsers from "./Components/GetUsers";
import Form from "./Components/Form";

// graphql에서 에러 캐치 시 동작
const errorLink = onError(({ graphqlErrors, networkError }) => {
  if (graphqlErrors) {
    graphqlErrors.map(({ message, location, path }) => {
      alert(`Graphql error ${message}`);
    });
  }
});

const link = from([
  errorLink,
  new HttpLink({ uri: "http://localhost:6969/graphql" }),
]); // graphql 라우터와 연결

const client = new ApolloClient({
  cache: new InMemoryCache(), // 캐시
  link: link, // 백엔드(서버)와 연결된 link
});

function App() {
  return (
    <ApolloProvider client={client}>
      <Form />
      <GetUsers />
    </ApolloProvider>
  );
}

export default App;
```

ApolloProvider를 감싸고 client를 props로 전달하는 형태이다.

#### 컴포넌트에서 사용 (query)

```jsx
// GetUsers.js 컴포넌트
import React, { useEffect, useState } from "react";
import { useQuery } from "@apollo/client";
import { LOAD_USERS } from "../GraphQL/Queries";

function GetUsers() {
  const { error, loading, data } = useQuery(LOAD_USERS); // getAllusers query 사용
  const [users, setUsers] = useState([]);

  useEffect(() => {
    if (data) {
      setUsers(data.getAllUsers); // data를 받으면 getAllUsers 안에 모든 데이터들이 들어있다.
    }
  }, [data]);

  if (error) {
    return <div>error occured</div>;
  }

  return (
    <div>
      {loading && <div>loading...</div>}
      {users.slice(999).map((val) => {
        return <h1 key={val.firstName}>{val.firstName}</h1>;
      })}
    </div>
  );
}

export default GetUsers;
```

apollo client의 useQuery hook을 이용하여 요청하는데,  
이 때 어떤 query를 사용할 지를 알아야 한다.  
그래서 미리 query를 지정해두어야 한다.

```jsx
import { gql } from "@apollo/client";

// gql을 통해서 query 만듦
export const LOAD_USERS = gql`
  query {
    getAllUsers {
      id
      firstName
      email
      password
    }
  }
`;
```

gql과 백틱(\`\`)안에 query를 작성하고 getAllUsers에서  
가져오고 싶은 속성만 명시한다.

이를 export하여 useQuery를 사용할 때 인자로 전달하면 해당 query가 동작하여 데이터를 반환해준다.

따라서 컴포넌트에서 `const { data } = useQuery(LOAD_USERS)`로 데이터를 받아 사용하면 된다.

data안에 getAllUser 속성 안에 배열이 들어가있는 형태이다.

#### 컴포넌트에서 사용 (mutation)

useMutation hook을 사용해서 인자로 정의해놓은 mutation을 전달한다.

정의할때 사용한 명칭(createUser)을 이용하여 객체를 전달한다.

```jsx
import React, { useState } from "react";
import { CREATE_USER_MUTATION } from "../GraphQL/Mutations";
import { useMutation } from "@apollo/client";

function Form() {
  const [firstName, setFirstName] = useState("");
  const [lastName, setLastName] = useState("");
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const [createUser, { error }] = useMutation(CREATE_USER_MUTATION);

  const addUser = () => {
    // mutation function
    // 안에 variables
    // 안에 객체로 mutate하고 싶은 속성에 값을 담는다.
    createUser({
      variables: {
        firstName: firstName,
        lastName: lastName,
        email: email,
        password: password,
      },
    });

    if (error) {
      console.log(error);
    }
  };

  return (
    <div>
      <input
        type="text"
        placeholder="First Name"
        onChange={(e) => {
          setFirstName(e.target.value);
        }}
      />
      <input
        type="text"
        placeholder="Last Name"
        onChange={(e) => {
          setLastName(e.target.value);
        }}
      />
      <input
        type="text"
        placeholder="Email"
        onChange={(e) => {
          setEmail(e.target.value);
        }}
      />
      <input
        type="text"
        placeholder="Password"
        onChange={(e) => {
          setPassword(e.target.value);
        }}
      />
      <button onClick={addUser}> Create User</button>
    </div>
  );
}

export default Form;
```

```jsx
import { gql } from "@apollo/client";

// mutation
export const CREATE_USER_MUTATION = gql`
  mutation createUser(
    $firstName: String!
    $lastName: String!
    $email: String!
    $password: String
  ) {
    createUser(
      firstName: $firstName
      lastName: $lastName
      email: $email
      password: $password
    ) {
      id
    }
  }
`;
```

### 결과

#### query

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-04/09_03.png)

#### mutation

![이미지]({{ site.url }}{{ site.baseurl }}/assets/images/2022-04/09_04.gif)

## 회고 (TIL)

**2022.04.09 Daily 회고**

✏오늘 한 일

- 코드 분석
- graphQL 연습

⁉느낀 점

graphQL 간단한 것을 해보았는데, 생각보다 어렵지는 않다!  
이것을 응용하고 best practice를 짜는 것은 다른 얘기지만...  
충분히 graphQL이 왜 나오게 되었는지를 알게 될 수 있어서 좋았다.

코드 분석해보면서 더 파악해봐야겠다.

🎃현재 나의 상태

뒤쳐지지 않으려면 남들보다 5배 이상은 열심히 해야한다.  
6개월만 고생하면서 적응해보자.  
할 수 있다. 제발!  
개발에 학 떼고 도망치고 싶지 않다.

<hr>
