# Run Http Server

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