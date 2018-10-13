# Form file

## c.FormFile

`c.FormFile("image")`可以從Form拿到名為image的file

## router.MaxMultipartMemory

限制上傳檔案的容量(默認 32MiB)

## c.SaveUploadedFile

上傳檔案

* 第一個參數為上傳的檔案資料
* 第二個參數為上傳檔案名路徑 ex: /var/www/name.png

## c.MultipartForm

取表單內全部的資料

`form.File["upload[]"]`從`c.MultipartForm`選出File類型且名稱為`upload[]`

# example

## 上傳單一檔案

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

## 上傳多個檔案

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
