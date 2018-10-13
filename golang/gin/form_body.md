# Form body

## c.DefaultPostForm

取Form參數名但可以設定默認值

* 第一個參數為Form參數名
* 第二個參數為默認值

## c.PostForm

取Form參數名，默認值為空值

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
