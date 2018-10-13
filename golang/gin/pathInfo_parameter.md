# PathInfo Parameter

# c.Param()

`c.Param("name")`可以取得名為路徑上定義的name

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