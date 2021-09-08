

## Pod identity

### AKS Set up Command
- `$ az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService`

Install the aks-preview extension
- `$ az extension add --name aks-preview`
- `$ az extension update --name aks-preview`

#### Create
- Add `--enable-pod-identity` in argument
- e.g `$ az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure`

#### Update
- `$ az aks update -g $RESOURCE_GROUP -n $AKS_CLUSTER_NAME --enable-pod-identity`



### Identity
```
az group create --name myIdentityResourceGroup --location $REGION_NAME
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

### Grant Permission
```
NODE_GROUP=$(az aks show -g $RESOURCE_GROUP -n $AKS_CLUSTER_NAME --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Virtual Machine Contributor" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="ratingsapp"
az aks pod-identity add --resource-group $RESOURCE_GROUP --cluster-name $AKS_CLUSTER_NAME --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

### Demo
- `$ kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE`
- ˋ$ kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACEˋ


## Storage Account

### Create Storage Account
```
az storage account create \
    --name storageaksworksop6209 \
    --resource-group $RESOURCE_GROUP \
    --location $REGION_NAME \
    --sku Standard_ZRS \
    --encryption-services blob

az storage account show --resource-group $RESOURCE_GROUP --name storageaksworksop6209 
```

### Create Container of Storage Account
```
az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope "/subscriptions/d8fbfe10-96dd-416a-9181-31bb408ce301/resourceGroups/aksworkshop/providers/Microsoft.Storage/storageAccounts/storageaksworksop6209"

az role assignment create --role "Storage Blob Data Contributor" --assignee "$IDENTITY_CLIENT_ID" --scope "/subscriptions/d8fbfe10-96dd-416a-9181-31bb408ce301/resourceGroups/aksworkshop/providers/Microsoft.Storage/storageAccounts/storageaksworksop6209"

az storage container create \
    --account-name storageaksworksop6209 \
    --name data0001 \
    --auth-mode login

```
- check portal
- https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/resource/subscriptions/d8fbfe10-96dd-416a-9181-31bb408ce301/resourceGroups/aksworkshop/providers/Microsoft.Storage/storageAccounts/storageaksworksop6209/containersList

#### Notice : "Azure role assignments may take a few minutes to propagate."

### azcopy
- ` $ kubectl apply -f azcopy.yaml -n ratingsapp `
- ˋ $ kubectl cp a-lot-of-yamls.jpg azcopy:/home -n ratingsapp ˋ
- ˋ $ kubectl exec -it azcopy -n ratingsapp -- bash ˋ

#### In Cotainer
- ` $ cd home `
- ` $ azcopy login --identity`
- ` $ azcopy list https://storageaksworksop6209.blob.core.windows.net/data0001`
- ` $ azcopy copy a-lot-of-yamls.jpg https://storageaksworksop6209.blob.core.windows.net/data0001`
- ` $ azcopy sync https://storageaksworksop6209.blob.core.windows.net/data0001`.





## Ref
- https://docs.microsoft.com/en-us/azure/aks/operator-best-practices-identity
- https://docs.microsoft.com/en-us/azure/aks/use-azure-ad-pod-identity
- https://docs.microsoft.com/en-us/learn/modules/copy-blobs-from-command-line-and-code/
- https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/
- https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli
- https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10a