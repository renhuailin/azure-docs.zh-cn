---
title: '使用客户管理的密钥加密永久性卷声明 (Azure Red Hat OpenShift 上的 CMK)  (ARO) '
description: 自带密钥 (BYOK) /客户托管密钥 (CMK) 部署 Azure Red Hat OpenShift 说明
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: encryption、byok、aro、openshift、red hat
ms.openlocfilehash: 09e1f92a967c7b77d3bb8e27769f4cafd4fd4f53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054518"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>使用客户管理的密钥加密永久性卷声明 (Azure Red Hat OpenShift 上的 CMK)  (ARO)  (预览版) 

Azure 存储对静态存储帐户中的所有数据进行加密。 默认情况下，使用 Microsoft 平台托管的密钥（包括 OS 和数据磁盘）对数据进行加密。 为了更好地控制加密密钥，你可以提供客户管理的密钥来加密 Azure Red Hat OpenShift 群集中的数据。

> [!NOTE]
> 在此阶段，仅支持对包含客户托管密钥的 ARO 永久性卷进行加密。 此功能目前不适用于操作系统磁盘。

> [!IMPORTANT]
> ARO 预览功能适用于自助服务，可选择使用。 预览按 "原样" 提供，并从服务级别协议和有限担保中排除。 ARO 预览版是一项最大努力的客户支持部分。 因此，这些功能并不用于生产。

## <a name="before-you-begin"></a>准备阶段
本文假设读者：

* OpenShift 版本4.4 具有预先存在的 ARO 群集 (或更高版本的) 。

* 你拥有 "oc" OpenShift 命令行工具，base64 (核心 utils) 的一部分，并且安装了 "az" Azure CLI。

* 你使用 *oc* 作为全局群集-管理员用户 (kubeadmin) 登录到了 ARO 群集。

* 你使用 *az* 登录到 Azure CLI，该帐户有权在与 ARO 群集相同的订阅中授予 "参与者" 访问权限。

## <a name="limitations"></a>限制

* 客户管理的密钥加密的可用性是特定于区域的。 若要查看特定 Azure 区域的状态，请查看 [azure 区域][supported-regions]。
* 如果使用的是 Ultra 磁盘，请在开始之前在订阅上启用 Ultra 磁盘。

## <a name="create-an-azure-key-vault-instance"></a>创建 Azure Key Vault 实例
必须使用 Azure Key Vault 实例来存储密钥。 创建一个启用了清除保护并启用了软删除的新 Key Vault。 然后，在保管库中创建一个新密钥来存储您自己的自定义密钥：

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table
# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>创建 Azure 磁盘加密集

Azure 磁盘加密集用作 ARO 中磁盘的参考点。 它连接到我们在上一步中创建的 Azure Key Vault，将从该位置拉取客户管理的密钥。

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $myRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>授予磁盘加密设置对 Key Vault 的访问权限
使用我们在前面的步骤中创建的磁盘加密集，并授予对 Azure Key Vault 的磁盘加密设置访问权限：

```azurecli-interactive
# First, find the disk encryption set's AppId value.
desIdentity="$(az disk-encryption-set show -n $desName -g $myRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $myRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the disk encryption set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>获取角色分配所需的其他 Id
我们需要允许 ARO 群集使用磁盘加密集来加密在 ARO 群集中 (Pvc) 的永久性卷声明。 为此，我们将创建一个新的托管服务标识 (MSI) 。 我们还将为 ARO MSI 和磁盘加密集设置其他权限。
```
# First, get the application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $myRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity application ID.
aroMSIAppId="$(az identity show -n $msiName -g $myRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
myRGResourceId="$(az group show -n $myRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-byokcmk-encryption"></a>实现 BYOK/CMK 加密所需的其他角色分配
使用在上一步中获取的变量应用所需的角色分配：

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $myRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $myRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks-optional"></a> (可选) 为加密的高级 & Ultra 磁盘创建 k8s 存储类
为 Premium_LRS 和 UltraSSD_LRS 磁盘生成用于 BYOK/CMK 的存储类：
```
# Premium Disks
cat > managed-premium-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```
### <a name="set-up-your-storage-class-configuration"></a>设置存储类配置
将 ARO 群集独有的变量替换为两个存储类配置文件：
```
# Insert your current active subscription ID into the configuration
sed -i "s/subId/$subId/g" managed-premium-encrypted-byok.yaml
sed -i "s/subId/$subId/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the Resource Group which contains the disk encryption set and Key Vault
sed -i "s/myRG/$myRG/g" managed-premium-encrypted-byok.yaml
sed -i "s/myRG/$myRG/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the disk encryption set
sed -i "s/desName/$desName/g" managed-premium-encrypted-byok.yaml
sed -i "s/desName/$desName/g" managed-ultra-encrypted-byok.yaml
```
接下来，在 ARO 群集中运行此部署，以应用存储类配置：
```
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-byok.yaml
oc apply -f managed-ultra-encrypted-byok.yaml
```
## <a name="test-encryption-with-customer-managed-keys"></a>通过客户托管的密钥测试加密
若要检查群集是否正在使用客户托管的密钥进行 PVC 加密，我们将使用适当的存储类创建永久性卷声明。 以下代码片段创建一个 pod，并使用标准磁盘装载持久卷声明
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-byok-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-byok
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-byok-encryption
spec:
  containers:
  - name: mypod-with-byok-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-byok-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file"></a>应用测试 pod 配置文件
执行以下命令以应用测试 Pod 配置，并返回新的永久性卷声明的 UID。 UID 将用于验证磁盘是否已使用 BYOK/CMK 进行加密。
```
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o json | jq -r '.items[0].metadata.uid')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o json |jq -r '.spec.azureDisk.diskName')"
```
### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey"></a>验证 PVC 磁盘是否配置了 "EncryptionAtRestWithCustomerKey" 
Pod 应创建引用 BYOK/CMK 存储类的永久性卷声明。 运行以下命令将验证是否按预期部署了 PVC：
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $myRG -o json --query [encryption]
```

## <a name="next-steps"></a>后续步骤

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

