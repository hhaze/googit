---
title: GraphQL
date: 2019-04-14 22:39:42
tags:
  - GraphQL
---

## Introduction

* API를 위해 Facebook에서 만든 **쿼리 언어 (Query Language)** 
  * Facebook이 2012년에 개발하여 2015년에 공개적으로 발표
  * "write one, run anywhere"



배경

* 모바일 사용 증가로 효율적인 데이터 로드에 대한 필요성
* 다양한 프론트 엔드 프레임워크 및 플랫폼 대응
* 빠른 개발



특징

* Specification
  * API 디자인에 대한 스펙/방법론 (ex. REST)
  * 클라이언트가 서버에서 데이터를 로드하는 방법을 정의
  * 스펙이 스키마의 유효성 판별
  * 스카마가 클라이언트 호출의 유효성 판별

* Application Layer
  * 정의된 스키마 기반으로 데이터 탐색 및 반환
  * 데이터 저장 방식과 무관
* Graph: 스키마에 정의된 구조
  * 그래프는 노드(Node)와 에지(Edge)로 구성
  * GraphQL은 객체와 그 관계로 구성



REST와의 가장 큰 차이점

* flexibillity and efficiency
* 단 하나의 Endpoint
* 요청 시 사용한 쿼리에 따라 각기 다른 응답



예제 상황

