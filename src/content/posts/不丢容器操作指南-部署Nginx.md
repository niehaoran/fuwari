---
title: 不丢容器操作指南-部署Nginx
published: 2025-01-28
description: '1分钟快速部署Nginx网站服务器，学会容器基础操作和持久化存储'
image: '/public/favicon/budiuyun/nginx/1.png'
tags: [Nginx, 容器部署, Web服务器, 快速入门]
category: '不丢容器'
draft: false 
lang: ''
---

# 部署你的第一个 Nginx 网站

欢迎来到不丢容器的第一个实战教程！在这个教程中，你将学会如何**1分钟**部署一个可公网访问的 Nginx 网站服务器。

:::tip[你将学到什么]
- 如何创建和管理持久化存储卷
- 如何部署 Nginx 容器服务
- 如何配置外网访问和域名绑定
- 理解容器资源配额管理
:::

---

## 准备工作

在开始部署之前，确保你的容器空间有足够的资源配额。

![概览页面资源检查](/public/favicon/budiuyun/nginx/1.png)

:::note[资源配额说明]
- **Limits 配额**：限制容器可以使用的资源上限
- **Requests 配额**：容器运行所需的最低资源保证

就像汽车限速一样：Limits 是最高时速 60km/h，Requests 是最低保证速度 20km/h。
:::

---

## 第一步：创建持久化存储卷

<details>
<summary><strong>【可选】创建持久化存储卷</strong></summary>

由于容器重启时会还原，如果你需要持久化保存网站文件，可以创建存储卷。

### 1. 进入存储卷管理页面

在持久卷列表页面，点击 **创建存储卷**

![创建存储卷入口](/public/favicon/budiuyun/nginx/2.png)

### 2. 配置存储卷参数

![存储卷配置](/public/favicon/budiuyun/nginx/3.png)

```yaml
存储卷名称: nginx-data
存储容量: 1Gi
访问模式: ReadWriteOnce
```

:::important[存储卷配置要点]
- 选择合适的存储容量，根据网站文件大小决定
- `ReadWriteOnce` 模式适合单个容器挂载
:::

### 3. 创建成功确认

![创建成功](/public/favicon/budiuyun/nginx/4.png)

</details>

---

## 第二步：部署 Nginx 工作负载

### 1. 创建工作负载

打开工作负载列表，点击 **创建工作负载**

![创建工作负载](/public/favicon/budiuyun/nginx/5.png)

### 2. 选择配置方式

![配置界面选择](/public/favicon/budiuyun/nginx/6.png)

:::tip[配置方式选择]
- **容器配置界面**：适合新手，图形化操作简单
- **YAML 配置**：适合有 Kubernetes 基础的用户
:::

### 3. 基本配置

![基本配置](/public/favicon/budiuyun/nginx/7.png)

```yaml
应用名称: nginx-web
镜像地址: nginx:latest
副本数量: 1
资源配置:
  CPU请求: 100m      # 根据实际需求调整
  CPU限制: 500m      # 防止资源过度占用
  内存请求: 128Mi    # 根据网站大小调整
  内存限制: 256Mi    # 设置合理上限
```

:::important[资源配置说明]
- **请求值 (requests)**：容器启动时保证分配的资源
- **限制值 (limits)**：容器可使用的最大资源

**配置建议**：
- 上述数值仅为示例，实际使用需要根据以下因素调整：
  - 网站访问量大小
  - 静态文件数量和大小
  - 服务器硬件资源
  - 其他应用的资源占用

**参考配置**：
- **小型网站**：CPU 50m-100m，内存 64Mi-128Mi
- **中型网站**：CPU 100m-200m，内存 128Mi-256Mi  
- **大型网站**：CPU 200m-500m，内存 256Mi-512Mi

**如何确定合适的配置**：
1. **初始配置**：先用较小的值启动
2. **监控观察**：通过不丢云监控面板查看实际使用情况
3. **逐步调优**：根据监控数据逐步调整
4. **压力测试**：模拟高峰期访问量进行测试
:::

