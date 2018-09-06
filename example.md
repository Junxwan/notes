# Golang Example

- [http](#http)
    - [StripPrefix](#stripprefix)
    - [FileServer](#fileserver)
- [os](#os)
    - [Create](#create)

## http

### StripPrefix

設定一個前綴路徑並濾掉該路徑，`curl http://localhost:8080/static/test`會輸出`/test`

* 第一個參數是想要濾掉的前綴路徑
* 第二個參數是Handler，必須有實作interface ServeHTTP()

範例:

    type testHandler struct{}

    func (h *testHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte(r.URL.Path))
    }

    func main() {
        http.Handle("/static/test", http.StripPrefix("/static", &testHandler{}))

        err := http.ListenAndServe(":8080", nil)

        if err != nil {
            log.Fatal("ListenAndServe: ", err)
        }
    }

### FileServer

建立一個靜態文件的Handler，`http://localhost:8080/static/`會指向`public`目錄

* 第一個參數目錄路徑

範例:

    func main() {
        h := http.FileServer(http.Dir("./public"))

        http.Handle("/static/", http.StripPrefix("/static/", h))

        err := http.ListenAndServe(":8080", nil)

        if err != nil {
            log.Fatal("ListenAndServe: ", err)
        }
    }

## os

### Create

建立一個`message.log`並寫入`hello world`

    func main() {
        fd, _ := os.Create("message.log")
        buf := []byte("hello world")
        fd.Write(buf)
        fd.Close()
    }
