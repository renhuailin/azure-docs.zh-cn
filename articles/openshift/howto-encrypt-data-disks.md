---
title: 在 Azure Red Hat OpenShift (ARO) 上使用客户管理的密钥 (CMK) 加密永久性卷声明
description: Azure Red Hat OpenShift 的自带密钥 (BYOK)/客户管理的密钥 (CMK) 部署说明
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: 加密, byok, aro, cmk, openshift, red hat
ms.openlocfilehash: d5251188dfef87363846d4a19cd7ccc0d5d2976f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665105"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>在 Azure Red Hat OpenShift (ARO) 上使用客户管理的密钥 (CMK) 加密永久性卷声明（预览版）

在将数据保存到云时，Azure 存储会使用服务器端加密 (SSE) 自动对数据[加密](../storage/common/storage-service-encryption.md)。 默认情况下，使用 Microsoft 平台管理的密钥对数据加密。 为了进一步控制加密密钥，你可以提供你自己的客户管理的密钥，用于对 Azure Red Hat OpenShift 群集中的数据加密。

> [!NOTE]
> 在此阶段，仅支持使用客户管理的密钥对 ARO 永久性卷加密。 此功能目前不适用于主节点或工作器节点操作系统磁盘。

> [!IMPORTANT]
> ARO 预览版功能是可选择启用的自助功能。 预览版功能“一经发布”就会“按原样”提供，不包括在服务级别协议和有限保证中。 客户支持部门会尽力为 ARO 预览版功能提供部分支持。 因此，这些功能不应用于生产。

## <a name="before-you-begin"></a>准备阶段
本文假设读者：

* 在 OpenShift 4.4（或更高版本）中有预先存在的 ARO 群集。

* 安装了 **oc** OpenShift 命令行工具、base64（coreutils 的一部分）以及 **az** Azure CLI。

* 已使用 **oc** 作为全局群集管理员用户 (kubeadmin) 登录到 ARO 群集。

* 已通过一个帐户使用 **az** 登录到 Azure CLI，该帐户有权在 ARO 群集所属的订阅中授予“参与者”访问权限。

## <a name="limitations"></a>限制

* 客户管理的密钥的加密是否可用取决于区域。 若要了解特定 Azure 区域的状态，请查看 [Azure 区域][supported-regions]。
* 如果要使用 Ultra Disks，必须先在订阅上启用它们，然后才能开始使用。

## <a name="declare-cluster--encryption-variables"></a>声明群集和加密变量
可以对以下变量进行任意配置，只要其适合 ARO 群集即可，你要在该群集中启用客户管理的加密密钥：
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>获取订阅 ID
你的 Azure 订阅 ID 在 CMK 的配置中多次使用。 获取它并将它存储为变量：
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>创建 Azure Key Vault 实例
必须使用 Azure Key Vault 实例来存储密钥。 创建启用了清除保护功能的新 Key Vault。 然后，在保管库中创建一个新密钥来存储你自己的自定义密钥：

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
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>授予磁盘加密集对 Key Vault 的访问权限
使用我们在前面的步骤中创建的磁盘加密集，授予磁盘加密集对 Azure Key Vault 的访问权限：

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>获取进行角色分配所需的其他 ID
我们需要允许 ARO 群集使用磁盘加密集来加密 ARO 群集中的永久性卷声明 (PVC)。 为此，我们将创建一个新的托管服务标识 (MSI)。 我们还将为 ARO MSI 和磁盘加密集设置其他权限。

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(az aro show -n $aroCluster -g $buildRG -o tsv --query servicePrincipalProfile.clientId)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>实施 CMK 加密所需的其他角色分配
使用在上一步获取的变量应用所需的角色分配：

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>为加密的高级磁盘和超级磁盘创建 k8s 存储类
为 Premium_LRS 和 UltraSSD_LRS 磁盘生成将要用于 CMK 的存储类：

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

接下来，在 ARO 群集中运行此部署，以应用存储类配置：

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>使用客户管理的密钥来测试加密（可选）
为了检查群集是否在使用客户管理的密钥进行 PVC 加密，我们将使用新的存储类创建一个永久性卷声明。 以下代码片段创建一个 Pod，并使用高级磁盘装载永久性卷声明。
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>应用测试 Pod 配置文件（可选）
执行以下命令以应用测试 Pod 配置，并返回新的永久性卷声明的 UID。 UID 将用于验证磁盘是否已使用 CMK 进行加密。
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> 有时，在 Azure Active Directory 中应用角色分配时可能会稍有延迟。 用于“确定完整的 Azure 磁盘名称”的命令可能不会成功，具体取决于执行这些指令的速度。 如果出现这种情况，请查看 **oc describe pvc mypod-with-cmk-encryption-pvc** 的输出，确保已成功预配该磁盘。 如果角色分配传播尚未完成，则可能需要删除并重新应用 Pod 和 PVC YAML。

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>验证是否为 PVC 磁盘配置了“EncryptionAtRestWithCustomerKey”（可选）
Pod 应创建一个引用 CMK 存储类的永久性卷声明。 运行以下命令将验证是否按预期部署了 PVC：
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
