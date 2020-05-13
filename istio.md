# 服务网格 Istio

![](images/istio-icon.jpg)

---

**目录**

- [服务网格出现的背景](#/3)
- [服务网格是什么](#/)
- [Istio 的功能](#/)
- [Istio 的架构](#/)
- [服务使用 Istio 的要求](#/)
- [请求完整链路分析](#/)

---

## 服务网格出现的背景

---

### 容器被广泛应用

<img src="images/docker.png" height="200px" width="200px">
<img src="images/kubernetes.png" height="200px" width="200px">

- 轻量、启动速度快，性能损失小，环境统一等特性
- Docker 的出现，使容器技术得到更广泛的应用
- 容器编排工具 Kubernetes 被大量采用

---

### 微服务架构流行

<img src="images/microservice.png" height="230px" width="430px">

- 云计算飞速发展，云原生的概念被提出
- 微服务架构模式在大公司的大规模实践
- 很多的公司开始尝试使用微服务架构

---

### 微服务架构的问题

<img src="images/microservice-communication.png" height="250px" width="650px">

- 服务间通信复杂
- 开发服务框架，处理服务间通信
- 业务开发技术栈受制于服务框架
- 服务框架版本升级困难

---

<img src="images/thinking.png" height="300px" width="350px">

有人开始思考能不能把服务间的复杂通信下沉到基础设施层去做，让应用无感知呢？

---

## 服务网格是什么

---

### 服务网格的定义

服务网格(service mesh)，服务网格概念来源于文章 [What’s a service mesh? And why do I need one?](https://blog.buoyant.io/2017/04/25/whats-a-service-mesh-and-why-do-i-need-one/)

服务网格是一个专注于处理服务间通信的基础设施层，它负责在由现代云原生应用组成的复杂的服务拓扑中可靠的传递请求，在实践中，服务网格通常被实现为一组随着应用代码部署的轻量级网络代理，与此同时，应用无须感知它的存在。

---

### 服务网格的特点

![](images/philcalcado-service-mesh-1.png)

- 轻量级网络代理
- 应用无感知
- 应用间流量由服务网格接管
- 超时、重试、监控、追踪等功能下沉到服务网格层

---

## Istio 的功能

---

- 服务发现
- 服务路由
- 负载均衡
- 故障转移
- 安全加密
- 弹性服务
- 故障注入
- 日志监控
- 调用链追踪

基于以上功能可以实现更复杂的功能，比如：A/B 测试、金丝雀发布/灰度发布，服务限流，服务权限控制等功能。

---

## Istio 的架构

---

<img src="images/arch.svg" height="500px" width="600px">

Istio 在逻辑上分为数据平面（data plane）和控制平面（control plane）

---

![](images/envoy-logo.svg)

数据平面由部署为边车模式的智能代理组成，负责流量转发、策略实施与遥测数据上报

Envoy 作为一个边车（sidecar）与服务部署在同一个 Kubernetes Pod 中，通过 iptables 规则来把所有网络流量拦截到 Envoy 中

---

<img src="images/arch.svg" height="330px" width="500px">

控制平面负责接受用户配置，生成路由规则、分发路由规则到代理

- Pilot 提供服务发现功能和流量管理能力
- Citadel 提供服务间认证，通信加密
- Galley 负责用户输入规则的合规性校验

---

## 服务使用 Istio 的要求

---

**使用命名端口**

Service 命名端口，命名必须以协议开头，之后可以接中划线和其他字符，例如：http2-foo、http。默认会被视为 TCP 流量

```yaml
kind: Service
apiVersion: v1
metadata:
  name: service-go
  labels:
    app: service-go
spec:
  selector:
    app: service-go
  ports:
    - name: http
      port: 80
```

---

**服务关联问题**

同一个 Pod 的相同端口不能使用不同的协议，如图所示：

![](images/istio-guide-pod-mutilservice.png)

---

**部署使用 app 和 version 标签**

给 Deployment 指定明确的 app 和 version 标签

<img src="images/app-version-label.png" height="500px" width="650px">

---

## 请求完整链路分析

---

**Envoy 中的概念**

![](images/envoy-logo.svg)

- LDS：监听器发现服务，服务实例的监听端口
- RDS：路由发现服务，路由流量到不同的集群中
- CDS：集群发现服务，服务实例的集群
- EDS：端点发现服务，单个服务实例

---

### HTTP 请求分析

---

**DNS 解析**

---

**调用方发出请求**

![](images/listener-1.png)

---

![](images/listener-2.png)

---

![](images/listener-json.png)

---

![](images/route-1.png)

---

![](images/route-2.png)

---

![](images/cluster-1.png)

---

![](images/cluster-2.png)

---

![](images/endpoint.png)

---

**服务方接受请求**

---

![](images/inbound-listener.png)

---

![](images/inbound-listener-2.png)

---

![](images/inbound-cluster.png)

---

### 谢谢
