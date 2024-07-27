在Kubernetes（k8s）中，"Readiness Probe" 是用来确定一个容器是否已经准备好为流量提供服务的。Readiness Probe 有助于确保只有在应用程序准备好接受请求时，服务才会将流量路由到它。Kubernetes 支持以下几种类型的 Readiness Probe：

1. **HTTP GET Probe**：
    - Kubernetes 向容器内的一个指定路径发送 HTTP GET 请求，如果返回的状态码在 200 到 399 范围内，则认为容器已就绪。
    - 示例：
      ```yaml
      readinessProbe:
        httpGet:
          path: /healthz
          port: 8080
        initialDelaySeconds: 5
        periodSeconds: 10
      ```

2. **TCP Socket Probe**：
    - Kubernetes 尝试通过指定的端口建立 TCP 连接。如果能够成功建立连接，则认为容器已就绪。
    - 示例：
      ```yaml
      readinessProbe:
        tcpSocket:
          port: 8080
        initialDelaySeconds: 5
        periodSeconds: 10
      ```

3. **Exec Probe**：
    - Kubernetes 在容器内执行指定的命令。如果命令以 0 退出，则认为容器已就绪。
    - 示例：
      ```yaml
      readinessProbe:
        exec:
          command:
            - cat
            - /tmp/healthy
        initialDelaySeconds: 5
        periodSeconds: 10
      ```

每种 Readiness Probe 类型都有一些共同的配置参数：

- **initialDelaySeconds**：容器启动后等待多长时间才开始探测。
- **periodSeconds**：探测之间的间隔时间。
- **timeoutSeconds**：每次探测的超时时间。
- **successThreshold**：探测成功的最小连续次数。
- **failureThreshold**：探测失败的最小连续次数。

通过配置合适的 Readiness Probe，可以有效地控制流量的路由，确保只有在容器准备好提供服务时，才会接收和处理请求。