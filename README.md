# Golang 筆記

**[index](#index)**
* [package name](#package-name)
* [import](#import)
* [func](#func)
* [變數](#變數)
* [const](#const)
* [iota](#iota)
* [if](#if)
* [for](#for)
* [type](#type)
* [string](#string)
* [array](#array)
* [slice](#slice)
* [map](#map)
* [struct](#struct)
* [json](#json)
* [interface](#interface)

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


## func 

> 命名採用駝峰式，參數是傳值，會複製一個同樣內容的變數當區域變數(底層內存地址不一樣)
    
可公開在其他package下使用命名開頭大寫

    fmt.Print()
    
私有在其他package不可使用命名開頭小寫

    fmt.fmtString()
    
參數類型定義，兩者一樣意思

    func f(i, j, k int, s, t string)                
    func f(i int, j int, k int,  s string, t string) 
    
多回傳值
    
    func findLinksLog(url string) ([]string, error) {
    	return findLinks(url)
    }
    
    func findLinks(url string) ([]string, error) {
        return url, nil
    }

bare return，省略return直接定義return 變數

    func CountWordsAndImages(url string) (words, images int, err error) {
    	resp, err := http.Get(url)
    	words, images = countWordsAndImages(resp)
    	return
    }
    
    // 與上面同等
    func CountWordsAndImages(url string) (int, int, error) {
    	resp, err := http.Get(url)
    	words, images = countWordsAndImages(resp)
    	return words, images, err
    }

接收任意數量參數

    func sum(vals...int) int {.....}

以"..."做將Slice內的值做參數傳遞

    values := []int{1, 2, 3, 4}
    
    fmt.Println(sum(values...)) 

defer當該func執行結束後才執行，如下不管是第一個還是第二個return後都會執行resp.Body.Close()

    func call(url string) error {
        resp, err := http.Get(url)
        if err != nil {
            return err
        }
    
        defer resp.Body.Close()
    
        return nil
    }

呼叫package function與struct method

    type Point struct {
    	X, Y float64
    }
    
    func Distance(p, q Point) float64 {
    	return p.X * q.X
    }
    
    func (p Point) Distance(q Point) float64 {
    	return p.Y + q.Y
    }
    
    func main() {
    	p := Point{1, 2}
    	q := Point{4, 6}
    	fmt.Println(Distance(p, q)) // "4"
    	fmt.Println(p.Distance(q))	// "8"
    }
    
>> p.Distance(q)做意思是q由外部注入，但p參數則由呼叫來源端(這邊指p)帶入

透過回傳func當變數並進行呼叫
   
    type Point struct {
    	X, Y float64
    }
    
    type ColoredPoint struct {
    	Point
    	Color color.RGBA
    }
    
    func (p Point) Distance(q Point) float64 {
    	return p.X + q.Y
    }
    
    func main() {
    	red := color.RGBA{255, 0, 0, 255}
    	var p = ColoredPoint{Point{1, 1}, red}
    	distance := p.Distance                  // 原本的func暫存在一個變數
    	fmt.Print(distance(Point{5, 4}))        // "5"
    }

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

Slice判斷空值

    len(s) == 0    

> Slice比較只有bytes.Equal可用，不然就只能自己實現，可跟nil判斷

## map
    
> 每次遍歷順序都是隨機
    
建立map，key為string type，value為int type

    ages := map[string]int{
        "alice":   31,
        "charlie": 34,
    }
    
    or
    
    ages := make(map[string]int)
    ages["alice"] = 31
    ages["charlie"] = 34
    
delete key，失敗回傳0

    delete(ages, "alice") 

判斷空值

    var ages map[string]int
    fmt.Println(ages == nil)    // "true"
    fmt.Println(len(ages) == 0) // "true"
    
判斷key是否存在
    
    ages := make(map[string]int)
    
    if _, ok := ages["bob"]; ok {
        fmt.Print(true)
    } else {
        fmt.Print(false)
    }
    
value也可以是字符串集合等

     a := make(map[string]map[string]bool)
    
> map不能互相比較只能自己實現，可跟nil判斷

## struct

建立一個文章結構並有相關屬性

    type Article struct {
        ID        int
        Name      string
        Title     string
        Context   string  
    }

相臨同樣類型可以並行

    type Article struct {
    	ID                   int
    	Name, Title, Context string
    }

放入相同結構

    type tree struct {
        value       int
        left, right *tree
    }

宣告一個變數為Article結構

    var news Article

操作  

    news.Title = "test"
    
設定結構屬性值，需按照順序與對應類型設定(不要使用可以對外開放的結構)

    type Point struct {
    	X, Y int
    }
    
    p := Point{1, 2}
    
設定結構屬性值以Name做設定，不需照順序設定，沒設定則採用默認(0)

    type Point struct {
    	X, Y int
    }
        
    p := Point{X: 1, Y: 2}

不可以混著使用

    type Point struct {
        X, Y int
    }
    
    a := Point{1, 2}
    b := Point{X: 1, Y: 2}
    
傳遞參數(用指標可以節省內存，因為操作同樣底層不用重新分配內存)
    
    func Scale(p *Point, factor int) *Point {
    	p.X *= factor
    	p.Y *= factor
    	return p
    }

屬性與struct比較

    type Point struct {
    	X, Y int
    }
    
    func main() {
    	p := Point{1, 2}
    	q := Point{1, 1}
    	fmt.Println(p.X == q.X) // "true"
    	fmt.Println(p.Y == q.Y) // "false"
    	fmt.Println(p == q)     // "false"
    }
    
屬性指定別的struct

    type Point struct {
        X, Y int
    }
    
    type Wheel struct {
        Point Point
        Spokes int
    }
    
    func main() {
        var w Wheel
    
        w.Point.X = 8
        w.Point.Y = 8
        w.Spokes = 20
    }
    
屬性不需指定完整路徑，屬性在結構上省略類型

    type Point struct {
        X, Y int
    }
    
    type Circle struct {
        Point           // 省略
        Radius int
    }
    
    type Wheel struct {
        Circle          // 省略
        Spokes int
    }
    
    func main() {
        var w Wheel
    
        w.X = 8
        w.Y = 8
        w.Radius = 5
        w.Spokes = 20
    }
    
省略屬性類型，可隱式對該類型方法做操作
    
    type Point struct{
    	X, Y float64
    }
    
    type ColoredPoint struct {
    	Point               // 省略
    	Color color.RGBA
    }
    
    func (p *Point) ScaleBy(factor float64) {
    	p.X *= factor
    	p.Y *= factor
    }
    
    func main() {
    	red := color.RGBA{255, 0, 0, 255}
    	p := ColoredPoint{Point{1, 1}, red}
    	p.ScaleBy(2)            //隱式的將ColoredPoint呼叫到Point類型的ScaleBy method
    	fmt.Println(p.Point)
    }
    
指針

    type ColoredPoint struct {
    	*Point
    	Color color.RGBA
    }
    
## json 

一個struct定義某些屬性在轉為成json後key要做更換

    type Movie struct {
    	Title  string
    	Year   int  `json:"released"`
    	Color  bool `json:"color"`
    	Actors []string
    }
    
    var movies = []Movie{
    	{Title: "Casablanca", Year: 1942, Color: false,
    		Actors: []string{"Humphrey Bogart", "Ingrid Bergman"}}
    }
    
    fmt.Printf("%s\n", json.Marshal(movies))
    
    {
        "Title": "Casablanca",
        "released": 1942,       // Year切換成released
        "color": false,         // Color切換成color
        "Actors": [
            "Humphrey Bogart",
            "Ingrid Bergman"
        ]
    }
    
json解碼，轉成struct並只轉換Title屬性

    var titles []struct {
        Title string
    }
    
    if err := json.Unmarshal(json.Marshal(movies), &titles); err == nil {
        fmt.Print(titles)
    }
    
json解碼根據tag name取對應json key做value
    
    type IssuesSearchResult struct {
    	TotalCount int `json:"total_count"`
    	Items      []*Issue
    }
    
    {
        "total_count": 20 // TotalCount對應到total_count
    }

## interface

一個method參數對應到一個interface，如下Fprintf()需要滿足io.Writer這個interface
而該interface需要一個Write(p []byte) (n int, err error)，當ByteCounter內
可以隱式的呼叫Write時則符合io.Writer即可以作為如下Fprintf()參數

    type ByteCounter int
    
    func (c *ByteCounter) Write(p []byte) (int, error) {
    	*c += ByteCounter(len(p)) // convert int to ByteCounter
    	return len(p), nil
    }
    
    func main() {
    	var c ByteCounter
    	var name = "Dolly"
    	fmt.Fprintf(&c, "hello, %s", name)
    }
    
    package fmt
    
    func Fprintf(w io.Writer, format string, a ...interface{}) (n int, err error) {
    	p := newPrinter()
    	p.doPrintf(format, a)
    	n, err = w.Write(p.buf)
    	p.free()
    	return
    }
    
    package io
    
    type Writer interface {
    	Write(p []byte) (n int, err error)
    }
    
一個interface可以內遷多個interface
    
    type ReadWriter interface {
    	Reader
    	Writer
    }
    
    type Reader interface {
        Read(p []byte) (n int, err error)
    }
    
    type Writer interface {
        Write(p []byte) (n int, err error)
    }