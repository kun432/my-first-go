# my-first-go

## 概要

GCPのセミナーに行って、Goの良さを聞いてきたので試してみる。MacでWindows用実行ファイルが生成できるとかクロスコンパイルができるのが良いなーと思ったところ。ちょっとしたツール的なものを作れればよいかなと。

とりあえず https://cybozu.atlassian.net/wiki/spaces/pubjp/pages/6422530/Go に従って、以下の順で読んで・写経してみる。

- [How to Write Go Code](https://golang.org/doc/code.html)
- [A Tour of Go](https://tour.golang.org/welcome/1)
- [Effective Go](https://golang.org/doc/effective_go.html)

## 基本

適当にインストール。今回はbrewでインストールした。

```bash
$ go version
go version go1.9.3 darwin/amd64
$ go env
GOARCH="amd64"
GOOS="darwin"
GOPATH="/Users/kun432/go"
・・・
```

## How to Write Go Code

### コードの管理

GoではすべてのファイルをGOPATHの配下で管理する。
以下例

```
/Users/kun432/go/
  bin/
   hello
   outyet
  pkg/
    linux_amd64
      github.com/kun432/my-first-go/
        stringutil.a
  src/
      github.com/kun432/my-first-go/
        .git/
        hello/
          hello.go
        outyet/
          main.go
          main_test.go
        stringutil/
          reverse.go
          reverse_test.go
      golang.org/x/image/
      
```

- src配下に各レポジトリをおいてその中でソース管理する
- binの下に実行ファイルがビルドされて配置される
- pkgの下にパッケージオブジェクトが配置される

環境設定

```
$ export PATH=$PATH:$(go env GOPATH)/bin
$ export GOPATH=$(go env GOPATH)
```

ベースディレクトリを作成する

```bash
$ mkdir -p $GOPATH/src/github.com/kun432
```

## Hello World

事前にgithubにリポジトリを作っておくこと

```
$ mkdir $GOPATH/src/github.com/kun432/hello
$ cd $GOPATH/src/github.com/kun432/hello
$ git init
$ gibo macOS Go >> .gitignore
$ git add .
$ git commit -a -m "first commit"
$ git remote add origin https://github.com/kun432/hello.git
$ git push -u origin master
```

Hello World

```
$ vi hello.go
```

```go:hello.go
package main

import "fmt"

func main() {
  fmt.Printf("Hello, World.\n")
}
```

ビルドとインストール

```
$ go install github.com/kun432/hello
```

もしくは

```
$ cd $GOPATH/github.com/kun432/hello
$ go install
```

ビルドされるとベースディレクトリ配下のbinに置かれる

```
$ ls $GOPATH/bin
hello
```

実行

```
$ hello
Hello, World.
```

コミット

```
$ git add .
$ git commit -a -m "add hello.go"
$ git push
```

## ライブラリを作ってみる

事前にgithubにリポジトリを作っておくこと

```
$ mkdir $GOPATH/src/github.com/kun432/stringutil
$ cd $GOPATH/src/github.com/kun432/stringutil
$ git init
$ gibo macOS Go >> .gitignore
$ git add .
$ git commit -a -m "first commit"
$ git remote add origin https://github.com/kun432/stringutil.git
$ git push -u origin master
```

早速作る

```
$ vi stringutil.go
```

```go:hello.go
package main

import "fmt"

func main() {
  fmt.Printf("Hello, World.\n")
}
```

ビルド。ただし何も出力されない。```go install```すればpkgの下にできるけど、ここではビルドが通ることだけを確認する。

```
$ go build github.com/kun432/stringutil
```

再度hello.goを修正

```go:hello.go
package main

import (
  "fmt"
  "github.com/kun432/stringutil"
)

func main() {
  fmt.Println(stringutil.Reverse("Hello, World!"))
}
```

ビルドとインストール

```
$ go install github.com/kun432/hello
```

helloを実行してみる

```
$ hello
!dlroW ,olleH
```

ディレクトリを見てみるとこうなっている

```
/Users/kun432/go/
  bin/
   hello
  pkg/
    linux_amd64
      github.com/kun432/stringutil/
        stringutil.a
  src/
    github.com/kun432/hello/
      hello.go
    github.com/kun432/stringutil/
      reverse.go
```

ライブラリstringutilがオブジェクトファイルとして生成され、それを静的にインクルードした実行ファイルhelloができる。