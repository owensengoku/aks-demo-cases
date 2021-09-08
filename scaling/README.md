
## Scaling

### Pod Scaling

#### YAML
```
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: ratings-api
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: ratings-api
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 30
```


#### Command
-` $ kubectl apply --namespace ratingsapp -f ratings-api-hpa.yaml `
- Notice:"For the horizontal pod autoscaler to work, you must remove any explicit replica count from your ratings-api deployment. Keep in mind that you need to redeploy your deployment when you make any changes."

#### Trigger Scaling
- attacker
- ` $ kubectl apply --namespace ratingsapp -f attacker.yaml `
- `$ kubectl get hpa --namespace ratingsapp -w`


### Work Node Scaler

#### Command
```
az aks update \
--resource-group $RESOURCE_GROUP \
--name $AKS_CLUSTER_NAME  \
--enable-cluster-autoscaler \
--min-count 1 \
--max-count 5
```


## Ref
https://docs.microsoft.com/en-us/learn/modules/aks-workshop/10-scale-application



## Special Note: KEDA
https://keda.sh/

https://docs.microsoft.com/en-us/learn/modules/aks-app-scale-keda/
