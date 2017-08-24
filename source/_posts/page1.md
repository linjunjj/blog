# 业务实体

- 手机
- 网关
- 工地服务器
- 云端服务器

# 功能实体

- 登录
- 注册
- 获取短信验证码
- 上线
- 看电影
- 充值


## 部署加载流程

### 第一步 将portal.html（落地页）加载到工地网关

#### 乐光缓存逻辑流程图
```
sequenceDiagram
网关->>网关: 1. 配置云端URL为 portal.html
网关->>云端服务器:  2. 请求网关发送portal.html
云端服务器->>网关: 3. 发送portal.html
```
### 第二步 手机使用WIFI

```
sequenceDiagram
手机->>网关: 1. 手机连接上云网WIFI
网关->>手机: 2. 跳出页面 打开portal.html
手机->>云端服务器: 3. portal.html -> 请求路由页面
云端服务器->>手机: 4. 返回 role.html
手机->>网关: 5. role.html -> 获得网关mac地址
网关->>手机: 6. 返回Mac地址
手机->>云端服务器: 7. 根据Mac地址获得重定向页面编号
云端服务器->>手机: 8. 返回重定向页面编号
手机->>云端服务器: 9. 根据重定向页面编号请求真实展示页面
云端服务器->>手机: 10. 返回真实展示页面
```

## page-1 使用流程

### page-1 定义

通过简单的页面，吸引用户输入手机号码，成为我们的预约用户。

### page-1 页面
页面1|页面2
:--:|:--:
![page-1#1](http://ouls09sce.bkt.clouddn.com/document/page-1/1.png)|![page-1#2](http://ouls09sce.bkt.clouddn.com/document/page-1/2.png)
主页|结果页

### page-1 交互流程

```
sequenceDiagram
手机->>云端服务器: 页面1: 输入手机号到11位时
云端服务器-->云端服务器: 服务器记录用户信息
云端服务器->>手机: 页面2：展示
```

## page-2 使用流程

### page-2 定义

用户可以通过本页面来使用以下服务
- 看电影
- 上外网

### page-2 页面

页面1|页面2
:--:|:--:
![page-2#1](http://ouls09sce.bkt.clouddn.com/document/page-2/1.png)|![page-2#2](http://ouls09sce.bkt.clouddn.com/document/page-2/2.png)
登录页|状态页

页面3|页面4
:--:|:--:
![page-2#3](http://ouls09sce.bkt.clouddn.com/document/page-2/3.png)|![page-2#3](http://ouls09sce.bkt.clouddn.com/document/page-2/4,png)
电影展示|电影播放页

### page-2 交互流程