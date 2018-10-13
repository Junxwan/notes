# Query

## c.DefaultQuery

取Query String但可以設定默認值

* 第一個參數為Query String
* 第二個參數為默認值

## c.Query

取Query String，默認值為空值

* 第一個參數為Query String

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