# Golang delve

See [Documentation](https://github.com/derekparker/delve/blob/master/Documentation/cli/README.md)

- [執行debug](#%E5%9F%B7%E8%A1%8Cdebug)
- [設定中斷點](#%E8%A8%AD%E5%AE%9A%E4%B8%AD%E6%96%B7%E9%BB%9E)
- [刪除中斷點](#%E5%88%AA%E9%99%A4%E4%B8%AD%E6%96%B7%E9%BB%9E)
- [刪除所有中斷點](#%E5%88%AA%E9%99%A4%E6%89%80%E6%9C%89%E4%B8%AD%E6%96%B7%E9%BB%9E)
- [顯示所有中斷點](#%E9%A1%AF%E7%A4%BA%E6%89%80%E6%9C%89%E4%B8%AD%E6%96%B7%E9%BB%9E)
- [跳到下一個中斷點](#%E8%B7%B3%E5%88%B0%E4%B8%8B%E4%B8%80%E5%80%8B%E4%B8%AD%E6%96%B7%E9%BB%9E)
- [進到func](#%E9%80%B2%E5%88%B0func)
- [跳下一行](#%E8%B7%B3%E4%B8%8B%E4%B8%80%E8%A1%8C)
- [列印變數](#%E5%88%97%E5%8D%B0%E8%AE%8A%E6%95%B8)
- [修改變數值](#%E4%BF%AE%E6%94%B9%E8%AE%8A%E6%95%B8%E5%80%BC)
- [顯示現在所在](#%E9%A1%AF%E7%A4%BA%E7%8F%BE%E5%9C%A8%E6%89%80%E5%9C%A8)
- [顯示當前goroutine堆疊](#%E9%A1%AF%E7%A4%BA%E7%95%B6%E5%89%8Dgoroutine%E5%A0%86%E7%96%8A)
- [顯示所有goroutine](#%E9%A1%AF%E7%A4%BA%E6%89%80%E6%9C%89goroutine)
- [切換當前要debug的goroutine](#%E5%88%87%E6%8F%9B%E7%95%B6%E5%89%8D%E8%A6%81debug%E7%9A%84goroutine)
- [查看當前goroutine堆疊各個呼叫入口](#%E6%9F%A5%E7%9C%8B%E7%95%B6%E5%89%8Dgoroutine%E5%A0%86%E7%96%8A%E5%90%84%E5%80%8B%E5%91%BC%E5%8F%AB%E5%85%A5%E5%8F%A3)
- [重置debug](#%E9%87%8D%E7%BD%AEdebug)
- [退出debug](#%E9%80%80%E5%87%BAdebug)

## 執行debug

    $ dlv debug main.so

進入後會呈現

    Type 'help' for list of commands.
    (dlv) {這邊打指令做操作}

## 設定中斷點

中斷main.go的30行

    $ b main.go:30

> b是break的縮寫

## 刪除中斷點

刪除第二個中斷點

    $ clear 2

## 刪除所有中斷點

    $ clearall

## 顯示所有中斷點

    $ bp

> bp是breakpoints的縮寫

## 跳到下一個中斷點

    $ c

> c是continue的縮寫

## 進到func

執行到某個func上就可以s進去

    $ s
    
        10: func main() {
    =>  11:         sss()
        12:         fmt.Println("Golang dlv test")
    
> s是step的縮寫

## 跳下一行

    $ n

> n是next的縮寫

## 列印變數

列印var變數

    $ p var

> p是print的縮寫，現在不支援執行func()

## 修改變數值

$ set var = 10

## 顯示現在所在

    $ ls

顯示到第22行

    $ ls 22

顯示到某func

    $ ls sum

    83:   func sum() string {
    84:           return test
    85:   }


> ls是list的縮寫

## 顯示當前goroutine堆疊

    $ bt
    
> bt是stack的縮寫

## 顯示所有goroutine
    
    $ goroutines

    $ goroutines -t // 追加顯示各個goroutine堆疊

        Goroutine 1 - User: ./main/main.go:52 main.DBGTestRun (0x10b2458) (thread 2560573)
        0  0x00000000010b2458 in main.run
            at ./main/main.go:52
        1  0x00000000010b20c4 in main.main
            at ./main/main.go:25
        2  0x000000000102c220 in runtime.main
            at /usr/local/go/src/runtime/proc.go:198
        3  0x0000000001054881 in runtime.goexit
            at /usr/local/go/src/runtime/asm_amd64.s:2361

## 切換當前要debug的goroutine
    
透過goroutines查看所有goroutine然後選擇要切換到第幾個goroutine
    
    $ goroutine 2

## 查看當前goroutine堆疊各個呼叫入口

先查看當前goroutine堆疊，可以看出現在執行到第四層(由上到下)
    
    $ bt

    0  0x00000000010b2458 in main.run
    at ./main/main.go:52
    1  0x00000000010b20c4 in main.main
    at ./main/main.go:25
    2  0x000000000102c220 in runtime.main
    at /usr/local/go/src/runtime/proc.go:198
    3  0x0000000001054881 in runtime.goexit
    at /usr/local/go/src/runtime/asm_amd64.s:2361

看第一層堆疊執行在哪裡

    $ frame 0 ls

    > main.run() ./main/main.go:25 (hits goroutine(1):1 total:1) (PC: 0x10b2458)
        23:         go run1()
        24: 
    =>  25:         go run2()
        26: 
        27:         fmt.Println("test")

看第二層堆疊執行在哪裡

    $ frame 1 ls

    > Goroutine 1 frame 1 at /private/var/www/golang/src/main/main.go:50 (PC: 0x10b20c4)
        48:         t1 := 1
        49:         t2 := 2
    =>  50:         fmt.Println(t1)
        51:         fmt.Println(t2)

## 重置debug

    $ r

> r是restart的縮寫

## 退出debug

    $ exit 
    
## 如何正確印出變數值

build時候加上`-gcflags "-N -l"`參數在做debug即可

    go build -gcflags "-N -l" main.go