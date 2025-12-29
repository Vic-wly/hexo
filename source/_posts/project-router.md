title: 项目路由及文件方法梳理（不包含项目内容，新写的demo接口）
author: Laiyong Wang
date: 2025-07-14 14:38:11
tags:
---
## 理清该项目路由机制，后续关注代码实现即可

## 路由主路径文件+代码解释
### 1. main.go文件

```
core.RunServer()
```

### 2. core/server.go 文件

```
routers := initialize.Routers()
```

### 3. initialize/router.go 文件 //初始化各种路由的地方

```
	// 方便统一添加路由组前缀 多服务器上线使用 配置的是api
	pubRouter := engine.Group(global.Cfg.System.RouterPrefix)
	{
		// Health monitoring  心跳检测，第一次运行后，需要数据库等初始化完成才能请求别的接口
		health.HealthRouter(pubRouter)
	}
    
	appRouter := pubRouter.Group("")
    // 这个方法初始化了 api 的 router
	initAppRouter(appRouter)

	// open web api 
	if global.Cfg.System.RouterPrefixWeb != "" {
	    // 这个是基于 api 生成的，假如配置了web，则链接是api/web
		webRouter := pubRouter.Group(global.Cfg.System.RouterPrefixWeb)
        // 这个方法初始化了 web 的 router
		iniWebRouter(webRouter)
	}
```
介绍下 iniWebRouter ，因其与initAppRouter一致则initAppRouter不介绍了
```
// 初始化管理后台路由
func iniWebRouter(pubRouter *gin.RouterGroup) {
	privateGroup := pubRouter.Group("")
	// 中间件，开启可使用    
	// privateGroup.Use(mw.AuthorityMiddleware(), mw.AutoBackupMiddleware())
    
	// 下方新开内容目录4、5 介绍 这个结构体 RouterGroupApp
	routerFunc := router.RouterGroupApp
	for {
		//心跳检测 全部初始化好后才注册路由
		if health.IsHealth() {
			{
				//参考下方目录4、5 的结构体 RouterGroupApp 
				routerFunc.Web.InitProjectRouter(privateGroup)
			}

			global.Log.Info(" iniWebRouter server health check done, enabled all route.")
			break
		}
		time.Sleep(time.Millisecond * 300)
	}
}
```

### 4. internal/router/router.go 包含所有注册路由的结构体

```
package router

import (
	"crypto-evaluate/internal/router/web"
)

type RouterGroup struct {
	Web       web.RouterGroup //有几个结构体写几个，import后
}

var RouterGroupApp = new(RouterGroup)

```

### 5. internal/router/web/enter.go web对应包下的enter.go 文件 结构体RouterGroup 定义

```
package web

type RouterGroup struct {
	ProjectRouter
}

```
### 6. internal/router/web/project.go  结构体成员 ProjectRouter的实现文件

```
package web

import (
	v1 "crypto-evaluate/internal/api/web/v1"

	"github.com/gin-gonic/gin"
)

type ProjectRouter struct{}

func (e *ProjectRouter) InitProjectRouter(Router *gin.RouterGroup) (R gin.IRoutes) {
	projectRouter := Router.Group("project")
	projectApi := v1.ApiGroupWeb.CryptoApiGroup.ProjectApi
	{
		projectRouter.GET("add", projectApi.Add)
	}
	return projectRouter
}

```

## 逻辑代码主路径文件+代码解释

### 7. internal/api/web/v1/v1.go 该版本所有接口的汇总

```
package v1

import (
	"crypto-evaluate/internal/api/web/v1/crypto" //这就是我新写的包 crypto
)

type ApiGroup struct {
	CryptoApiGroup crypto.ApiGroup //新写的包，在这里汇总，有几个加几个
}

var ApiGroupWeb = new(ApiGroup)
```

### 8. internal/api/web/v1/crypto/enter.go 新写的包 crypto 将其内的结构体及service里的方法汇总

```
package crypto

import "crypto-evaluate/internal/service"

type ApiGroup struct {
	ProjectApi //有多少结构体则写多少，我这里同目录只新增了一个，故只有一个
}

var (
	// service 层的方法，在这里饮用下，方便使用
	projectService = service.ServiceGroupApp.WebServiceGroup.ProjectService
)
```

### 9. internal/api/web/v1/crypto/project.go 新写的包内的一个结构体，即上一个目录的 ProjectApi

```
package crypto

import (
	"crypto-evaluate/common"
	"crypto-evaluate/internal/model"
	request2 "crypto-evaluate/internal/model/app/app_req/crypto/request"

	"github.com/gin-gonic/gin"
)

// 在enter 里汇总 
type ProjectApi struct{}

// Add
//
//	@Tags		项目
//	@Summary	添加项目
//	@Security	ApiKeyAuth
//	@accept		application/json
//	@Produce	application/json
//	@Param		req	body		request.ProjectAddReq	true	"请求"
//	@Success	200	{object}	model.Response
//	@Router		/project/add [post]
func (p *ProjectApi) Add(c *gin.Context) {
	var req request2.ProjectAddReq
	err := c.ShouldBindJSON(&req)
	if err != nil {
		model.FailWithError(common.ParameterException, err, c)
		return
	}
    //使用了 enter 文件里的引用
	err = projectService.Add(req)
	if err != nil {
		model.FailWithError(common.AddFailed, err, c)
		return
	}
	model.OkWithMessage(common.AddSuccess, c)
}
```

## 服务层逻辑代码主路径文件+代码解释


### 10. internal/service/web/enter.go 

```
package web

import (
	"crypto-evaluate/internal/service/system"
)

type ServiceGroup struct {
	ProjectService //有多少结构体则写多少，我这里同目录只新增了一个，故只有一个
}

var (
	//引入该文件，方便调用方法
	ProjectSrv = ProjectService{}

	//system的运行被其他service调用 但是system不要调用其他
	configService = system.ConfigService{}
)
```

### 11. internal/service/web/project.go 新增的结构体，代码逻辑在这里，然后在目录6里调用

```
package web

import (
)

type ProjectService struct{}

func (p *ProjectService) Add(req request.ProjectAddReq) error {
}

```

### 12. internal/service/service.go 汇总在一起，方便使用，例如目录8里的引用

```
package service

import (
	webService "crypto-evaluate/internal/service/web"
)

type ServiceGroup struct {
	WebServiceGroup       webService.ServiceGroup
}

var ServiceGroupApp = new(ServiceGroup)
```


# 路由基本如此，可以写业务逻辑了，我他妈也是服了，就两层，一层路由，一层service，能搞的这么麻烦