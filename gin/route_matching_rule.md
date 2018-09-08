# Route Matching Rule

## /user/:name

嚴僅的規則如果在`:name`後面又接了其他path info則不匹配

See [Documentation](https://github.com/julienschmidt/httprouter#named-parameters)

    Pattern: /user/:name

    /user/                    不匹配
    /user/test                匹配
    /user/test/index.go       不匹配

## /user/:name/*action

鬆散的規則如果在`:name`後面又接了其他path info同樣匹配

See [Documentation](https://github.com/julienschmidt/httprouter#catch-all-parameters)

    Pattern: /user/:name/*action

    /user/                          不匹配
    /user/test                      匹配
    /user/test/index.go             匹配