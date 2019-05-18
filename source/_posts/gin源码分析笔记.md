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
