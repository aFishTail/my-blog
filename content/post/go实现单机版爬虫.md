---
title: "Go 实现单机版爬虫"
date: 2021-11-10T16:47:50+08:00
categories:
    - 服务端
tags:
    - Go
    - 爬虫
---
> mk-go项目实战

### 获取网页内容
```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"io/ioutil"
	"net/http"

	"golang.org/x/net/html/charset"
	"golang.org/x/text/encoding"
	"golang.org/x/text/transform"
)

func main() {
	resp, err := http.Get("http://zhenai.com/zhenghun")
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()
	if resp.StatusCode != http.StatusOK {
		fmt.Println("error StatusCode", resp.StatusCode)
	}
	e := determineEncoding(resp.Body)
	utfbReader := transform.NewReader(resp.Body, e.NewDecoder())
	all, err := ioutil.ReadAll(utfbReader)
	if err != nil {
		panic(err)
	}
	fmt.Printf("%s\n", all)
}

func determineEncoding(r io.Reader) encoding.Encoding {
	bytes, err := bufio.NewReader(r).Peek(1024)
	if err != nil {
		panic(err)
	}
	e, _, _ := charset.DetermineEncoding(bytes, "")
	return e
}

```

### 正则表达式
```go
package main

import (
	"fmt"
	"regexp"
)

const text = `My email is ccmouse@gmail.com
email1 is abc@test.com
email2 is shdyd@ssdoo.com
email2 is shdyd@ssdoo.com.cn
`

func main() {
	re := regexp.MustCompile(`([a-zA-A0-9])+@([a-zA-A0-9.]+)\.([a-zA-A0-9]+)`)
	matches := re.FindAllStringSubmatch(text, -1)
	for _, match := range matches {
		fmt.Printf("%s\n", match)
	}
}
```
### 提取城市
```go
package main

import (
	"bufio"
	"fmt"
	"io"
	"io/ioutil"
	"net/http"
	"regexp"

	"golang.org/x/net/html/charset"
	"golang.org/x/text/encoding"
	"golang.org/x/text/transform"
)

func main() {
	resp, err := http.Get("http://zhenai.com/zhenghun")
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()
	if resp.StatusCode != http.StatusOK {
		fmt.Println("error StatusCode", resp.StatusCode)
	}
	e := determineEncoding(resp.Body)
	utfbReader := transform.NewReader(resp.Body, e.NewDecoder())
	all, err := ioutil.ReadAll(utfbReader)
	if err != nil {
		panic(err)
	}
	printCityList(all)
}

func determineEncoding(r io.Reader) encoding.Encoding {
	bytes, err := bufio.NewReader(r).Peek(1024)
	if err != nil {
		panic(err)
	}
	e, _, _ := charset.DetermineEncoding(bytes, "")
	return e
}

func printCityList(contents []byte) {
	// re := regexp.MustCompile(`<a href="http://www.zhenai.com/zhenghun/yangquan" data-v-1573aa7c>阳泉</a>`)
	re := regexp.MustCompile(`<a href="http://www.zhenai.com/zhenghun/[0-9a-z]+" [^>]*>[^<]+</a>`)
	matchs := re.FindAll(contents, -1)
	for _, v := range matchs {
		fmt.Printf("%s\n", v)
	}
	fmt.Printf("match find %d\n", len(matchs))
}
```
#### 进一步提取出城市名称和链接
```go
func printCityList(contents []byte) {
	re := regexp.MustCompile(`<a href="(http://www.zhenai.com/zhenghun/[0-9a-z]+)" [^>]*>([^<]+)</a>`)
	matchs := re.FindAllSubmatch(contents, -1)
	for _, v := range matchs {
		fmt.Printf("%s\n", v)
	}
	fmt.Printf("match find %d\n", len(matchs))
}
```