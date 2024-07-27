一、部署应用

```
kubectl run kubernetes-bootcamp \
> --image=docker.io/jocatalin/kubernetes-bootcamp:v1 \
> --port=8080
```

二、访问应用

```
kubectl expose deployment/kubernetes-bootcamp \
--type="NodePort" \
--port=8080
```

