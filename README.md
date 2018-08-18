# Golang 筆記

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

全大寫以"_"做區隔

    APP_URL

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
    
## String
    
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