* 블로그 앱에서 특정 사용자의 글 제목과 그 사용자의 최근 3명 follower 이름을 보여주려고 할 때
  * REST 경우

    ![REST](https://imgur.com/VRyV7Jh.png)

    

  * GraphQL 경우

    ![GraphQL](https://imgur.com/z9VKnHs.png)




이로 인한 장점

* HTTP 요청 횟수 감소
* HTTP 응답 사이즈 감소
* 서버 측 추가 작업 없이 클라이언트 수정 가능



대표 사용처

* Facebook
* Github
  * [Github API v4](https://developer.github.com/v4/)





## Learn

동작 방식

* 요청 받은 쿼리에 대해 정의된 타입과 필드를 검사한 다음, 함수를 실행하여 결과를 생성하여 응답

* 타입과 필드를 정의하고, 각 타입의 필드에 대한 함수로 구현



타입과 필드

```graphql
type Query {
  me: User
}

type User {
  id: ID
  name: String
}
```

쿼리

```graphql
{
  me {
    name
  }
}
```

응답 (JSON)

```json
{
  "me": {
    "name": "Luke Skywalker"
  }
}
```



#### Schema - a collection of GraphQL types

* SDL (Schema Definition Language)

  ```graphql
  type Query {
    allPersons(last: Int): [Person!]!
  }
  
  type Mutation {
    createPerson(name: String!, age: Int!): Person!
  }
  
  type Subscription {
    newPerson: Person!
  }
  
  type Person {
    name: String!
    age: Int!
    posts: [Post!]!
  }
  
  type Post {
    title: String!
    author: Person!
  }
  ```



#### Type

* Query - fetch

  ```graphql
  type Query {
    allPersons(last: Int): [Person!]!
  }
  ```

  

* Mutation - create / update / delete

  ```graphql
  type Mutation {
    createPerson(name: String!, age: Int!): Person!
  }
  ```

  

* Subscription - realtime connection

  ```graphql
  type Subscription {
    newPerson: Person!
  }
  ```

  

* 스칼라

  * 하위 필드가 없는 쿼리의 끝 부분
  * Int: 부호가 있는 32비트 정수
  * Float: 부호가 있는 부동소수점 값
  * String: UTF=8 문자열
  * Boolean: true 또는 false
  * ID: 고유 식별자를 나타내며 사람이 읽을 수 있도록 하는 의도가 아니라는 것을 의미

  

* 열거형 (Enums)

  ```graphql
  enum Weekday {
    MONDAY
    TUESDAY
    WEDNESDAY
    THURSDAY
    FRIDAY
    SATURDAY
    SUNDAY
  }
  ```

  

* 리스트와 Non-Null

  ```json
  myField: [String!]
            
  myField: null // valid
  myField: [] // valid
  myField: ['a', 'b'] // valid
  myField: ['a', null, 'b'] // error
  ```

  ```json
  myField: [String]!
  
  myField: null // error
  myField: [] // valid
  myField: ['a', 'b'] // valid
  myField: ['a', null, 'b'] // valid
  ```

  

* 인터페이스

  ```graphql
  interface Node {
    id: ID!
  }
  ```

  * `Node` 를 *구현한(implements)* 모든 타입은 이러한 인자와 리턴 타입을 가져야 한다는 것을 의미

    ```graphql
    type User implements Node {
      id: ID!
      name: String!
      age: Int!
    }
    ```

  

* 유니온

  ```graphql
  union Person = Adult | Child
  ```

  

* 입력

  ```graphql
  input ReviewInput {
    stars: Int!
    commentary: String
  }
  ```

  ```graphql
  mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
    createReview(episode: $ep, review: $review) {
      stars
      commentary
    }
  }
  ```

  ```json
  {
    "ep": "JEDI",
    "review": {
      "stars": 5,
      "commentary": "This is a great movie!"
    }
  }
  ```

  ```json
  {
    "data": {
      "createReview": {
        "stars": 5,
        "commentary": "This is a great movie!"
      }
    }
  }
  ```

  

#### Query & Mutation 

쿼리 필드는 병렬로 실행되지만 뮤테이션 필드는 하나씩 차례대로 실행




* 기본

  ```graphql
  {
    allPersons {
      name
      age
      posts {
        title
      }
    }
  }
  ```

  

* 인자

  ```graphql
  {
    allPersons(last: 2) {
      name
    }
  }
  ```

  

* 별칭 (alias) - 필드의 결과를 원하는 이름으로

  ```graphql
  {
    first: User(id: "1") {
      name
    }
    second: User(id: "2") {
      name
    }
  }
  ```

  ```json
  {
    "first": {
      "name": "Alice"
    },
    "second": {
      "name": "Sarah"
    }
  }
  ```

  - 별칭이 없으면 두 개 결과 모두 "User"라는 필드명으로 반환되므로

  

* 프래그먼트 - a collection of fields on a specific type

  ```graphql
  {
    allUsers {
      name
      age
      email
      street
      zipcode
      city
    }
  }
  ```

  ```graphql
  fragment addressDetails on User {
    name
    street
    zipcode
    city
  }
  ```

  ```graphql
  {
    allUsers {
      age
      email
      ... addressDetails
    }
  }
  ```

  - 유니온과 활용하는 예제

    ```graphql
    type Adult {
      name: String!
      work: String!
    }
      
    type Child {
      name: String!
      school: String!
    }
    ```

    ```graphql
     union Person = Adult | Child
    ```

    ```graphql
    {
      allPersons {
        name # works for `Adult` and `Child`
        ... on Child {
          school
        }
        ... on Adult {
           work
        }
      }
    }
    ```





* 작업 이름

  ```graphql
  query HeroNameAndFriends {
    hero {
      name
      friends {
        name
      }
    }
  }
  ```

  

* 변수

  ```graphql
  type Query {
    allUsers(olderThan: Int = -1): [User!]!
  }
  ```

  ```graphql
  {
    allUsers(olderThan: 30) {
      name
      age
    }
  }
  ```

  

* 지시어

  ```json
  query Hero($episode: Episode, $withFriends: Boolean!) {
    hero(episode: $episode) {
      name
      friends @include(if: $withFriends) {
        name
      }
    }
  }
  ```

  * @include: 인자가 true인 경우에만 이 필드를 결과에 포함
  * @skip: 인자가 true인 경우 이 필드를 무시

  ```json
  {
    "episode": "JEDI",
    "withFriends": false
  }
  ```

  ```json
  {
    "data": {
      "hero": {
        "name": "R2-D2"
      }
    }
  }
  ```

  

* 뮤테이션 (Mutation)

  ```graphql
  mutation {
    createPerson(name: "Bob", age: 36) {
      name
      age
    }
  }
  ```

  ```json
  {
    "data": {
      "createPerson": {
        "name": "Bob",
        "age": 36
      }
    }
  }
  ```

  


#### Resolver

- 쿼리 내 각 필드는 정확히 하나의 함수에 대응
- [Go 언어 예제](<https://github.com/graphql-go/graphql/blob/master/examples/crud/main.go>)





## Code & Playground

* 예제:

  <https://api.graph.cool/simple/v1/cju2eos093a0w01869i1pnnyj/>






## 참고 링크

* <https://graphql-kr.github.io/>
* <https://www.howtographql.com/>
* <https://github.com/graphql-go/graphql>
* <https://graphql.github.io/swapi-graphql/>