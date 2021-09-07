
## Deploy Application

### Mongo
```
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install ratings bitnami/mongodb \
    --namespace ratingsapp \
    --set auth.username=mongouser,auth.password=yvLurJE7jsfgQBGq,auth.database=ratingsdb

kubectl create secret generic mongosecret \
    --namespace ratingsapp \
    --from-literal=MONGOCONNECTION="mongodb://<username>:<password>@ratings-mongodb.ratingsapp:27017/ratingsdb"
```
- `ratings-mongodb.ratingsapp.svc.cluster.local`


### Secret & ConfigMap
```
kubectl create secret generic mongosecret \
    --namespace ratingsapp \
    --from-literal=MONGOCONNECTION="mongodb://mongouser:yvLurJE7jsfgQBGq@ratings-mongodb.ratingsapp:27017/ratingsdb"
kubectl describe secret mongosecret --namespace ratingsapp
```


### API
- `$ kubectl apply --namespace ratingsapp -f ratings-api-deployment.yaml`

### `$ kubectl apply --namespace ratingsapp -f ratings-api-service.yaml`