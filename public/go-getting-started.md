---
title: Go 시작하기 (macOS)
date: 2018-12-25 15:55:05
tags:
  - Go
---

## Go 설치

macOS에서 Go 설치하는 방법엔 크게 두 가지가 있는데 나는 첫 번째 방법으로 진행했다.

* **Golang 홈페이지에서 다운로드** - https://golang.org/dl/
* homebrew 통해 다운로드 - `$ brew install go`

첫 번째 방법을 선택한 이유는 특별한 것이 아니라 단지 brew 통해서 받을 생각을 미처 못 했었다... 🙈

설치 파일을 통한 다운로드는 안내에 따르면 된다.



## GOPATH 설정 

설치를 마치고 `$ go env` 입력하면  `GOPATH` 및 `GOROOT` 를 확인할 수 있다.

GOPATH는 작업 공간을 의미하므로 본인이 작업하는 Path에 맞추어 설정한다.

내 경우에는 zsh 사용하고 있어 `.zshrc` 파일에 추가했는데 사용하는 쉘에 맞추어 적용한다.

* `export GOPATH=/Users/사용자명/work/go`



작업 공간에는 `bin`, `pkg`, `src` 세 개의 폴더를 생성한다.

* `bin` : 실행 파일 (Command) 위치
* `pkg` : 라이브러리 파일 위치
* `src` : 소스 파일 위치



## Hello, world

코딩의 시작은 역시  `Hello, world`

소스 파일 위치에서  `hello/hello.go` 파일을 만들어 아래 내용을 입력한다.

```go
package main

import "fmt"

func main() {
  fmt.Println("Hello, world")
}
```

* ` $ go run hello.go` 를 통해 실행하거나

* `$ go build` 로 컴파일 후  `$ ./hello` 로 실행 파일을 실행하면

 `Hello, world` 출력을 볼 수 있다.

이제 `Go` 로 코딩할 준비가 되었다. 🙌



## 💁‍♀️ Tip

나는 대부분의 경우 `go get` 을 사용하고 있는데 그래도 궁금해서 **간단하게** 차이를 알아봤다. (더 알고 싶으면 [링크](https://golang.org/cmd/go/) 참고)

심지어 내가 사용하는 IDE (Visual Studio Code) 에서는 `import "URL"` 을 보고 설치를 도와주기 때문에 따로 설치할 일이 별로 없다. 설치 오류로 누락되는 패키지가 있는 경우에만 `go get` 사용해서 수동 설치하고 있다.

* `go build` : 패키지 컴파일

* `go install` : 패키지 컴파일 + 설치
* `go get` : 패키지 다운로드 + 설치 (즉, 다운로드 + `go install` 인 셈)
  - `GOPATH` 에 패키지가 이미 있으면 fetch 생략하므로  `go install` 과 동일하게 동작
  - import 경로의 패키지는 `go get` 을 통해 `fetch -> build -> install`  단계 자동으로 수행