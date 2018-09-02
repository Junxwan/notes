# Gin Web Framework

- [Api](#Api)
    - [GET, POST, PUT, PATCH, DELETE and OPTIONS](#get-post-put-patch-delete-and-options)
    - [Route 匹配規則](#Route-匹配規則)
    - [GET PathInfo 參數](#GET-PathInfo-參數)
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

### Route 匹配規則

`/user/:name`是一個較嚴僅的規則如果在`:name`後面又接了其他path info則不匹配

See [Documentation](https://github.com/julienschmidt/httprouter#named-parameters)

    Pattern: /user/:name

    /user/                    不匹配
    /user/test                匹配
    /user/test/index.go       不匹配

`/user/:name/*action`是一個較鬆散的規則如果在`:name`後面又接了其他path info同樣匹配

See [Documentation](https://github.com/julienschmidt/httprouter#catch-all-parameters)

    Pattern: /user/:name/*action

    /user/                          匹配
    /user/test                      匹配
    /user/test/index.go             匹配

### GET PathInfo 參數

`c.Param("name")`可以取得名為路徑上定義的name

    router.GET("/user/:name", func(c *gin.Context) {
        name := c.Param("name")
        c.String(http.StatusOK, "Hello %s", name)
    })

    // $ curl http://localhost:8080/user/world 
    // Hello world

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

設定一個`/user/:name`路徑，這代表第二個path為必填且可以自由定義當參數，監聽`127.0.0.1:8080`並回傳json，

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
