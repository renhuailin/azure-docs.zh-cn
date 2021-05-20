---
title: 在 Azure Red Hat OpenShift 4 群集上添加或更新 Red Hat 拉取机密
description: 在现有的 4.x ARO 群集上添加或更新 Red Hat 拉取机密
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/21/2020
keywords: pull secret, aro, openshift, red hat
ms.openlocfilehash: 58c0eb2be3423783a69d005277ffe75aaf59415f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633727"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>在 Azure Red Hat OpenShift 4 群集上添加或更新 Red Hat 拉取机密

本指南介绍如何为现有 Azure Red Hat OpenShift (ARO) 4.x 群集添加或更新 Red Hat 拉取机密。

如果是首次创建群集，可以在创建群集时添加拉取机密。 有关使用 Red Hat 拉取机密创建 ARO 群集的详细信息，请参阅[创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional)。

## <a name="before-you-begin"></a>开始之前

本指南假定你有一个现有的 Azure Red Hat OpenShift 4 群集。 确保对群集具有管理员访问权限。

## <a name="prepare-your-pull-secret"></a>准备拉取机密
如果在创建 ARO 群集时未添加 Red Hat 拉取机密，系统会在群集上自动创建拉取机密。 但是此拉取机密未完整填充。

本部分逐步介绍如何使用 Red Hat 拉取机密中的其他值更新该拉取机密。

1. 运行以下命令，提取 `openshift-config` 命名空间中名为 `pull-secret` 的机密，并将其保存到单独的文件中： 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    输出结果应类似于以下所示： （请注意，实际的机密值已删除。）

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. 转到 [Red Hat OpenShift 群集管理员门户](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)，然后选择“下载拉取机密”。 Red Hat 拉取机密将类似于以下所示。 （请注意，实际的机密值已删除。）

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. 通过添加 Red Hat 拉取机密中的条目，编辑从群集获取的拉取机密文件。 

    > [!IMPORTANT]
    > 包含 Red Hat 拉取机密中的 `cloud.openshift.com` 条目会使群集开始向 Red Hat 发送遥测数据。 仅在要发送遥测数据时包含此部分。 否则请勿包含以下部分。    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > 请勿删除或更改拉取机密中的 `arosvc.azurecr.io` 条目。 此部分是群集正常运行所必需的。

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    最终文件应类似于以下所示。 （请注意，实际的机密值已删除。）

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. 确保文件是有效的 JSON。 有很多方法可以验证 JSON。 下面的示例使用 jq：

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > 如果文件中有错误，会显示 `parse error`。

## <a name="add-your-pull-secret-to-your-cluster"></a>将拉取机密添加到群集中

运行以下命令来更新拉取密钥。

> [!NOTE]
> 运行此命令会导致群集节点在更新时逐一重启。 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

设置机密后，即可启用经过 Red Hat 认证的操作员。

### <a name="modify-the-configuration-files"></a>修改配置文件

修改下列对象以启用 Red Hat 操作员。

首先，修改“示例操作员”配置文件。 然后可以运行以下命令来编辑配置文件：

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

将 `spec.architectures.managementState` 和 `status.architecture.managementState` 值从 `Removed` 更改为 `Managed`。 

以下 YAML 代码片段只显示经过编辑的 YAML 文件的相关部分：

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

然后，运行以下命令来编辑“操作员中心”配置文件：  

```console
oc edit operatorhub cluster -o yaml
```

将要启用的任何源的 `Spec.Sources.Disabled` 和 `Status.Sources.Disabled` 值从 `true` 更改为 `false`。

以下 YAML 代码片段只显示经过编辑的 YAML 文件的相关部分：

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

保存文件以应用所做的编辑。

## <a name="validate-that-your-secret-is-working"></a>验证机密是否有效

添加拉取机密并修改正确的配置文件后，群集可能会花几分钟来进行更新。 若要检查群集是否已更新，可运行以下命令来显示可用的“认证操作员”和“Red Hat 操作员”源：

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

如果未显示“认证操作员”和“Red Hat 操作员”，请等待几分钟，然后重试。

若要确保拉取机密已更新且正常运行，可打开 OperatorHub 并检查是否有经过 Red Hat 验证的操作员。 例如，检查 OpenShift 容器存储操作员是否可用，并查看是否具有安装权限。

## <a name="next-steps"></a>后续步骤
若要详细了解 Red Hat 拉取机密，请参阅[使用映像拉取机密](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html)。

若要详细了解 Red Hat OpenShift 4，请参阅 [Red Hat OpenShift 容器平台文档](https://docs.openshift.com/container-platform/4.6/welcome/index.html)。
