# liuma
由Helm管理的开箱即用的分布式文件服务器

# 业务流程

1. 通过系统token在后端客户端发起请求获取上传或下载令牌，
2. 前端通过令牌进行上传或下载

# 安装说明

**前置条件: 系统配置好Kubernetes分布式环境**

- 本地版  
1. clone下当前资源仓库，在devops目录下执行 helm install ./liuma 自行按需添加其他参数

- 线上版  
1. helm repo add '自定义仓库名' https://docker.hub.shoogoome.com/chartrepo/liuma
2. helm repo update
3. helm install '自定义仓库名'/liuma 自行按需添加其他参数

# 接口说明
ps: 需附带system_token的接口应为后端访问接口，前端与系统交互应使用后端获取的临时token
```
/server/token [put] 修改系统token
headers: system_token 附带系统token
return: status success
```
```
/server/active [get] 获取活跃信号
headers: system_token 附带系统token
return status 'ip列表'
```
```
/upload/token [get] 生成上传令牌
headers: system_token 附带系统token
url参数: hash 文件hash
return token token
```
```
/download/token [get] 生成下载令牌
headers: system_token 附带系统token
url参数: hash 文件hash
return token token
```
```
/upload/single [post] 单文件上传
headers: token 上传临时token
form-data: file 文件
return status success
```
```
/upload/chuck [post] 文件分块上传
headers: token 上传临时token
url参数: chuck 当前分片数
form-data: file 分片文件
return status success
```
```
/upload/finish [get] 完成上传(仅断点续传模式需要)
headers: token 上传临时token
url参数: chuck_num 分片总数  
        file_name 文件名称
return status success
```
```
/download [get] 下载文件
headers: token 下载临时token
return file
```

# PS
1.0版本存在许多问题，将在后续版本持续更新改善
1. 服务发现功能，当前是在系统启动时统一初始化服务数量相关参数，无法做到使用过程中动态水平扩容。考虑在后续采用诸如RabbitMQ等手段改善服务发现功能
2. 当前版本没有实现资源版本控制功能
3. 当前使用的中间件 redis、mongo仍处于单机形式，后续将部署为集群可扩容形式
4. 与主系统之间识别手段单一简陋
5. 忘记支持断点下载了...