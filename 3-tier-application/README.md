
## Deploy Application

### Mongo
```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install ratings bitnami/mongodb \
    --namespace ratingsapp \
    --set auth.username=mongouser,auth.password=yvLurJE7jsfgQBGq,auth.database=ratingsdb

```
- `{service}.{namespace}.svc.cluster.local`
- `ratings-mongodb.ratingsapp.svc.cluster.local`


#### Secret & ConfigMap
```
kubectl create secret generic mongosecret \
    --namespace ratingsapp \
    --from-literal=MONGOCONNECTION="mongodb://mongouser:yvLurJE7jsfgQBGq@ratings-mongodb.ratingsapp:27017/ratingsdb"
kubectl describe secret mongosecret --namespace ratingsapp
```


### API
- `$ kubectl apply --namespace ratingsapp -f ratings-api-deployment.yaml`
- `$ kubectl apply --namespace ratingsapp -f ratings-api-service.yaml`


### Web
- `$ kubectl apply --namespace ratingsapp -f ratings-web-deployment.yaml`
- `$ kubectl apply --namespace ratingsapp -f ratings-web-service.yaml`



## Ref
https://docs.microsoft.com/en-us/learn/modules/aks-workshop/04-deploy-mongodb
https://docs.microsoft.com/en-us/learn/modules/aks-workshop/05-deploy-ratings-api
https://docs.microsoft.com/en-us/learn/modules/aks-workshop/06-deploy-ratings-web

