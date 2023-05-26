# 最简单的构建

先安装 [Docker Desktop](https://www.docker.com/products/docker-desktop/)，并启用 kubectl

## 简单程序

main.go

```go
package main

import (
	"fmt"
	"log"
	"net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintln(w, "Hello, 世界")
}

func main() {
	http.HandleFunc("/", handler)
	fmt.Println("Running demo app. Press Ctrl+C to exit...")
	log.Fatal(http.ListenAndServe(":8888", nil))
}
```

go.mod

```mod
module github.com/cloudnativedevops/demo/hello
```

Dockerfile

```dockerfile
FROM golang:1.17-alpine AS build

WORKDIR /src/
COPY main.go go.* /src/
RUN CGO_ENABLED=0 go build -o /bin/demo

FROM scratch
COPY --from=build /bin/demo /bin/demo
ENTRYPOINT ["/bin/demo"]
```

## 构建镜像

```bash
docker image build -t myhello .
```

## 运行容器

```bash
docker container run -p 9999:8888 myhello
```

访问 http://localhost:9999/

## 推送镜像

Dockers ID 注册地址：https://hub.docker.com/signup

```bash
docker login

docker image tag myhello YOUR_DOCKER_ID/myhello
docker image push YOUR_DOCKER_ID/myhello
```

## 通过 Kubernetes 运行

```bash
kubectl run demo --image=YOUR_DOCKER_ID/myhello --port=9999 --labels app=demo
kubectl port-forward pod/demo 9999:8888
kubectl get pods --selector app=demo
```

Last Modified 2023-05-26
