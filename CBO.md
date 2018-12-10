# Kubernetes notes from "Container based orchestration"

## Setup

### Start minkube cluster and configure kubectl shell autocompletion
Cluster start
```
$ minikube start
$ kubectl get pods --namespace=kube-system
$ kubectl version
$ minikube version
$ kubectl config view
```
Note, that last command appears to simply be outputing the contents of `$HOME/.kube/config`.
### Shell autocompletion configuration
```
$ kubectl completion -h
$ kubectl completion bash > ~/.kube/completion.bash.inc
$ printf "
# Kubectl shell completion
source '$HOME/.kube/completion.bash.inc'
" >> $HOME/.bash_profile
$ source $HOME/.bash_profile
```
Then type this...
```
$ kubectl clu*<tab>*
```
... and confirm that it autocompletes to:
```
$ kubectl cluster-info
```
That's going to help. Autocompletion is applied not only for commands, but also named objects (e.g. pods etc), and flags too. This is pretty useful because for example, pods are assigned names with random characters at the end of them, so rather than copy-and-paste you can just hit `<tab>` to complete

## Test deployment

### Run echo server as hello-minikube (DEPRECATED)
~~Note, `run` is used here, but should use `create` in future due to former's deprecation~~
```
~~$ kubectl run hello-minikube --image=k8s.gcr.io/echoserver:1.4 --port=8080~~
~~$ kubectl get po~~
~~$ kubectl get deployment~~
~~$ kubectl get rs~~
```
~~Once the pod is running, expose a service via NodePort~~
```
~~$ kubectl expose deployment hello-minikube --type=NodePort~~
```
### Run echo server as hello-minikube

Due to `run` deprecation, had a play with `create`
```
$ kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
$ kubectl get deployment
$ kubectl get rs
$ kubectl get po
$ kubectl describe po hello-minikube-7b6c7fc556-lvqf4
```
$ kubectl expose -h
$ kubectl expose deployment hello-minikube --type=NodePort --port=80 --target-port=8080
```

This will open the service in the default browser:
```
$ minikube service hello-minikube
```
Whereas you can access via curl by using the --url option:
```
$ minikube service hello-minikube --url
$ curl $(minikube service hello-minikube --url)
```
For kicks, see what other services are running:
```
$ minikube service list
|-------------|----------------------|-----------------------------|
|  NAMESPACE  |         NAME         |             URL             |
|-------------|----------------------|-----------------------------|
| default     | hello-minikube       | http://192.168.99.100:31166 |
| default     | kubernetes           | No node port                |
| kube-system | kube-dns             | No node port                |
| kube-system | kubernetes-dashboard | No node port                |
|-------------|----------------------|-----------------------------|
```
Finally once done, delete the stuff:
```
$ kubectl delete deployment hello-minikube
$ kubectl delete svc hello-minikube
```
Alternatively, we can also use a selector to delete it all:

```
$ kubectl get all --selector=app=hello-minikube
NAME                                  READY   STATUS    RESTARTS   AGE
pod/hello-minikube-7b6c7fc556-trqfn   1/1     Running   0          20s

NAME                     TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/hello-minikube   NodePort   10.106.246.69   <none>        80:31938/TCP   15s

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-minikube   1         1         1            1           20s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-minikube-7b6c7fc556   1         1         1       20s

$ kubectl delete all --selector=app=hello-minikube
pod "hello-minikube-7b6c7fc556-trqfn" deleted
service "hello-minikube" deleted
deployment.apps "hello-minikube" deleted


```

