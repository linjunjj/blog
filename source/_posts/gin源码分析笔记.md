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
