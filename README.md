# k8s-fluentd
Kubernetes logging mechanism using EFK (Elastic search, Kibana and Fluentd)

#### Prerequisites

- Docker setup: https://docs.docker.com/engine/install/
- Kubectl installation: https://kubernetes.io/docs/tasks/tools/
- helm setup  - https://helm.sh/docs/intro/install/

```
docker version

kubectl version

helm version

```

### Implementation

**Step 1: Create Namespace**

```
kubectl create namespace efk-monitoring

```
**Step 2: Add Elastic Helm Repository**

```
helm repo add elastic https://helm.elastic.co
helm repo update

```

**Step 4: Install Elasticsearch with Persistence Disabled**

If you want to disable persistence for Elasticsearch (Note: This is suitable for testing purposes only):

```
helm install elasticsearch elastic/elasticsearch --version 7.17.3 -n efk-monitoring --set persistence.enabled=false,replicas=1

```

**Step 5: Check the Installation**

```
helm list -n efk-monitoring
kubectl get pods -n efk-monitoring
kubectl get svc -n efk-monitoring

```

**Step 6: Install Kibana**

```
helm install kibana elastic/kibana --version 7.17.3 -n efk-monitoring
```

**Step 7: Verify Kibana Installation**

```
kubectl get pods -n efk-monitoring
```


**Step 8: Apply Fluentd Configuration**

Apply the Fluentd ConfigMap and DaemonSet YAML files:

```
kubectl apply -f ./fluentd-config-map.yaml
kubectl apply -f ./fluentd-rbac.yaml

```


**Step 9: Monitor Fluentd DaemonSet**

Monitor the Fluentd DaemonSet to ensure successful deployment:

```
kubectl get pods -n kube-system -w

```


**Step 12: Add nodeport instead of clusterIP**

To access Kibana web interface:

```
k edit svc -n efk-monitoring kibana-kibana


-- go to stack management , set the index and then go to discover 
```

**Step13: Deploy the sample application**

```
kubectl apply -f k8s-app.yml

kubectl get pods

kubectl get svc

kubectl logs <pod-name>

kubectl port-forward svc/myapp 8080

```


### Delete all the resources


```
kubectl delete -f k8s-app.yml

kubectl delete -f fluentd-config-map.yaml

kubectl delete -f fluentd-rbac.yaml

helm list -n efk-monitoring

helm uninstall elasticsearch -n efk-monitoring

helm uninstall kibana -n efk-monitoring

```



