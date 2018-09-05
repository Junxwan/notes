# Gin Web Framework

See [Documentation](https://github.com/gin-gonic/gin)

- [API](#API)
    - [GET, POST, PUT, PATCH, DELETE and OPTIONS](#get-post-put-patch-delete-and-options)
    - [Route 匹配規則](#route-匹配規則)
    - [PathInfo 參數](#pathinfo-參數)
    - [Query String](#query)
    - [Form body](#form-body)
    - [Form file](#form-file)
    - [Router Static](#router-static)
    - [Run Http Server](#run-http-server)
    - [Response body](#response-body)
    
- [範例](#範例)
    - [基礎](#基礎)
    - [路徑當參數](#路徑當參數)
    - [Query String](#query-string)
    - [Form](#form)
    - [靜態目錄route](#靜態目錄route)
    - [靜態檔案route](#靜態檔案route)
    - [上傳單一檔案](#上傳單一檔案)
    - [上傳多個檔案](#上傳多個檔案)
    - [Route Group](#route-group)
    - [Route Middleware](#route-middleware)

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
    router.Any("/pathinfo", func())

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

    /user/                          不匹配
    /user/test                      匹配
    /user/test/index.go             匹配

### PathInfo 參數

`c.Param("name")`可以取得名為路徑上定義的name

[範例](#路徑當參數)

### Query

`c.DefaultQuery`取Query String但可以設定默認值

* 第一個參數為Query String
* 第二個參數為默認值

`c.Query`取Query String，默認值為空值

* 第一個參數為Query String

[範例](#query-string)

### Form body

`c.DefaultPostForm`取Form參數名但可以設定默認值

* 第一個參數為Form參數名
* 第二個參數為默認值

`c.PostForm`取Form參數名，默認值為空值

[範例](#form)

### Form file

`c.FormFile("image")`可以從Form拿到名為image的file

`router.MaxMultipartMemory`限制上傳檔案的容量(默認 32MiB)

`c.SaveUploadedFile` 上傳檔案

* 第一個參數為上傳的檔案資料
* 第二個參數為上傳檔案名路徑 ex: /var/www/name.png

`c.MultipartForm`取表單內全部的資料

`form.File["upload[]"]`從`c.MultipartForm`選出File類型且名稱為`upload[]`

[上傳單一檔案範例](#上傳單一檔案)

[上傳多個檔案](#上傳多個檔案)

### Router Static

`router.Static`可以依照route訪問目錄

* 第一個參數為route
* 第二個參數為目錄路徑

`router.StaticFile`可以依照route訪問檔案

* 第一個參數為route
* 第二個參數為檔案路徑

[靜態目錄route範例](#靜態目錄route)

[靜態檔案route範例](#範例)

兩者差異

* `router.Static`預設是抓目錄，因為底層使用`http.FileServer`
* `router.StaticFile`預設是抓檔案，因為底層使用`c.File`

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

### Response body

`c.JSON`輸出json格式且Content-Type: `application/json`

* 第一個參數為Http code 
* 第二個參數為`interface{}`

範例:

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

範例:

    func main() {
        router := gin.Default()

        router.GET("/", func(c *gin.Context) {
            c.String(http.StatusOK, "Hello World")
        })

        router.Run()
    }

## 範例

### 基礎

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

### 路徑當參數

設定一個`/user/:name`路徑並回傳`Hello` + `name`參數值

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

設定一個`/user/:name/*action`路徑並回傳`name`與`action`參數值

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

設定一個`/test`路徑並回傳`firstname`與`lastname`參數值

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

設定一個`/form_post`路徑並回傳`message`與`name`參數值

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

### 靜態目錄route

設定一個`/`路徑指向`public`目錄，默認會找該目錄的index.html

    func main() {
        router := gin.Default()
        
        router.Static("/", "./public")

        router.Run()
    }

### 靜態檔案route

設定一個`/`路徑指向`public/info.html`檔案

    func main() {
        router := gin.Default()
        
        router.StaticFile("/", "./public/info.html")

        router.Run()
    }

### 上傳單一檔案

設定一個`/upload`路徑，向該路徑打`POST`並附帶file就會將該file上傳到專案目錄下

    func main() {
        router := gin.Default()
        router.POST("/upload", func(c *gin.Context) {
            file, err := c.FormFile("file")

            if err != nil {
                c.String(http.StatusBadRequest, fmt.Sprintf("get form err: %s", err.Error()))
                return
            }

            if err := c.SaveUploadedFile(file, file.Filename); err != nil {
                c.String(http.StatusBadRequest, fmt.Sprintf("upload file err: %s", err.Error()))
                return
            }

            c.String(http.StatusOK, fmt.Sprintf("File %s uploaded successfully", file.Filename))
        })

        router.Run()
    }

    // $ curl -X POST http://localhost:8080/upload -F "file=@/work/a.png" -H "Content-Type: multipart/form-data" 
    // File index.php uploaded successfully

### 上傳多個檔案

設定一個`/upload`路徑，向該路徑打`POST`並附帶多個file就會將file上傳到專案目錄下

    func main() {
        router := gin.Default()

        router.POST("/upload", func(c *gin.Context) {
            
            form, _ := c.MultipartForm()

            files := form.File["upload[]"]

            for _, file := range files {
                log.Println(file.Filename)

                c.SaveUploadedFile(file, file.Filename)
            }
            c.String(http.StatusOK, fmt.Sprintf("%d files uploaded!", len(files)))
        })

        router.Run()
    }

    // $ curl -X POST http://localhost:8080/upload -F "upload[]=@/work/a.png" -F "upload[]=@/work/b.png" -H "Content-Type: multipart/form-data"
    // 2 files uploaded!

### Route Group

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

### Route Middleware

每個Route可以設定執行該Route前所要做的動作

`router := gin.Default()` 該route內建了`Logger()`  `Recovery()`兩個
Middleware分別是紀錄route log與遇到panic的log

`router := gin.New()`該route沒有內建任何Middleware