### 4. 端口配置

![端口配置](/public/favicon/budiuyun/nginx/8.png)

```yaml
容器端口: 80
协议: TCP
服务类型: ClusterIP
```

:::warning[端口配置注意]
Nginx 默认监听 80 端口，确保端口配置正确，否则服务无法访问。
:::

### 5. 存储卷挂载

<details>
<summary><strong>【可选】配置存储卷挂载</strong></summary>

如果你在第一步创建了存储卷，需要在这里进行挂载配置。

![存储卷挂载](/public/favicon/budiuyun/nginx/9.png)

```yaml
选择存储卷: nginx-data
挂载路径: /usr/share/nginx/html
```

:::note[挂载路径说明]
`/usr/share/nginx/html` 是 Nginx 默认的网站文件目录，将存储卷挂载到这里可以持久化保存网站文件。
:::

</details>

### 6. 应用配置并创建

![应用配置](/public/favicon/budiuyun/nginx/10.png)

---

<details>
<summary><strong>【可选】查看工作负载管理和监控</strong></summary>

### 查看工作负载状态

![工作负载管理](/public/favicon/budiuyun/nginx/11.png)

在工作负载页面中找到刚创建的应用，点击 **管理** 查看详细信息。

### 查看容器详情

![容器详情](/public/favicon/budiuyun/nginx/12.png)

在这里可以查看：
- 容器运行状态
- 资源使用情况  
- 日志信息
- 事件记录

</details>

---

## 第三步：配置外网访问

### 1. 创建服务

![创建服务](/public/favicon/budiuyun/nginx/13.png)

进入服务页面，点击 **快速创建服务**

### 2. 创建应用路由

![创建路由](/public/favicon/budiuyun/nginx/14.png)

进入应用路由列表，点击 **创建路由**

### 3. 配置路由规则

![路由配置](/public/favicon/budiuyun/nginx/15.png)

```yaml
路由名称: nginx-route
域名: 自动生成或自定义
服务: nginx-web
端口: 80
启用HTTPS: 推荐开启
```

### 4. 获取访问域名

![访问域名](/public/favicon/budiuyun/nginx/16.png)

创建成功后会生成可访问的域名，点击即可访问你的网站！

---

## 第四步：验证部署结果

### 访问成功页面

![部署成功](/public/favicon/budiuyun/nginx/17.png)

:::tip[上传网站文件]
如果你配置了持久卷，需要手动上传网站文件到存储卷中。如果没有配置持久卷，将看到 Nginx 默认欢迎页面。
:::

---

## 下一步操作

恭喜！你已经成功部署了第一个 Nginx 网站服务器。接下来你可以：

1. **上传自定义网站文件** - 将你的 HTML、CSS、JS 文件上传到持久卷
2. **配置自定义域名** - 绑定你自己的域名
3. **学习更多部署** - 尝试部署数据库或其他服务

:::note[学习建议]
建议继续学习 [MySQL 数据库部署](/posts/不丢容器操作指南-部署mysql/) 或 [Minecraft 服务器部署](/posts/不丢容器操作指南-部署minecraft服务器/)
:::

---

## 常见问题

### Q: 为什么访问网站显示 404？
A: 如果配置了持久卷但没有上传网站文件，会显示 404。请确保在 `/usr/share/nginx/html` 目录中有 `index.html` 文件。

### Q: 如何上传网站文件？
A: 可以通过容器终端或文件管理工具将文件上传到挂载的存储卷中。

### Q: 服务无法访问怎么办？
A: 检查以下几点：
- 容器是否正常运行
- 端口配置是否正确
- 应用路由是否创建成功

---

**总结**：通过这个教程，你学会了完整的容器化部署流程，包括存储管理、服务配置和外网访问。这些技能是容器化部署的基础，可以应用到任何其他服务的部署中！

