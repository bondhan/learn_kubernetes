# Learn Kubernetes

### configure minikube

set our cluster to 4 cpus and 16Gb memory RAM

```bash
minikube config set memory 16384
minikube config set cpus 4
minikube start
minikube addons enable default-storageclass 
minikube addons enable storage-provisioner
````

### get minikube ip

```bash
minikube ip
```
we got minikube_ip to accessing services in the cluster (NodePort)

##### get your services up an running, below just examples

```bash
kubectl apply -f services/storage.yaml
kubectl apply -f services/mongo-stack.yaml
kubectl apply -f services/workloads.yaml
kubectl apply -f services/services.yaml
```

### Install helm in ubuntu based

```bash
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
````

### Configure fluentd for log harvesting

```bash
kubectl apply -f fluentd/fluentd-rbac.yaml
kubectl apply -f fluentd/configMap.yaml
kubectl apply -f fluentd/fluentd-daemonset.yaml
```

### Install elastic search

```bash
helm install elasticsearch elastic/elasticsearch  -f elastic/values.yaml --namespace=kube-system   --wait
kubectl delete service/elasticsearch-master -n kube-system
kubectl apply -f elastic/elastic.yaml
```
you could access elasticsearch from `$minikube_ip:30920`


### Install kibana

```bash
helm install kibana elastic/kibana --namespace=kube-system --wait
kubectl delete svc kibana-kibana -n kube-system
kubectl apply -f elastic/kibana.yaml
```
you could access kibana from `$minikube_ip:30560`. You should see the logs of your services (use filters, etc)

#### Notes

you could do port forwarding instead of changing from ClusterIP to NodePort

```bash
kubectl port-forward deployment/kibana-kibana 5601 --namespace=kube-system
kubectl port-forward service/elasticsearch-master 9200  --namespace=kube-system
```

### Minikube stop & delete

if you want to release resource for another time

```bash
minikube stop
```

sayonara
```bash
minikube delete
```