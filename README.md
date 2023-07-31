## nestjs-bullmq-k8s
Nextjs + K8S + nestjs/bull

* [Nextjs](https://docs.nestjs.com/)
* [K8S](https://kubernetes.io/)
* [NPM BULL](https://github.com/OptimalBits/bull)
* [Helm](https://helm.sh/docs/intro/install/)
* [Artifacthub](https://artifacthub.io/)


### use docker redis

```
$ docker run -p 6379:6379 redis
$ curl -H "Content-Type: application/json" -X POST http://localhost:3000/transcode
```

### prepare your local k8s environment
Installing Helm, and go artifacthub to find third-party packages

```
$ mkdir k8s && cd k8s
// create a new chart with the given name
$ helm create nestjs-bullmq
$ cd nestjs-bullmq
```

Find Kuberbetes packages from Artifacthub

```
// update Chart.yaml, and add dependencies
$ helm dependency update
```

Cretae deployment YML

```
// use kubectl command, use docker desktop Kuberbetes
$ kubectl create deployment nestjs-bullmq --image=image-nestjs-bullmq --port 3000 --dry-run=client -o yaml > deployment.yaml

$ mv deployment.yaml ./templates/deployment.yaml
```

Docker Desktop RUN Kubernetes

```
// build docker images
$ docker build davischang/nestjs-bullmq .
$ docker push davischang/nestjs-bullmq:latest

// go to k8s/nestjs-bullmq
$ helm install nestjs-bullmq .
$ kubectl get pods
$ kubectl logs nestjs-bullmq-9bb7fccb4-8fxbd
$ kubectl get services

// create local service 
$ kubectl expose deployment nestjs-bullmq --type=NodePort --port 3000 --dry-run=client -o yaml > service.yaml

// after update yaml file
$ helm upgrade nestjs-bullmq .

// get logs for all pods at once
$ kubectl logs -l app=nestjs-bullmq --follow

// test
$ curl -X POST http://localhost:30285/transcode

// uninstall
$ helm uninstall nestjs-bullmq
```
