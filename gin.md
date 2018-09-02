# Gin Web Framework

- [Api](#Api)
    - [GET, POST, PUT, PATCH, DELETE and OPTIONS](#get-post-put-patch-delete-and-options)
- [範例](#範例)
    - [基礎](#基礎)
    - [路徑當參數](#路徑當參數)

## Api

### GET, POST, PUT, PATCH, DELETE and OPTIONS

第一個參數為該Http Method的路徑，第二個為實作邏輯的func

    router := gin.Default()

	router.GET("/pathinfo", func())
	router.POST("/pathinfo", func())
	router.PUT("/pathinfo", func())
	router.DELETE("/pathinfo", func())
	router.PATCH("/pathinfo", func())
	router.HEAD("/pathinfo", func())
	router.OPTIONS("/pathinfo", func())

## 範例

### 基礎

設定一個`/ping`路徑，監聽`127.0.0.1:8080`並回傳json，

    func main() {
        r := gin.Default()
        r.GET("/ping", func(c *gin.Context) {
            c.JSON(200, gin.H{
                "message": "pong",
            })
        })
        r.Run() 
    }

    // $ curl http://localhost:8080/ping     
    // {"message":"pong"}

### 路徑當參數

設定一個`/user/:name`路徑，這代表第二個path為必填且可以自由定義，監聽`127.0.0.1:8080`並回傳json，

    func main() {
        router := gin.Default()

        router.GET("/user/:name", func(c *gin.Context) {
            name := c.Param("name")
            c.String(http.StatusOK, "Hello %s", name)
        })

        router.Run()
    }

    // $ curl http://localhost:8080/user/world 
    // Hello world