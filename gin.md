# Gin Web Framework

See [Documentation](https://github.com/gin-gonic/gin)

- [API](#API)
    - [GET, POST, PUT, PATCH, DELETE and OPTIONS](#get-post-put-patch-delete-and-options)
    - [Route Matching Rule](#route-matching-rule)
        - [/user/:name](#/username)
        - [/user/:name/*action](#usernameaction)
    - [PathInfo Parameter](#pathinfo-參數)
        - [c.Param()](#cparam)
    - [Query String](#query)
        - [c.DefaultQuery](#cdefaultquery)
        - [c.Query](#cquery)
    - [Form body](#form-body)
        - [c.DefaultPostForm](#cdefaultpostform)
        - [c.PostForm](#cpostForm)
    - [Form file](#form-file)
        - [c.FormFile](#cformFile)
        - [router.MaxMultipartMemory](#routermaxmultipartmemory)
        - [c.SaveUploadedFile](#csaveuploadedfile)
        - [c.MultipartForm](#cmultipartform)
    - [Router Static](#router-static)
        - [router.Static](#routerstatic)
        - [router.StaticFile](#routerstaticfile)
    - [Run Http Server](#run-http-server)
    - [Response body](#response-body)
        - [c.JSON](#cjson)
        - [c.String](#cstring)
    - [Validator](#validator)
        - [required](#required)
        - [c.Bind](#cbind)
        - [c.BindJSON](#cbindjson)
        - [c.BindQuery](#cbindquery)
        - [c.ShouldBind](#cshouldbind)
        - [c.ShouldBindJSON](#cshouldbindjson)
        - [c.ShouldBindQuery](#cshouldbindquery)

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
        - [gin.Default()](#gindefault)
        - [gin.New()](#ginnew)
    - [Logger](#logger)
    - [驗證](#驗證)
        - [From](#form-validator)
        - [Json](#json-validator)
        - [Query](#query-validator)

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

> router `func()` 可以接很多個並且根據順序做執行

### Route Matching Rule

#### /user/:name

嚴僅的規則如果在`:name`後面又接了其他path info則不匹配

See [Documentation](https://github.com/julienschmidt/httprouter#named-parameters)

    Pattern: /user/:name

    /user/                    不匹配
    /user/test                匹配
    /user/test/index.go       不匹配

#### /user/:name/*action

鬆散的規則如果在`:name`後面又接了其他path info同樣匹配

See [Documentation](https://github.com/julienschmidt/httprouter#catch-all-parameters)

    Pattern: /user/:name/*action

    /user/                          不匹配
    /user/test                      匹配
    /user/test/index.go             匹配

### PathInfo Parameter

#### c.Param()

`c.Param("name")`可以取得名為路徑上定義的name

[範例](#路徑當參數)

### Query

#### c.DefaultQuery

取Query String但可以設定默認值

* 第一個參數為Query String
* 第二個參數為默認值

#### c.Query

取Query String，默認值為空值

* 第一個參數為Query String

[範例](#query-string)

### Form body

#### c.DefaultPostForm

取Form參數名但可以設定默認值

* 第一個參數為Form參數名
* 第二個參數為默認值

#### c.PostForm

取Form參數名，默認值為空值

[範例](#form)

### Form file

#### c.FormFile

`c.FormFile("image")`可以從Form拿到名為image的file

#### router.MaxMultipartMemory

限制上傳檔案的容量(默認 32MiB)

#### c.SaveUploadedFile

上傳檔案

* 第一個參數為上傳的檔案資料
* 第二個參數為上傳檔案名路徑 ex: /var/www/name.png

#### c.MultipartForm

取表單內全部的資料

`form.File["upload[]"]`從`c.MultipartForm`選出File類型且名稱為`upload[]`

[上傳單一檔案範例](#上傳單一檔案)

[上傳多個檔案](#上傳多個檔案)

### Router Static

#### router.Static

可以依照route訪問目錄

* 第一個參數為route
* 第二個參數為目錄路徑

#### router.StaticFile

可以依照route訪問檔案

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

#### c.JSON

輸出json格式且Content-Type: `application/json`

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

#### c.String

輸出string格式且Content-Type: `text/plain`

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

### Validator

[See Documentation](https://godoc.org/gopkg.in/go-playground/validator.v9)

#### required

欄位必填

    type Field struct {
        Field     string `form:"name" binding:"required"`
    }







#### c.Bind
#### c.BindJSON
#### c.BindQuery
#### c.ShouldBind
#### c.ShouldBindJSON
#### c.ShouldBindQuery

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

#### gin.Default()

`router := gin.Default()` 該route內建了`Logger()`  `Recovery()`兩個
Middleware分別是紀錄route log與遇到panic的log

#### gin.New()

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

### Logger

當訪問`http://localhost:8080/`會將訪問紀錄在log file內

透過`io.MultiWriter(f)`來將file轉成`io.write` 

並覆蓋gin默認`io.write`的對象

由於`gin.Default()`內有Logger Middleware且又是用到`gin.DefaultWriter`來當寫入對象

因此`c.String`會將Log寫入file
 
    func main() {
        f, _ := os.Create("gin.log")
        gin.DefaultWriter = io.MultiWriter(f)

        router := gin.Default()

        router.GET("/", func(c *gin.Context) {
            c.String(200, "pong")
        })

        router.Run()
    }

### 驗證 

驗證request內的資料


#### Form Validator

綁定一個form規則，如果請求的form不符合該規則就error
   
要求`user`跟`password`必填

如果`user` or `password`有一個不對就認證失敗，成功就回200

Bind 規則錯誤會回  `400 Content-Type: text/plain; charset=utf-8`

ShouldBind 規則錯誤會回 `Content-Type: application/json; charset=utf-8`

    type Login struct {
        User     string `form:"user" binding:"required"`
        Password string `form:"password" binding:"required"`
    }

    func main() {
        router := gin.Default()

        router.POST("/loginForm", func(c *gin.Context) {
            var form Login

            // if err := c.ShouldBind(&form); err == nil {
            if err := c.Bind(&form); err == nil {
                if form.User == "manu" && form.Password == "123" {
                    c.JSON(http.StatusOK, gin.H{"status": "you are logged in"})
                } else {
                    c.JSON(http.StatusUnauthorized, gin.H{"status": "unauthorized"})
                }
            } else {
                c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
            }
        })

        router.Run()
    }

Bad request 

    curl -v -X POST \
    http://localhost:8080/loginForm \
    -F 'user=manu'

    > {"error":"Key: 'Login.Password' Error:Field validation for 'Password' failed on the 'required' tag"}

Success request

    curl -v -X POST \
    http://localhost:8080/loginForm \
    -F 'user=manu' -F 'password=123'

    > {"status":"you are logged in"}

#### Json Validator

綁定一個json規則，如果請求的json不符合該規則就error
   
要求`user`跟`password`必填

如果`user` or `password`有一個不對就認證失敗，成功就回200

BindJSON 規則錯誤會回  `400 Content-Type: text/plain; charset=utf-8`

ShouldBindJSON 規則錯誤會回 `Content-Type: application/json; charset=utf-8`

    type Login struct {
        User     string `json:"user" binding:"required"`
        Password string `json:"password" binding:"required"`
    }

    func main() {
        router := gin.Default()

        router.POST("/loginJSON", func(c *gin.Context) {
            var json Login
            if err := c.ShouldBindJSON(&json); err == nil {
                if json.User == "manu" && json.Password == "123" {
                    c.JSON(http.StatusOK, gin.H{"status": "you are logged in"})
                } else {
                    c.JSON(http.StatusUnauthorized, gin.H{"status": "unauthorized"})
                }
            } else {
                c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
            }
        })

        router.Run()
    }

Bad request 

    curl -v -X POST \
    http://localhost:8080/loginJSON \
    -H 'content-type: application/json' \
    -d '{ "user": "manu" }'

    > {"error":"Key: 'Login.Password' Error:Field validation for 'Password' failed on the 'required' tag"}

Success request

    curl -v -X POST \
    http://localhost:8080/loginJSON \
    -H 'content-type: application/json' \
    -d '{ "user": "manu", "password": "123" }'

    > {"status":"you are logged in"}

#### Query Validator

綁定一個Query String規則，如果請求的Query String不符合該規則就error
   
要求`name`必填

BindQuery 規則錯誤會回  `400 Content-Type: text/plain; charset=utf-8`

ShouldBindQuery 規則錯誤會回 `Content-Type: application/json; charset=utf-8`

    type Person struct {
        Name    string `form:"name" binding:"required"`
        Address string `form:"address"`
    }

    func main() {
        route := gin.Default()

        route.Any("/query", func(c *gin.Context) {
            var person Person
            // if c.ShouldBindQuery(&person) == nil {
            if c.BindQuery(&person) == nil {
                c.String(http.StatusOK, "Success")
            } else {
                c.String(http.StatusBadRequest, "Bad")
            }
        })

        route.Run()
    }

Bad request 

    curl -X GET "localhost:8080/query?address=xyz"
    > Bad

    curl -X POST "localhost:8085/query?address=tw" -H "Content-Type:application/x-www-form-urlencoded"
    > Bad

Success request

    curl -X GET "localhost:8080/query?name=test&address=tw" 
    > Success


    curl -X POST "localhost:8085/query?name=test&address=tw" -H "Content-Type:application/x-www-form-urlencoded"
    > Success
