### Kubernetes basic
入门操作

##### Create a Kubernetes cluster
```bash
## 创建集群
minikube version 
minikube start
minikube start --registry-mirror=https://registry.docker-cn.com
kubectl version
kubectl cluster-info
kubectl get nodes
minikube dashboard
```

##### Deploy an app
```bash
kubectl run kubernetes-bootcamp --image=docker.io/jocatalin/kubernetes-bootcamp:v1 --port=8080
kubectl get deployments
kubectl proxy
curl http://localhost:8001/version
curl http://localhost:8001/api/v1/proxy/namespaces/default/pods/$POD_NAME/
```

##### Explore your app
```bash
kubectl describe nodes
kubectl logs $POD_NAME
kubectl exec $POD_NAME env
kubectl exec $POD_NAME -ti bash
$ cat server.js
$ curl localhost:8080
$ exit
```

##### Expose your app publicly 
```bash
kubectl get nodes
kubectl get services
kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
kubectl get services
kubectl describe services/kubernetes-bootcamp
curl $MINIKUBE_IP:$NODE_PORT

kubectl describe deployment
kubectl get pods -l run=kubernetes-bootcamp
kubectl get services -l run=kubernetes-bootcamp
kubectl label pod $POD_NAME app=v1
kubectl describe pods $POD_NAME
kubectl get pods -l app=v1

kubectl delete service -l run=kubernetes-bootcamp
kubectl get services
curl $MINIKUBE_IP:$NODE_PORT
kubectl exec -ti $POD_NAME curl localhost:8080
```

###### Scale up your app
```bash
kubectl get deployments
kubectl scale deployments/kubernetes-bootcamp --replicas=4
kubectl get deployments
kubectl get pods -o wide
kubectl describe deployments/kubernetes-bootcamp
kubectl describe services/kubernetes-bootcamp
curl $MINIKUBE_IP:$NODE_PORT
kubectl scale deployments/kubernetes-bootcamp --replicas=2
kubectl get deployments
kubectl get pods -o wide
```

##### Update your app
```bash
kubectl get deployments
kubectl get pods
kubectl describe pods
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
kubectl get pods
kubectl describe services/kubernetes-bootcamp
curl $MINIKUBE_IP:$NODE_PORT
kubectl rollout status deployments/kubernetes-bootcamp
kubectl describe pods
kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v10
kubectl get deployments
kubectl get pods
kubectl describe pods
kubectl rollout undo deployments/kubernetes-bootcamp
kubectl get pods
kubectl describe pods
```
