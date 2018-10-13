# 檢測goroutine資源競爭

當有兩個goroutine對同一個資源做存取時就有可能發生

    func main() {
        var a = 0
        go func() {
            a++
        }()
        a++
        fmt.Println(a)
    }

go run -race main.go，可以檢測出是否有data races情況

    1
    ==================
    WARNING: DATA RACE
    Read at 0x00c00009c008 by goroutine 6:
    main.main.func1()
        /private/var/www/golang/src/test/main.go:8 +0x38

    Previous write at 0x00c00009c008 by main goroutine:
    main.main()
        /private/var/www/golang/src/test/main.go:10 +0x9e

    Goroutine 6 (running) created at:
    main.main()
        /private/var/www/golang/src/test/main.go:7 +0x7a
    ==================
    Found 1 data race(s)
    exit status 66

`Read at 0x00c00009c008 by goroutine 6:`，指出goroutine 6對0x00c00009c008變數做讀取，但`Previous write at 0x00c00009c008 by main goroutine:`又對0x00c00009c008做寫入的動作，所以發生data races