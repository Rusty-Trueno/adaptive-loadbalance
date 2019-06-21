# adaptive-loadbalance
阿里中间件大赛
# 阿里中间件挑战赛
- 初赛：自适应负载均衡算法的设计与实现
- 复赛：基于消息队列机制的存储引擎设计


## dev : 同步本地开发版本

## 项目文件结构
- |--- docs 存放项目文档，包扩工程文档，技术文档，开发问题和操作规范
- |--- adaptive-balance  负载均衡算法
- |--- internal-service  provider service 
- |--- dubbo-internal 比赛使用的 dubbo 依赖


### 依赖项目
- dubbo-internal 
  比赛专用版本的 dubbo, 在本地手动安装依赖
  ```
  ~# git clone https://code.aliyun.com/middlewarerace2019/dubbo-internal.git
  ~# mvn clean install -Dmaven.test.skip=true
  ```
- adaptive-loadbalance
  ```
  ~# git clone https://code.aliyun.com/middlewarerace2019/adaptive-loadbalance.git
  ~# mvn clean install -Dmaven.test.skip=true
  ```
- internal-service
  内置服务，负责加载实现的负载均衡算法，启动 Consumer 和 Provider 程序。
  ```
  ~# git clone https://code.aliyun.com/middlewarerace2019/internal-service.git
  ~# mvn clean install -Dmaven.test.skip=true
  ```
  ***注意先后顺序：internal-service 依赖 adaptive-loadbalance
### 快速开始
1. 按照上述操作安装 `dubbo` 和 `service`两个依赖包。
2. 配置hosts
  ```
  ~# vim /etc/hosts
  #------添加-------
  127.0.0.1 provider-small
  127.0.0.1 provider-medium
  127.0.0.1 provider-large
  :wq
  ~# 
  ```
3. 运行 internal-service 项目中的 com.aliware.tianchi.MyProvider 启动 Provider，为了模拟负载均衡场景，需要启动三个 Provider，分别指定启动参数 -Dquota=large、-Dquota=medium、-Dquota=small

  启动provider
  ```
  ~# cd internal-service/service-provider
  ~# mvn exec:java -Dexec.mainClass="com.aliware.tianchi.MyProvider" -Dquota=large
  ~# mvn exec:java -Dexec.mainClass="com.aliware.tianchi.MyProvider" -Dquota=medium
  ~# mvn exec:java -Dexec.mainClass="com.aliware.tianchi.MyProvider" -Dquota=small
  ```
  启动consumer
  ```
  ~# cd internal-service/service-consumer
  ~# mvn exec:java -Dexec.mainClass="com.aliware.tianchi.MyConsumer"
  ```
4. 运行 internal-service 项目中的 com.aliware.tianchi.MyConsumer 启动 Consumer
5. 打开浏览器 http://localhost:8087/call，显示OK即表示配置成功
  
### 本地压测
- 安装 http 压测工具 `wrk`， 进入想要安装 wrk 的目录下
  ```
  ~# sudo apt-get update
  ~# sudo apt-get instll wrk
  ~# cd wrk | make
  ```

- 在 internal-service 项目中存放了一个 wrk.lua 脚本
  ```
  ~# cd internal-service
  ~# wrk -t4 -c1024 -d60s -T5 --script=./wrk.lua --latency http://localhost:8087/invoke
  ```
  ps: 敲下命令 `wrk`，可以看到使用帮助
