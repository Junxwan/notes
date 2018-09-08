# Response body

## c.JSON

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

## c.String

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