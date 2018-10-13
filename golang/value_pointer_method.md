# value method和pointer method

在Go中使用struct去呼叫method有分為value or pointer，如下

    package main

    type Interface interface {
        method()
    }

    type value struct{}

    type pointer struct{}

    func main() {
        v1 := value{}
        _ = Interface(v1)

        v2 := &value{}
        _ = Interface(v2)

        p1 := pointer{}
        _ = Interface(p1)

        p2 := &pointer{}
        _ = Interface(p2)
    }

    func (v value) method() {}

    func (p *pointer) method() {}
    
執行會出現以下錯誤，因為pointer的`func method`只接受pointer，相反的value的`func method`可以接受pointer或是value

    cannot convert p1 (type pointer) to type Interface:
	pointer does not implement Interface (method method has pointer receiver)
    
為什麽`value method`可以接收value or pointer而`pointer method`卻只能接受pointer struct

[官網解答](https://link.jianshu.com/?t=https://golang.org/doc/effective_go.html#pointers_vs_values)

首先要了解value method和pointer method差異

### value method

value method對struct做任何異動不會反應到原始值，因為value method是針對struct做copy在傳入method內

    package main

    import "fmt"

    type Interface interface {
        set(num int)
    }

    type Ball struct {
        Num int
    }

    func main() {
        b := Interface(Ball{Num: 1})
        b.set(2)

        fmt.Println(b)
    }

    func (b Ball) set(Num int) {
        b.Num = Num
    }
    

### pointer method

pointer method則是傳址所以任何異動都會反應到原始資料，以下輸出`&{2}`

    package main

    import "fmt"

    type Interface interface {
        set(num int)
    }

    type Ball struct {
        Num int
    }

    func main() {
        b := Interface(&Ball{Num: 1})
        b.set(2)

        fmt.Println(b)
    }

    func (b *Ball) set(Num int) {
        b.Num = Num
    }

因為`pointer method`的特性資料會反應到原始資料，所以如果讓value也可以呼叫到`pointer method`會讓人以為明明就是傳值為甚麼會修改到原始值，所以Go設計時為了避免這種難以察覺的錯誤，就直接設計成不可以透過value來呼叫`pointer method`且在編譯過程就會自動出錯