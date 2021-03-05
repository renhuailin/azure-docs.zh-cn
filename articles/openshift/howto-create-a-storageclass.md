---
title: 在 Azure Red Hat OpenShift 4 上创建 Azure 文件存储 StorageClass
description: 了解如何在 Azure Red Hat OpenShift 上创建 Azure 文件 StorageClass
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: aro，openshift，az aro，red hat，cli，azure 文件
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 039aa3cce6615e71960db810ae383d22d7bcd909
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212971"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>在 Azure Red Hat OpenShift 4 上创建 Azure 文件存储 StorageClass

在本文中，你将创建一个 StorageClass for Azure Red Hat OpenShift 4，使用 Azure 文件动态预配 ReadWriteMany (RWX) 存储。 你将了解如何执行以下操作：

> [!div class="checklist"]
> * 安装必备组件并安装所需的工具
> * 使用 Azure File 配置程序创建 Azure Red Hat OpenShift 4 StorageClass

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="before-you-begin"></a>准备阶段

将 Azure Red Hat OpenShift 4 群集部署到订阅中，请参阅 [创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)


### <a name="set-up-azure-storage-account"></a>设置 Azure 存储帐户

此步骤将在 Azure Red Hat OpenShift (ARO) 群集资源组外创建资源组。 此资源组将包含由 Azure Red Hat OpenShift 的动态配置程序创建的 Azure 文件共享。

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

ARO 服务主体要求对新的 Azure 存储帐户资源组具有 "listKeys" 权限。 分配 "参与者" 角色以实现此目的。

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER --query servicePrincipalProfile.clientId -o tsv)

az role assignment create --role Contributor --assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>设置 ARO 群集权限

OpenShift 永久性卷联编程序服务帐户需要能够读取机密。 创建并分配 OpenShift 群集角色以实现此目的。
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>创建 StorageClass 与 Azure 文件配置程序

此步骤将创建 StorageClass，其中包含 Azure 文件配置程序。 在 StorageClass 清单中，需要提供存储帐户的详细信息，以便 ARO 群集知道要查看当前资源组之外的存储帐户。

在存储预配期间，会为安装凭据创建一个名为 secretName 的机密。 在多租户环境中，强烈建议显式设置 secretNamespace 的值，否则其他用户可能会读取存储帐户凭据。

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

## <a name="change-the-default-storageclass-optional"></a>更改默认的 StorageClass (可选) 

默认的 StorageClass on ARO 称为托管-premium，并使用 azure 磁盘配置程序。 通过对 StorageClass 清单发出修补程序命令对此进行更改。

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>验证 Azure 文件 StorageClass (可选) 

创建新的应用程序并将存储分配给它。

```bash
oc new-project azfiletest
oc new-app -template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
test.txt 文件也将通过 Azure 门户中的存储资源管理器可见。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Microsoft Azure 文件和 Azure Red Hat OpenShift 4 创建动态永久性存储。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 创建存储帐户
> * 使用 Azure Files 配置程序在 Azure Red Hat OpenShift 4 群集上配置 StorageClass

转到下一篇文章，了解 Azure Red Hat OpenShift 4 支持的资源。

* [Azure Red Hat OpenShift 支持策略](support-policies-v4.md)
