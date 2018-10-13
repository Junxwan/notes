# deadlock

所有thread或者process都在等待資源釋放的情況，我們便把它稱之為deadlock。


由於main goroutine channel正在等待接收別人發送資料，但查詢所有goroutine(除了main)都在睡覺，所以不會有再有寫入channel的情況，所以deadlock

    func main() {
        channel := make(chan int)
        <-channel
    }

    // fatal error: all goroutines are asleep - deadlock!
    // goroutine 1 [chan receive]:

同上只是等待別人接收

    func main() {
        channel := make(chan int)
        channel <- 1
    }

    // fatal error: all goroutines are asleep - deadlock!
    // goroutine 1 [chan send]:

channel先寫入一個int，但是由於是無緩衝channel所以會一直在卡在`channel <- 1`等待另一個goroutine channel做接收，但無其他goroutine，所以deadlock

    func main() {
        channel := make(chan int)

        channel <- 1
        <-channel
    }

    // fatal error: all goroutines are asleep - deadlock!
    // goroutine 1 [chan send]:

設置一個有緩衝channel但當channel滿了無法寫入後會阻塞等待別的goroutine做接收，但無其他goroutine，所以deadlock

    func main() {
        channel := make(chan int, 2)

        fmt.Println(1)
        channel <- 1
        fmt.Println(2)
        channel <- 2

        fmt.Println(3)
        channel <- 3
    }

    // fatal error: all goroutines are asleep - deadlock!
    // 1
    // goroutine 1 [chan send]:
    // main.main()
    // 2
    // /private/var/www/golang/src/test/main.go:14 +0x152
    // 3

設置一個有緩衝channel，開另一個goroutine針對此channel做寫入但沒有寫滿，回到main goroutine則硬是要取出跟channel同容量的值，由於未寫滿造成最後一次取channel阻塞，但無其他goroutine會在做寫入，所以deadlock

    func main() {
        channel := make(chan int, 5)
        for i := 0; i < 5; i++ {
            go func(j int) {
                fmt.Println(j)

                if (j == 5) {
                    channel <- i
                }

            }(i)
        }

        for j := 0; j < 5; j++ {
            <-channel
        }

        fmt.Println("end")
    }

    // goroutine 1 [chan receive]:
    // main.main()
    // /private/var/www/golang/src/test/main.go:19 +0xbd
    // 4
    // 2
    // 1
    // 0
    
由於test1 goroutine會一直存在，所以main goroutine channel會一直認為可能有其他goroutine會向channel寫入，所以會一直等待，因為Go只要判斷有其他goroutine就不會認為這個channel deadlock

    func test1() {
        for {
            fmt.Println("test1")
        }
    }

    func test2() {
        fmt.Println("test2")
    }

    func main() {
        go test1()
        go test2()

        channel := make(chan int)
        <-channel
    }

可以利用select來判別channel讀取資料

    func main() {
        channel := make(chan int, 2)

        fmt.Println(1)
        channel <- 1
        fmt.Println(2)
        channel <- 2

        select {
        case channel <- 3:
            fmt.Println(3)
        default:
            fmt.Println("end")
        }
    }