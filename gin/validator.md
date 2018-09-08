# Validator

[See Documentation](https://godoc.org/gopkg.in/go-playground/validator.v9)

## required

name欄位必填

    type Test struct {
        Name string `form:"name" binding:"required"`
    }

## or

name欄位必須符合`email` or `alpha`兩種格式

    type Test struct {
        Name string `form:"name" binding:"email|alpha"`
    }

## len

name欄位長度必須剛好為10

    type Test struct {
        Name string `form:"name" binding:"len=10"`
    }

## max

name欄位長度最大為10

    type Test struct {
        Name string `form:"name" binding:"max=10"`
    }

## min

name欄位長度最小為10

    type Test struct {
        Name string `form:"name" binding:"min=10"`
    }

## eq

name欄位值必須為`test`

    type Test struct {
        Name string `form:"name" binding:"eq=test"`
    }

## ne

name欄位值必須不為`test`

    type Test struct {
        Name string `form:"name" binding:"ne=test"`
    }

## oneof

name欄位值必須為`red` or `green`

    type Test struct {
        Name string `form:"name" binding:"oneof=red green 
    "`
    }

> 注意oneof之後要跳行不然執行會出錯

## gt

name欄位長度必須大於10

    type Test struct {
        Name string `form:"name" binding:"gt=10"`
    }

## gte

name欄位長度必須大於等於10

    type Test struct {
        Name string `form:"name" binding:"gte=10"`
    }

## lt

name欄位長度必須小於10

    type Test struct {
        Name string `form:"name" binding:"lt=10"`
    }

## lte

name欄位長度必須小於等於10

    type Test struct {
        Name string `form:"name" binding:"lte=10"`
    }

## unique

name欄位是arrays & slices則不可以有重覆的值

    type Test struct {
        Name []string `validate:"unique"`
    }

    var validate *validator.Validate

    func main() {
        validate = validator.New()

        err := validate.Struct(&Test{
            Name: []string{"1", "2"},
        })

        if (err == nil) {
            fmt.Println(true)
        } else {
            fmt.Println(false)
        }
    }

## alpha

name欄位值只能是英文

    type Test struct {
	    Name string `form:"name" binding:"alpha"`
    }

## alphanum

name欄位值可包含英文跟數字

    type Test struct {
	    Name string `form:"name" binding:"alphanum"`
    }

## numeric

name欄位值只能是數字

    type Test struct {
	    Name string `form:"name" binding:"numeric"`
    }

## email

name欄位值只能是email格式

    type Test struct {
	    Name string `form:"name" binding:"email"`
    }

## url

name欄位值只能是url格式

    type Test struct {
	    Name string `form:"name" binding:"url"`
    }

## uri

name欄位值只能是uri格式

    type Test struct {
	    Name string `form:"name" binding:"uri"`
    }

## contains

name欄位值內需包含`test`

    type Test struct {
	    Name string `form:"name" binding:"contains=test"`
    }

## containsany

name欄位值內需包含`！@＃？`任一字

    type Test struct {
        Name string `form:"name" binding:"containsany=！@＃？"`
    }

## excludes

name欄位值內不能包含`test`

    type Test struct {
	    Name string `form:"name" binding:"excludes=test"`
    }

## excludesall

name欄位值內不能包含`！@＃？`任一字

    type Test struct {
        Name string `form:"name" binding:"excludesall=！@＃？"`
    }

## c.Bind

綁定一個form規則，如果請求的form不符合該規則就
返回`400 Content-Type: text/plain; charset=utf-8`

[範例](#form-validator)

## c.BindJSON

綁定一個json規則，如果請求的json不符合該規則就
返回`400 Content-Type: text/plain; charset=utf-8`

[範例](#json-validator)

## c.BindQuery

綁定一個Query String規則，如果請求的Query String不符合該規則就
返回`400 Content-Type: text/plain; charset=utf-8`

[範例](#query-validator)

## c.ShouldBind

[範例](#form-validator)

綁定一個form規則，如果請求的form不符合該規則就
返回`400 Content-Type: application/json; charset=utf-8`

## c.ShouldBindJSON

綁定一個json規則，如果請求的json不符合該規則就
返回`400 Content-Type: application/json; charset=utf-8`

[範例](#json-validator)

## c.ShouldBindQuery

綁定一個Query String規則，如果請求的Query String不符合該規則就
返回`400 Content-Type: application/json; charset=utf-8`

[範例](#query-validator)

# example 

驗證request內的資料

## Form Validator

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

## Json Validator

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

## Query Validator

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
