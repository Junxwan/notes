# Gin Web Framework

- [API](#API)
    - [GET, POST, PUT, PATCH, DELETE and OPTIONS](#get-post-put-patch-delete-and-options)
    - [Route 匹配規則](#Route-匹配規則)
    - [PathInfo 參數](#PathInfo-參數)
    - [Query String](#Query-String)
    - [Form body](#Form-body)
    - [Run Http Server](#Run-Http-Server)
    - [Response body](#Response-body)
- [範例](#範例)
    - [基礎](#基礎)
    - [路徑當參數](#路徑當參數)
    - [Query String](#Query-String)
    - [Form](#Form)

## API

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

### PathInfo 參數

`c.Param("name")`可以取得名為路徑上定義的name

    router.GET("/user/:name", func(c *gin.Context) {
        name := c.Param("name")
        c.String(http.StatusOK, "Hello %s", name)
    })

    // $ curl http://localhost:8080/user/world 
    // Hello world

### Query String

`c.DefaultQuery`取Query String但可以設定默認值

* 第一個參數為Query String
* 第二個參數為默認值

`c.Query`取Query String，默認值為空值

* 第一個參數為Query String

範例

    router.GET("/test", func(c *gin.Context) {
        firstname := c.DefaultQuery("firstname", "Guest")
        lastname := c.Query("lastname")

        c.String(http.StatusOK, "firstname:%s\nlastname:%s", firstname, lastname)
    })

### Form-body

`c.DefaultPostForm`取Form參數名但可以設定默認值

* 第一個參數為Form參數名
* 第二個參數為默認值

`c.PostForm`取Form參數名，默認值為空值

    router.POST("/form_post", func(c *gin.Context) {
        message := c.PostForm("message")
        name := c.DefaultPostForm("name", "test")

        c.JSON(200, gin.H{
            "message": message,
            "name":    name,
        })
    })

### Response body

`c.JSON`輸出json格式且Content-Type: `application/json`

* 第一個參數為Http code 
* 第二個參數為`interface{}`

範例

    func main() {
    
        router := gin.Default()

        router.GET("/", func(c *gin.Context) {
            c.JSON(200, gin.H{
                "message": "pong",
            })
        })

        router.Run() 
    }

`c.String`輸出string格式且Content-Type: `text/plain`

* 第一個參數為Http code 
* 第二個參數為`interface{}`

範例

    func main() {
        router := gin.Default()

        router.GET("/", func(c *gin.Context) {
            c.String(http.StatusOK, "Hello World")
        })

        router.Run()
    }

### Run Http Server

默認監聽8080 port

    func main() {
        router := gin.Default()
        router.Run()
    }

監聽9000 port

    func main() {
        router := gin.Default()
        router.Run(":9000")
    }


## 範例

### 基礎

設定一個`/ping`路徑，監聽`127.0.0.1:8080`並回傳json，

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

### 路徑當參數

設定一個`/user/:name`路徑，監聽`127.0.0.1:8080`並回傳`Hello` + `name`參數值

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

設定一個`/user/:name/*action`路徑，監聽`127.0.0.1:8080`並回傳`name`與`action`參數值

    func main() {
        router := gin.Default()

        router.GET("/user/:name/*action", func(c *gin.Context) {
            name := c.Param("name")
            action := c.Param("action")
            c.String(http.StatusOK, "name:%s\naction:%s\n", name, action)
        })

        router.Run()
    }

    // $ curl http://localhost:8080/user/test/path/index.go
    // name:test
    // action:/path/index.go

### Query String

設定一個`/test`路徑，監聽`127.0.0.1:8080`並回傳`firstname`與`lastname`參數值

    func main() {
        router := gin.Default()

        router.GET("/test", func(c *gin.Context) {
            firstname := c.DefaultQuery("firstname", "Guest")
            lastname := c.Query("lastname")

            c.String(http.StatusOK, "firstname:%s\nlastname:%s", firstname, lastname)
        })
        
        router.Run()
    }

    // $ curl http://localhost:8080/welcome?lastname=test
    // firstname:Guest
    // lastname:test

### Form

設定一個`/form_post`路徑，監聽`127.0.0.1:8080`並回傳`message`與`name`參數值

    func main() {
        router := gin.Default()

        router.POST("/form_post", func(c *gin.Context) {
            message := c.PostForm("message")
            name := c.DefaultPostForm("name", "test")

            c.JSON(200, gin.H{
                "message": message,
                "name":    name,
            })
        })

        router.Run()
    }

    // $ curl -d "message=Hello" -H "Content-Type: application/x-www-form-urlencoded" -X POST http://localhost:8080/form_post
    // {"message":"Hello","name":"test"}