# Kubernetes notes from "Container based orchestration"

## Setup

### Start minkube cluster and configure kubectl shell autocompletion
Cluster start
```
minikube start
kubectl get pods --namespace=kube-system
kubectl version
minikube version
kubectl config view
```
### Shell autocompletion configuration
kubectl completion -h
kubectl completion bash > ~/.kube/completion.bash.inc
printf "
# Kubectl shell completion
source '$HOME/.kube/completion.bash.inc'
" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
Then type this...
```
kubectl clu<tab>
```
... and confirm that it autocompletes to:
```
kubectl cluster-info
```
That's going to help. TODO: Learn exactly where autocompletion is applied - it seems not only commands but also named objects, and flags autocomplete too.

## Test deployment

### Run echo server as hello-minikube
Note, "run" is used here, but should use "create" in future due to former's deprecation
```
kubectl run hello-minikube --image=k8s.gcr.io/echoserver:1.4 --port=8080
kubectl get po
kubectl get deployment
kubectl get rs
```
Once the pod is running, expose a service via NodePort
```
kubectl expose deployment hello-minikube --type=NodePort
```
This will open the service in the default browser:
```
minikube service hello-minikube
```
Whereas you can access via curl by using the --url option:
```
minikube service hello-minikube --url
curl $(minikube service hello-minikube --url)
```
For kicks, see what other services are running:
```
minikube service list
```
Finally once done, delete the stuff:
```
kubectl delete deployment hello-minikube
kubectl delete svc hello-minikube
```
TODO: Actually you might be able to delete all, by specifying an appropriate selector

