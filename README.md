# Golang 筆記
- [Golang 筆記](#golang-%E7%AD%86%E8%A8%98)
    - [package name](#package-name)
    - [import](#import)
    - [func](#func)
    - [變數](#%E8%AE%8A%E6%95%B8)
    - [指標](#%E6%8C%87%E6%A8%99)
    - [const](#const)
    - [iota](#iota)
    - [if](#if)
    - [for](#for)
    - [type](#type)
    - [string](#string)
    - [array](#array)
    - [slice](#slice)
    - [map](#map)
    - [struct](#struct)
    - [json](#json)
    - [interface](#interface)
    - [錯誤](#%E9%8C%AF%E8%AA%A4)
    - [型別判斷](#%E5%9E%8B%E5%88%A5%E5%88%A4%E6%96%B7)
    - [goroutine](#goroutine)
    - [channel](#channel)
    - [select](#select)
- [Golang delve](#golang-delve)
    - [執行debug](#%E5%9F%B7%E8%A1%8Cdebug)
    - [設定中斷點](#%E8%A8%AD%E5%AE%9A%E4%B8%AD%E6%96%B7%E9%BB%9E)
    - [刪除中斷點](#%E5%88%AA%E9%99%A4%E4%B8%AD%E6%96%B7%E9%BB%9E)
    - [顯示所有中斷點](#%E9%A1%AF%E7%A4%BA%E6%89%80%E6%9C%89%E4%B8%AD%E6%96%B7%E9%BB%9E)
    - [跳到下一個中斷點](#%E8%B7%B3%E5%88%B0%E4%B8%8B%E4%B8%80%E5%80%8B%E4%B8%AD%E6%96%B7%E9%BB%9E)
    - [跳下一行](#%E8%B7%B3%E4%B8%8B%E4%B8%80%E8%A1%8C)
    - [列印變數](#%E5%88%97%E5%8D%B0%E8%AE%8A%E6%95%B8)
    - [顯示現在所在](#%E9%A1%AF%E7%A4%BA%E7%8F%BE%E5%9C%A8%E6%89%80%E5%9C%A8)
    - [顯示當前goroutine堆疊](#%E9%A1%AF%E7%A4%BA%E7%95%B6%E5%89%8Dgoroutine%E5%A0%86%E7%96%8A)
    - [顯示所有goroutine](#%E9%A1%AF%E7%A4%BA%E6%89%80%E6%9C%89goroutine)
    - [查看當前goroutine堆疊各個呼叫入口](#%E6%9F%A5%E7%9C%8B%E7%95%B6%E5%89%8Dgoroutine%E5%A0%86%E7%96%8A%E5%90%84%E5%80%8B%E5%91%BC%E5%8F%AB%E5%85%A5%E5%8F%A3)
    - [退出debug](#%E9%80%80%E5%87%BAdebug)

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

    func sum(vals ...int) int {
        total := 0
        for _, val := range vals {
            total += val
        }
        return total
    }

以"..."做將Slice內的值做參數傳遞

    values := []int{1, 2, 3, 4}
    
    fmt.Println(sum(values...)) 

closure當參數
    
    func test(a string, c func(s []string)) {....}

defer當該func執行結束後才執行，如下不管是第一個還是第二個return後都會執行resp.Body.Close()

    func call(url string) error {
        resp, err := http.Get(url)
        if err != nil {
            return err
        }
    
        defer resp.Body.Close()
    
        return nil
    }

呼叫package function

    type Point struct {
        X, Y float64
    }
    
    func Distance(p, q Point) float64 {
        return p.X * q.X
    }
        
    func main() {
        p := Point{1, 2}
        q := Point{4, 6}
        fmt.Println(Distance(p, q)) // "4"
    }
    
呼叫struct method    

    type Point struct {
        X, Y float64
    }
    
    func (p Point) Distance(q Point) float64 {
        return p.Y + q.Y
    }
    
    func main() {
        p := Point{1, 2}
        q := Point{4, 6}
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

定義某個類型

    var s string

定義預設值
    
    var s = ""
    
定義多個變數

    var a, b, c = true, "string", 1.0
    
取得func結果與是否成功

    var f, err = os.Open($name)
    
> var 通常用於需要賦予特定值(非各類型預設值)

簡短變量聲明

    i := ""
    
多個簡短變量聲明

    i, j := 1, "1"

func內變數大多以簡短變量聲明

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

## 指標

取指標

    var p string
    fmt.Print(&p)   // ""

取指標的值

    var p  = "1"
    q := &p
    fmt.Print(*q)   // 1
    
指標比較

    var p  = "1"
    q := &p
    fmt.Println(q == &p)    // true
    fmt.Println(*q == p)    // true

指標值會連動

    var p  = "1"
    q := &p
    *q = "2"
    fmt.Println(*q) // 2
    fmt.Println(p)  // 2

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
    
查看類型

    fmt.Printf("%T", os.Stdin) // *os.File
    fmt.Printf("%T", new(io.Writer)) // *io.Writer
    
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

## 錯誤

如果需要自訂一個error message

    doc, err := html.Parse(resp.Body)
    resp.Body.Close()
    if err != nil {
        // fmt.Errorf會返回error type
        return nil, fmt.Errorf("parsing %s as HTML: %v", url, err) 
    }

堆疊錯誤訊息再一起印出來

    func printStack() {
        var buf [4096]byte
        n := runtime.Stack(buf[:], false) // runtime寫入錯誤歷程
        os.Stdout.Write(buf[:n])
    }

    func f(x int) {
        defer fmt.Printf("defer %d\n", x)
        f(x - 1)
    }

補抓panic錯誤

    func main() {
        get()
    }

    func get() int {
        defer func() {
            switch p := recover(); p {  // recover()可以取得panic內容
            case nil:
                fmt.Print(p)
            default:
                fmt.Print(p)
            }
        }()

        panic(nil)
        return 0
    }
    
## 型別判斷

當型別判斷錯誤時會自動painc

    func main() {
        var w io.Writer
        w = os.Stdin            // w 會是*os.File且os.Stdin有符合io.Writer interface條件
        f := w.(*os.File)       // 轉換成功 f = os.Stdin
        c := w.(*bytes.Buffer)  // painc 因為*bytes.Buffer不等於*os.File

        fmt.Printf("%T", f)
        fmt.Printf("%T", c)
    }

當型別判斷錯誤時不跳出painc改用變數接

    func main() {
        var w io.Writer
        w = os.Stdin
        f, e1 := w.(*os.File)
        c, e2 := w.(*bytes.Buffer)

        fmt.Printf("%T\n", f)   // *os.File
        fmt.Printf("%T\n", c)   // *bytes.Buffer
        fmt.Println(e1)         // true
        fmt.Println(e2)         // false
    }

## goroutine

使用go關鍵字處理多個人連線並顯示系統時間

>> nc localhost 8000 可以連線並顯示系統時間

    func main() {
        // 監聽localhost:8000
        listener, err := net.Listen("tcp", "localhost:8000")
        if err != nil {
            log.Fatal(err)
        }

        for {
            // 一直循環檢查是否有人連線至localhost:8000
            // 如果都沒有就停留在Accept()不往下走
            // 這邊用無限迴圈主因是要一直監聽有沒有人連線過來
            conn, err := listener.Accept()
            if err != nil {
                log.Print(err)
                continue
            }
            // 執行顯示時間
            // 這邊用go是因為當出現兩個以上的連線時要另外開一個process做處理
            // 不然都是單線程會阻塞第二個連線的Client
            go handleConn(conn)
        }
    }

    func handleConn(c net.Conn) {
        defer c.Close()
        for {
            // 一直處於連線並回應Client端現在的時間
            // 如果不使用for則func執行完後會c.Close()中斷連線
            _, err := io.WriteString(c, time.Now().Format("15:04:05\n"))
            if err != nil {
                return
            }
            // 停留1秒
            time.Sleep(1 * time.Second)
        }
    }

## channel

> 請不要在同一個goroutine內做接收與發送channel，因為發送端會一直阻塞

建立無緩衝，發送端goroutine發送值直到接收端goroutine接收前，發送端goroutine都會等待接收端goroutine接收值
後發送端goroutine才會繼續執行，這可以讓接收端與發送端goroutine同步化

    naturals := make(chan int)
    squares := make(chan int)

> 三個廚師，A廚師做飯，B廚師做湯，C廚師擺盤，A廚師每次做完一個就往下丟給下一個廚師
如果中間某個廚師動作慢就會造成等待 

建立有緩衝，這channel保存三個值，如channel已滿則會停止執行發送端goroutine直到接收端goroutine接收channel
空出空間才會在繼續執行發送端goroutine，如果channel非空也非滿則接收與發送端goroutine都會繼續執行

    naturals := make(chan int, 3)
    squares := make(chan int, 3)

> 三個廚師，A廚師做飯，B廚師做湯，C廚師擺盤，A廚師每次做完三個再一次往下丟給下一個廚師
比較不會因為某廚師動作慢而造成阻塞，因為有緩衝 

取得channel最大緩衝值

    fmt.Print(cap(make(chan int, 3)))

取得channel目前緩衝數

   fmt.Print(len(make(chan int, 3)))

傳送

    naturals <- 1

接收

    x := <-squares

關閉

    close(squares)

> 不可關閉已關閉的channel

以func傳遞

    func squarer(out chan<- int, in <-chan int) {
        for v := range in {
            out <- v * v
        }
    }

## select

看誰case最快完成就執行該case邏輯

    func main() {
        abort := make(chan struct{})
        go func() {
            os.Stdin.Read(make([]byte, 1)) // read a single byte
            abort <- struct{}{}
        }()

        select {
        case <-time.After(10 * time.Second):    // 10s後沒動作
            // Do nothing.
        case <-abort:
            fmt.Println("Launch aborted!")      // 有輸入任何字元
            return
        }

        fmt.Println("Lift off!")
    }

判斷輸出與接收

    func main() {
        ch := make(chan int, 1)
        for i := 0; i < 10; i++ {
            fmt.Printf("當前i:%d\n", i)
            select {
            case x := <-ch: 
                fmt.Printf("輸出:%d\n", x)        // 輸出 0 2 4 6 8
            case ch <- i:
                fmt.Printf("輸入:%d 長度:%d\n", i, len(ch))
            }
        }
    }

# Golang delve

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

    $ clear 2

## 顯示所有中斷點

    $ bp

> bp是breakpoints的縮寫

## 跳到下一個中斷點

    $ c

> c是continue的縮寫

## 跳下一行

    $ n

> n是next的縮寫

## 列印變數

列印var變數

    $ p var

> p是print的縮寫，現在不支援執行func()

## 顯示現在所在

    $ ls

> ls是list的縮寫

## 顯示當前goroutine堆疊

    $ stack

## 顯示所有goroutine
    
    $ goroutines

    $ goroutines -t // 追加顯示各個goroutine堆疊

        Goroutine 1 - User: ./main/main.go:52 main.DBGTestRun (0x10b2458) (thread 2560573)
        0  0x00000000010b2458 in main.run
            at ./main/main.go:52
        1  0x00000000010b20c4 in main.main
            at ./main/main.go:25
        2  0x000000000102c220 in runtime.main
            at /usr/local/go/src/runtime/proc.go:198
        3  0x0000000001054881 in runtime.goexit
            at /usr/local/go/src/runtime/asm_amd64.s:2361


## 查看當前goroutine堆疊各個呼叫入口

先查看當前goroutine堆疊，可以看出現在執行到第四層(由上到下)
    
    $ stack

    0  0x00000000010b2458 in main.run
    at ./main/main.go:52
    1  0x00000000010b20c4 in main.main
    at ./main/main.go:25
    2  0x000000000102c220 in runtime.main
    at /usr/local/go/src/runtime/proc.go:198
    3  0x0000000001054881 in runtime.goexit
    at /usr/local/go/src/runtime/asm_amd64.s:2361

看第一層堆疊執行在哪裡

    $ frame 0 ls

        > main.run() ./main/main.go:25 (hits goroutine(1):1 total:1) (PC: 0x10b2458)
        47:         go run1()
        48: 
    =>  50:         go run2()
        51: 
        52:         fmt.Println("test")

看第二層堆疊執行在哪裡

    $ frame 1 ls

    > Goroutine 1 frame 1 at /private/var/www/golang/src/main/main.go:50 (PC: 0x10b20c4)
        23:         t1 := 1
        24:         t2 := 2
    =>  25:         fmt.Println(t1)
        26:         fmt.Println(t2)

## 退出debug

    $ exit