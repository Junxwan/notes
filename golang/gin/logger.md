# Logger

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