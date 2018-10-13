# GET, POST, PUT, PATCH, DELETE and OPTIONS

第一個參數為該Http Method的路徑，第二個為實作邏輯的func

    router := gin.Default()

	router.GET("/pathinfo", func())
	router.POST("/pathinfo", func())
	router.PUT("/pathinfo", func())
	router.DELETE("/pathinfo", func())
	router.PATCH("/pathinfo", func())
	router.HEAD("/pathinfo", func())
	router.OPTIONS("/pathinfo", func())
    router.Any("/pathinfo", func())

> router `func()` 可以接很多個並且根據順序做執行

# 範例

## Base

設定一個`/ping`路徑並回傳json，

    func main() {
        router := gin.Default()

        router.GET("/ping", func(c *gin.Context) {
            c.JSON(200, gin.H{
                "message": "pong",
            })
        })

        router.Run() 
    }

    // $ curl http://localhost:8080/ping     
    // {"message":"pong"}

## Route Group

設定二個Group，url前綴詞分別為`v1` `v2`

    func main() {
        router := gin.Default()

        handler := func(c *gin.Context) {
            c.String(http.StatusOK, "url: %s", c.Request.URL)
        }

        v1 := router.Group("/v1")
        {
            v1.GET("/test", handler)
        }

        v2 := router.Group("/v2")
        {
            v2.GET("/test", handler)
        }

        router.Run()
    }

    // $ curl http://localhost:8080/v1/test  
    // url: /v1/test
    // $ curl http://localhost:8080/v2/test  
    // url: /v2/test

## Route Middleware

每個Route可以設定執行該Route前所要做的動作

### gin.Default()

`router := gin.Default()` 該route內建了`Logger()`  `Recovery()`兩個
Middleware分別是紀錄route log與遇到panic的log

### gin.New()

`router := gin.New()`該route沒有內建任何Middleware

對route新增全域Middleware

    func main() {
        router := gin.New()

        router.Use(gin.Logger(), gin.Recovery())
        
        router.Run()
    }

對特定route新增Middleware

    func main() {
        router := gin.New()

        router.GET("/log", gin.Logger())

        router.Run()
    }