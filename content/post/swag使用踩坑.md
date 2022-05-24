---
title: "Swag使用踩坑"
date: 2021-06-08T17:37:18+08:00
draft: false
---
# swag 使用踩坑



## 快速入门

1. 下载swag

   `$ go get -u github.com/swaggo/swag/cmd/swag`

2. 初始化

   `swag init`

   运行成功后将会生成`docs/docs.go`

3. 结合gin使用

   ```go
   import "github.com/swaggo/gin-swagger" // gin-swagger middleware
   import "github.com/swaggo/files" // swagger embed files
   ```

4. 注册swagger路由

   ```go
   r := gin.New()
   
   	// use ginSwagger middleware to serve the API docs
   	r.GET("/swagger/*any", ginSwagger.WrapHandler(swaggerFiles.Handler))
   ```

5. 给路由handler添加文档注释

   ```go
   package controller
   
   import (
   	"fmt"
   	"net/http"
   	"strconv"
   
   	"github.com/gin-gonic/gin"
   	"github.com/swaggo/swag/example/celler/httputil"
   	"github.com/swaggo/swag/example/celler/model"
   )
   
   // ShowAccount godoc
   // @Summary Show a account
   // @Description get string by ID
   // @ID get-string-by-int
   // @Accept  json
   // @Produce  json
   // @Param id path int true "Account ID"
   // @Success 200 {object} model.Account
   // @Header 200 {string} Token "qwerty"
   // @Failure 400,404 {object} httputil.HTTPError
   // @Failure 500 {object} httputil.HTTPError
   // @Failure default {object} httputil.DefaultError
   // @Router /accounts/{id} [get]
   func (c *Controller) ShowAccount(ctx *gin.Context) {
   	id := ctx.Param("id")
   	aid, err := strconv.Atoi(id)
   	if err != nil {
   		httputil.NewError(ctx, http.StatusBadRequest, err)
   		return
   	}
   	account, err := model.AccountOne(aid)
   	if err != nil {
   		httputil.NewError(ctx, http.StatusNotFound, err)
   		return
   	}
   	ctx.JSON(http.StatusOK, account)
   }
   
   // ListAccounts godoc
   // @Summary List accounts
   // @Description get accounts
   // @Accept  json
   // @Produce  json
   // @Param q query string false "name search by q"
   // @Success 200 {array} model.Account
   // @Header 200 {string} Token "qwerty"
   // @Failure 400,404 {object} httputil.HTTPError
   // @Failure 500 {object} httputil.HTTPError
   // @Failure default {object} httputil.DefaultError
   // @Router /accounts [get]
   func (c *Controller) ListAccounts(ctx *gin.Context) {
   	q := ctx.Request.URL.Query().Get("q")
   	accounts, err := model.AccountsAll(q)
   	if err != nil {
   		httputil.NewError(ctx, http.StatusNotFound, err)
   		return
   	}
   	ctx.JSON(http.StatusOK, accounts)
   }
   
   //...
   
   ```

   项目启动后，访问`localhost:8080/swagger/index.html`，将会看到swag文档

## 踩坑

1.`@param`后面的comment一定不能少，不然`swag init`会报错，文档生成不了，一度让人怀疑人生（这什么鬼玩意）

```code
@param 
Parameters that separated by spaces. param name,param type,data type,is mandatory?,comment attribute(optional)
```

2. 结构体字段的注释添加方法

   > 官方文档中描述及其简单
   >
   > ### Description of struct
   >
   > ```
   > type Account struct {
   > 	// ID this is userid
   > 	ID   int    `json:"id"`
   > 	Name string `json:"name"` // This is Name
   > }
   > 
   > ```

   你没有看错，就这是注释的写法，即直接在结构体中写注释。

   例如：

   ```go
   type SysConfigQueryOutDto struct {
   	Code  string `json:"code" example:"SSL_MODE"` // 配置码
   	Value string `json:"value" example:"1"`       // 配置值
   }
   ```

   生成的文档是这样的：

   ![结构体的注释文档](https://files.catbox.moe/ls8s8r.png)

   

   [swag官方文档](https://github.com/swaggo/swag)


