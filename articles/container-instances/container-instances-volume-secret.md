---
title: 将机密卷装载到容器组
description: 了解如何装载机密卷以存储供容器实例访问的敏感信息
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 63fb5ded667ce2d46d97ed51c6b16754c46f2a7b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676269"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>在 Azure 容器实例中装载机密卷

可以使用机密卷向容器组中的容器提供敏感信息。 机密卷将机密存储在该卷内的文件中，然后容器组中的容器可以访问这些机密。 将机密存储在机密卷中，可以避免将敏感数据（例如，SSH 密钥或数据库凭据）添加到应用程序代码中。

* 与机密一起部署到容器组中后，机密卷将为只读。
* 所有机密卷均由 RAM 支持的文件系统 [tmpfs][tmpfs] 提供支持；其内容永远不会写入非易失性存储。

> [!NOTE]
> 机密卷目前仅限于 Linux 容器。 [设置环境变量](container-instances-environment-variables.md)介绍了如何为 Windows 和 Linux 容器传递安全环境变量。 虽然我们正致力于为 Windows 容器提供全部功能，但你可在[概述](container-instances-overview.md#linux-and-windows-containers)中了解当前的平台差异。

## <a name="mount-secret-volume---azure-cli"></a>装载机密卷 - Azure CLI

若要使用 Azure CLI 部署包含一个或多个机密的容器，请在 [az container create][az-container-create] 命令中包含 `--secrets` 和 `--secrets-mount-path` 参数。 此示例在 `/mnt/secrets` 处装载一个机密卷，该卷由两个包含机密“mysecret1”和“mysecret2”的文件组成：

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

以下 [az container exec][az-container-exec] 输出演示在运行的容器中打开 shell，列出机密卷中的文件，然后显示其内容：

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>装载机密卷 - YAML

还可以使用 Azure CLI 和 [YAML 模板](container-instances-multi-container-yaml.md)部署容器组。 在部署由多个容器组成的容器组时，通过 YAML 模板进行部署是首选方法。

使用 YAML 模板进行部署时，模板中的机密值必须已进行 **Base64 编码**。 但是，机密值会以明文形式显示在容器的文件中。

以下 YAML 模板定义了一个容器组，其中包含一个容器，该容器在 `/mnt/secrets` 处装载了一个机密卷。 机密卷有两个包含机密“mysecret1”和“mysecret2”的文件。

```yaml
apiVersion: '2019-12-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

若要使用 YAML 模板进行部署，请将前面的 YAML 保存到名为 `deploy-aci.yaml` 的文件中，然后使用 `--file` 参数执行 [az container create][az-container-create] 命令：

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>装载机密卷 - 资源管理器

除了 CLI 和 YAML 部署外，还可以使用 Azure [资源管理器模板](/azure/templates/microsoft.containerinstance/containergroups)部署容器组。

首先，在模板的容器组 `properties` 节中填充 `volumes` 数组。 使用资源管理器模板进行部署时，模板中的机密值必须已进行 **Base64 编码**。 但是，机密值会以明文形式显示在容器的文件中。

接下来，针对容器组中希望装载 *机密* 卷的每个容器，在容器定义的 `properties` 节中填充 `volumeMounts` 数组。

以下资源管理器模板定义了一个容器组，其中包含一个容器，该容器在 `/mnt/secrets` 处装载了一个机密卷。 机密卷有两个机密：“mysecret1”和“mysecret2”。

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/resourcemanager-templates/container-instances/aci-deploy-volume-secret.json)]

若要使用资源管理器模板进行部署，请将前面的 JSON 保存到名为 `deploy-aci.json` 的文件中，然后使用 `--template-file` 参数执行 [az deployment group create][az-deployment-group-create] 命令：

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>后续步骤

### <a name="volumes"></a>卷

了解如何在 Azure 容器实例中装载其他卷类型：

* [在 Azure 容器实例中装载 Azure 文件共享](container-instances-volume-azure-files.md)
* [在 Azure 容器实例中装载 emptyDir 卷](container-instances-volume-emptydir.md)
* [在 Azure 容器实例中装载 gitRepo 卷](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>安全环境变量

向容器（包括 Windows 容器）提供敏感信息的另一种方法是通过使用[安全环境变量](container-instances-environment-variables.md#secure-values)。

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-exec]: /cli/azure/container#az_container_exec
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
