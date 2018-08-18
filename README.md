# Golang 筆記

**[index](#index)**
* [package name](#package-name)
* [import](#import)
* [func](#func-命名採用駝峰式)
* [變數](#變數)
* [const](#const)
* [iota](#iota)
* [if](#if)
* [for](#for)
* [type](#type)
* [string](#string)
* [array](#array)
* [slice](#slice)

## package name 

正確 應該小寫

    package main
    
不要駝峰式

    package QueryString
    
不要"_"

    package query_string
    
不要複數

    package httputils

## import 

不要使用相對路徑
  
    import ../fmt
    
用絕對路徑

    import “github.com/name/project/src/net”  

main package一定是最晚被初始化，這樣可以確認import的package一定比main還早初始化
    
    package main
    
    import (
    	"fmt"
    	"os"
    	"strconv"
    	"tempconv"
    )
    
    func main() {
    	.....
    }


## func 命名採用駝峰式
    
可公開在其他package下使用命名開頭大寫

    fmt.Print()
    
私有在其他package不可使用命名開頭小寫

    fmt.fmtString()

## 變數

全域公開變數 駝峰式

    var Input
    
全域私有變數 小駝峰式

    var input
    
全域變數不要使用簡短變量聲明

    i := ""
    
func內變數以簡短變量聲明

    func Println() {
        i := ""
    }
    
func內變數盡量簡短有用

    func Println() {
        # 剛好
        for i:=0; i < 10; i++ {
            ......
        }
        
        # 太長
        for index:=0; i < 10; i++ {
            ......
        }
    }
    
func 參數 小駝峰式

    func Println(a ...interface{}) (n int, err error) {
    	......
    }
    
專有名詞應全大寫 or 小寫

    UrlPathInfo應該寫成URLPathInfo或urlPathInfo

func 開頭大寫與小寫應該分類，大寫放上，小寫放下
    
    func Println(a ...interface{}) (n int, err error) {
    	....
    }
    
    func Sprintln(a ...interface{}) string {
    	....
    }
    
    func imag(c ComplexType) FloatType {
        ....
    }
    
    func copy(dst, src []Type) int {
        ...
    }

    
## const

編譯後不可在執行過程中更改

    const pi = 3.14159
    
可以多組
    
    const (
        e  = 2.71828182845904523536028747135266249775724709369995957496696763
        pi = 3.14159265358979323846264338327950288419716939937510582097494459
    )

宣告可以包含一個類別或值

    const noDelay time.Duration = 0
    
    fmt.Printf(noDelay)  // 0s
    
    0s是因為 time package內的func String()自動加上

省略初始值，沒有宣告則往上早有宣告的變數值

    const (
        a = 1
        b
        c = 2
        d
    )
    
    fmt.Println(a, b, c, d) // "1 1 2 2"
    
## iota

iota初始值為零並根據"="右方表達式做遞增

    type Weekday int
    
    const (
        Sunday Weekday = iota   // 0
        Monday                  // 1
        Tuesday                 // 2
    )
    
iota做計算規則

    type Flags uint
    
    const (
        FlagUp Flags = 1 << iota // 1
        FlagBroadcast            // 2
        FlagLoopback             // 4
    )
    
## if 
    
initialization以簡短變量聲明

    if i,err := os.Open(file); err != nil {
        ........  
    }

## for 
    
initialization以簡短變量聲明

    for i := 0; i < 10; i++ {
        sum += i
    }
    
不需要initialization則用_來捨棄

    for _, value := range array {
        .....
    }
    
## type
    
開頭name大寫則可讓外部使用

    type Stringer interface {
    	String() string
    }

開頭name小寫則不可讓外部使用

    type float32 float32

type 開頭大寫與小寫應該分類，大寫放上，小寫放下
    
    type Stringer interface {
    	String() string
    }
    
    type Formatter interface {
    	Format(f State, c rune)
    }
    
    type buffer []byte

    type IntegerType int
    
## string
    
盡量不要使用+串接字串

    func main() {
        s, sep := "1", "2"
      
        fmt.Println(s + sep)
    }
    
盡量使用Join(效能比"+"好)

    func main() {
        fmt.Println(strings.Join(os.Args[1:], " "))
    }
    
將String轉成bytes也可以

    var strBuf = bytes.NewBufferString("")
    strBuf.WriteString("123456")
    
臨時String用Struct

    for i := 0; i < 99999999999; i++ {
        var test struct {
            Title string
            Body  string
        }
        test.Title = "Test"
        test.Body = "123456789"
    }
      
臨時String不要用map[string]interface{}

    for i := 0; i < 99999999999; i++ {
        test := map[string]interface{}{}
        test["Title"] = "1"
        test["Body"] = 2
    }
    
## array

宣告一個長度三，類型為int，初始值都是0

    var a [3]int    
    
給定初始值

    var a [3]int = [3]int{1, 2, 3}
    
初始10個值，最後一個為-1

    var a = [...]int{10: -1}
    
自動計算長度

    var a  = [...]int{1, 2, 3, 4}

長度在編譯後就已固定，不可在執行過程中做更改長度    

    var a [3]int    
    
array應用場景在於長度固定不變，如sha256，一般建議用slice
    
    import "crypto/sha256"
    
    func main() {
        c1 := sha256.Sum256([]byte("x"))
        fmt.Printf("%x\n%x\n%t\n%T\n", c1, c2, c1 == c2, c1)
    }

## slice

一組Slice

    months := []string{1: "A", 2: "B", 3: "C", 4: "D", 5: "E"}
    
選擇某範圍的index

    fmt.Print(months[1:3]) // B,C

選擇起始index到最後
    
    fmt.Print(months[2:]) // C,D,E
    
選擇全部
    
    fmt.Println(months[:]) // A,B,C,D,E
    fmt.Println(months)    // A,B,C,D,E

只選擇到最後某個index

    fmt.Print(months[:2]) // A,B

Slice cap(容量)，如下slice取的範圍為index:1到index3，值為B,C，所以長度為2
但cap為4，cap計算的基準為起始index到該array最後一筆index，不看slice切片的最後範圍
    
    s := months[1:3] // B,C
    
    // 由於cap為4但要取20則超出cap容量上限，所以error
    fmt.Println(s[:20])     
    
    // 雖然切片出來的值只有B,C但指定取出第一筆到第四筆，此時會自動擴充
    fmt.Println(s[:4])  // "[A B C D]"
    
Slice在呼叫func傳遞參數做Slice修改會自動更改到底層數據，反之array則不會
        
    // reverse()是反轉字串
    
    // Array
    a := [...]int{0, 1, 2, 3, 4, 5}
    reverse(a)
    fmt.Println(a) // "[0 1 2 3 4 5]"
    
    // Slice
    s := []int{0, 1, 2, 3, 4, 5}
    reverse(s)
    fmt.Println(s) // "[5 4 3 2 1 0]"

Slice不能互相比較，Array可以互相比較

    a := []string{1: "A", 2: "B", 3: "C", 4: "D", 5: "E"}
    b := []string{1: "A", 2: "B", 3: "C", 4: "D", 5: "E"}
    c := [...]string{}
    d := [...]string{}
 
    fmt.Println(a == b) // 編譯錯誤
    fmt.Println(c == d) // true
    fmt.Println(a == nil) // Slice只能跟nil比較 false

> Slice比較只有bytes.Equal可用，不然就只能自己實現，要判斷是否為空最好用len(s) == 0
