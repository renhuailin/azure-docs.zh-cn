---
title: 在 Azure Red Hat OpenShift 4 上创建 Azure 文件存储 StorageClass
description: 了解如何在 Azure Red Hat OpenShift 上创建 Azure 文件存储 StorageClass
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: aro, openshift, az aro, red hat, cli, azure file
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: e25a56b1d31ca9ef2c076ccc812b14043b798642
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112969583"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>在 Azure Red Hat OpenShift 4 上创建 Azure 文件存储 StorageClass

在本文中，你将为 Azure Red Hat OpenShift 4 创建一个 StorageClass，它使用 Azure 文件存储动态预配 ReadWriteMany (RWX) 存储。 你将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置先决条件并安装所需的工具
> * 使用 Azure 文件配置程序创建 Azure Red Hat OpenShift 4 StorageClass

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="before-you-begin"></a>准备阶段

将 Azure Red Hat OpenShift 4 群集部署到订阅中，请参阅[创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)


### <a name="set-up-azure-storage-account"></a>设置 Azure 存储帐户

此步骤将在 Azure Red Hat OpenShift (ARO) 群集的资源组之外创建一个资源组。 此资源组将包含由 Azure Red Hat OpenShift 的动态配置程序创建的 Azure 文件存储共享。

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>设置权限
### <a name="set-resource-group-permissions"></a>设置资源组权限

ARO 服务主体需要对新的 Azure 存储帐户资源组具有“listKeys”权限。 分配“参与者”角色来实现此目的。

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER --query servicePrincipalProfile.clientId -o tsv)

az role assignment create --role Contributor --assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>设置 ARO 群集权限

OpenShift 永久性卷绑定程序服务帐户需要能够读取机密。 创建并分配 OpenShift 群集角色来实现此目的。
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $ARO_API_SERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>使用 Azure 文件存储配置程序来创建 StorageClass

此步骤将使用 Azure 文件存储配置程序来创建 StorageClass。 在 StorageClass 清单中，需要提供存储帐户的详细信息，以便 ARO 群集知道要查看当前资源组之外的存储帐户。

在存储预配期间，会为装载凭据创建一个由 secretName 命名的机密。 在多租户上下文中，强烈建议显式设置 secretNamespace 的值，否则其他用户可能会读取存储帐户凭据。

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  secretNamespace: kube-system
  skuName: Standard_LRS
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>更改默认 StorageClass（可选）

ARO 上的默认 StorageClass 称为 managed-premium，并使用 azure-disk 配置程序。 通过针对 StorageClass 清单发出修补命令来对此进行更改。

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>验证 Azure 文件 StorageClass（可选）

创建新的应用程序并将向其分配存储。

> [!NOTE]
> 若要使用 `httpd-example` 模板，必须在启用“拉取机密”的情况下部署 ARO 群集。 有关详细信息，请参阅[获取 Red Hat 拉取机密](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional)。

```bash
oc new-project azfiletest
oc new-app httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

#If you have set the storage class by default, you can omit the --claim-class parameter
oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data --claim-class='azure-file'

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
还可以通过 Azure 门户中的存储资源管理器显示 test.txt 文件。

## <a name="next-steps"></a>后续步骤

在本文中，你使用 Microsoft Azure 文件存储和 Azure Red Hat OpenShift 4 创建了动态持久化存储。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 创建存储帐户
> * 使用 Azure 文件存储配置程序在 Azure Red Hat OpenShift 4 群集上配置 StorageClass

转到下一篇文章以了解 Azure Red Hat OpenShift 4 支持的资源。

* [Azure Red Hat OpenShift 支持策略](support-policies-v4.md)
