# Router Static

## router.Static

可以依照route訪問目錄

* 第一個參數為route
* 第二個參數為目錄路徑

## router.StaticFile

可以依照route訪問檔案

* 第一個參數為route
* 第二個參數為檔案路徑

[靜態目錄route範例](#靜態目錄route)

[靜態檔案route範例](#範例)

兩者差異

* `router.Static`預設是抓目錄，因為底層使用`http.FileServer`
* `router.StaticFile`預設是抓檔案，因為底層使用`c.File`

# example

## 靜態目錄route

設定一個`/`路徑指向`public`目錄，默認會找該目錄的index.html

    func main() {
        router := gin.Default()
        
        router.Static("/", "./public")

        router.Run()
    }

## 靜態檔案route

設定一個`/`路徑指向`public/info.html`檔案

    func main() {
        router := gin.Default()
        
        router.StaticFile("/", "./public/info.html")

        router.Run()
    }