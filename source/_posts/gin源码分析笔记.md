---
title: gin源码分析笔记
date: 2019-05-18 22:13:42
tags: Golong
---

gin源码分析 render模块


```golang

type Render interface {
	Render(http.ResponseWriter) error
	WriteContentType(w http.ResponseWriter)
}

func writeContentType(w http.ResponseWriter, value []string) {
	header := w.Header()
	if val := header["Content-Type"]; len(val) == 0 {
		header["Content-Type"] = value
	}
}
```

**Rendor接口**
render两个接口
Render 承接状态
WriteContentType 写入的内容类型



writeContentType验证写入数据类型

Render有8中需要处理的数据类型 

> data  
```angular2html
type Data struct {
	ContentType string
	Data        []byte
}
```
> 
html
json  json.Marshal(r.Data)
mgspack  
redirect
test
xml
yaml


ginS.go

描述：

引用了sync.one锁
var internalEngine *gin.Engine


方法有
> LoadHTMLGlob    LoadHTMLFile SetHTMLTemplate NoRoute NoMethod 


binding.go
Default方法 选择数据类型


> 绑定描述绑定请求中存在的数据（如JSON请求主体、查询参数或表单发布）需要实现的接口。
```angular2html
type Binding interface {
	Name() string
	Bind(*http.Request, interface{}) error
}
```


```angular2html
type StructValidator interface {

	ValidateStruct(interface{}) error

	Engine() interface{}
}
```


default_validator.go

```angular2html
type defaultValidator struct {
	once     sync.Once
	validate *validator.Validate
}
```

方法： kindOfData  返回数值类型
lazyinit -> Engine  ValidateStruct方法


gin包中的

utils.go

方法
Bind(val interface{}) HandlerFunc()
WraF(f http.HandlerFunc) HandlerFunc()
WraH(f http.Handler) HandlerFunc
MarshalXML
parseAccept
joinPaths
resolveAddress

包含这个
```angular2html
type Context struct {
	writermem responseWriter
	Request   *http.Request
	Writer    ResponseWriter

	Params   Params
	handlers HandlersChain
	index    int8

	engine *Engine

	// Keys is a key/value pair exclusively for the context of each request.
	Keys map[string]interface{}

	// Errors is a list of errors attached to all the handlers/middlewares who used this context.
	Errors errorMsgs

	// Accepted defines a list of manually accepted formats for content negotiation.
	Accepted []string
}

```


tree.go文件
主要功能是 
树的基本操作

```angular2html
type node struct {
	path      string
	indices   string
	children  []*node
	handlers  HandlersChain
	priority  uint32
	nType     nodeType
	maxParams uint8
	wildChild bool
}
```
方法 incrementChildPrio


context.go
   ```angular2html
type Context struct {
	writermem responseWriter
	Request   *http.Request
	Writer    ResponseWriter

	Params   Params
	handlers HandlersChain
	index    int8

	engine *Engine

	// Keys is a key/value pair exclusively for the context of each request.
	Keys map[string]interface{}

	// Errors is a list of errors attached to all the handlers/middlewares who used this context.
	Errors errorMsgs

	// Accepted defines a list of manually accepted formats for content negotiation.
	Accepted []string
}
```

功能有
reset()
Copy()
HandlerName()
Handler()
Next()





```golang

type Engine struct {
	RouterGroup.
	RedirectTrailingSlash bool


	RedirectFixedPath bool

	HandleMethodNotAllowed bool
	ForwardedByClientIP    bool

	AppEngine bool

	UseRawPath bool
	UnescapePathValues bool
	MaxMultipartMemory int64

	delims           render.Delims
	secureJsonPrefix string
	HTMLRender       render.HTMLRender
	FuncMap          template.FuncMap
	allNoRoute       HandlersChain
	allNoMethod      HandlersChain
	noRoute          HandlersChain
	noMethod         HandlersChain
	pool             sync.Pool
	trees            methodTrees
}
```

