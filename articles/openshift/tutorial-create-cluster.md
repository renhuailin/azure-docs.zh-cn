---
title: 教程 - 创建 Azure Red Hat OpenShift 4 群集
description: 了解如何使用 Azure CLI 创建 Microsoft Azure Red Hat OpenShift 群集
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 10/26/2020
ms.openlocfilehash: 707ee38ef71a0f3f3f09e142bd4d944bb04cff3d
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446125"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>教程：创建 Azure Red Hat OpenShift 4 群集

在本教程（三个教程的第一部分）中，你将准备好环境以创建一个运行 OpenShift 4 的 Azure Red Hat OpenShift 群集，并创建一个群集。 将了解如何执行以下操作：
> [!div class="checklist"]
> * 设置先决条件 
> * 创建所需的虚拟网络和子网
> * 部署群集

## <a name="before-you-begin"></a>开始之前

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

Azure Red Hat OpenShift 至少需要 40 个核心才能创建和运行 OpenShift 群集。 新 Azure 订阅的默认 Azure 资源配额不满足此要求。 若要请求提高资源上限，请参阅[标准配额：按 VM 系列提高上限](../azure-portal/supportability/per-vm-quota-requests.md)中所述。

* 例如，若要查看最小的受支持虚拟机系列 SKU“标准 DSv3”的当前订阅配额，请运行以下代码：

    ```azurecli-interactive
    LOCATION=eastus
    az vm list-usage -l $LOCATION \
    --query "[?contains(name.value, 'standardDSv3Family')]" \
    -o table
    ```

ARO 拉取机密不会改变 ARO 的 RH OpenShift 许可证的成本。

### <a name="verify-your-permissions"></a>验证你的权限

在本教程中，你将创建一个资源组，该资源组将包含群集的虚拟网络。 你必须直接在虚拟网络或包含该网络的资源组或订阅上具有“参与者”和“用户访问管理员”权限或“所有者”权限。

还需要具有足够的 Azure Active Directory 权限（租户的成员用户或分配有角色“应用程序管理员”的来宾用户），以便工具代表你为群集创建应用程序和服务主体。 有关详细信息，请参阅[成员和来宾用户](/azure/active-directory/fundamentals/users-default-permissions#member-and-guest-users)以及[为具有 Azure Active Directory 的用户分配管理员和非管理员角色](/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

### <a name="register-the-resource-providers"></a>注册资源提供程序

1. 如果有多个 Azure 订阅，请指定相关订阅 ID：

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. 注册 `Microsoft.RedHatOpenShift` 资源提供程序：

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```
    
1. 注册 `Microsoft.Compute` 资源提供程序：

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```
    
1. 注册 `Microsoft.Storage` 资源提供程序：

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```
    
1. 注册 `Microsoft.Authorization` 资源提供程序：

    ```azurecli-interactive
    az provider register -n Microsoft.Authorization --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>获取 Red Hat 拉取机密（可选）

Red Hat 拉取机密使群集能够访问 Red Hat 容器注册表以及其他内容。 此步骤是可选的，但建议执行。

1. [导航到 Red Hat OpenShift 群集管理器门户](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)并登录。

   你将需要使用你的企业电子邮件登录 Red Hat 帐户或创建一个新的 Red Hat 帐户，并接受条款和条件。

1. 单击“下载拉取机密”，下载要用于 ARO 群集的拉取机密。

    将已保存的 `pull-secret.txt` 文件保存在安全的位置。 如果需要创建包含 Red Hat 或认证合作伙伴的示例或运算符的群集，则该文件将用于每个群集创建。

    运行 `az aro create` 命令时，可以使用 `--pull-secret @pull-secret.txt` 参数引用拉取机密。 从存储 `pull-secret.txt` 文件的目录执行 `az aro create`。 否则，将 `@pull-secret.txt` 替换为 `@/path/to/my/pull-secret.txt`。

    如果要在其他脚本中复制拉取机密或引用它，则应该将拉取机密格式设置为有效的 JSON 字符串。

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>为群集准备自定义域（可选）

运行 `az aro create` 命令时，可以使用 `--domain foo.example.com` 参数为群集指定自定义域。

如果为群集提供自定义域，请注意以下几点：

* 创建群集后，必须在 DNS 服务器中为指定的 `--domain` 创建 2 个 DNS A 记录：
    * **api** - 指向 API 服务器 IP 地址
    * **\*.apps** - 指向入口 IP 地址
    * 创建群集后，通过执行 `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'` 命令检索这些值。

* OpenShift 控制台将在 URL（如 `https://console-openshift-console.apps.example.com`）上可用，而在内置域 `https://console-openshift-console.apps.<random>.<location>.aroapp.io` 中不可用。

* 默认情况下，OpenShift 对自定义域 `*.apps.example.com` 上创建的所有路由使用自签名证书。  如果在连接到群集后选择使用自定义 DNS，则需按照 OpenShift 文档[为入口控制器配置自定义 CA](https://docs.openshift.com/container-platform/4.6/security/certificates/replacing-default-ingress-certificate.html)，并[为 API 服务器配置自定义 CA](https://docs.openshift.com/container-platform/4.6/security/certificates/api-server.html)。

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>创建包含两个空子网的虚拟网络

接下来，你将创建一个包含两个空子网的虚拟网络。 如果现有虚拟网络满足你的需求，则可以跳过此步骤。

1. **在将执行 `az` 命令的 shell 环境中设置以下变量。**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

2. **创建资源组。**

   Azure 资源组是一个逻辑组，用于部署和管理 Azure 资源。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。
    
   > [!NOTE] 
   > Azure Red Hat OpenShift 并非在可以创建 Azure 资源组的所有区域中可用。 有关支持 Azure Red Hat OpenShift 的位置的信息，请参阅[可用区域](https://azure.microsoft.com/global-infrastructure/services/?products=openshift)。

   ```azurecli-interactive
   az group create \
     --name $RESOURCEGROUP \
     --location $LOCATION
   ```

   以下示例输出显示已成功创建资源组：

   ```json
   {
     "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
     "location": "eastus",
     "name": "aro-rg",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "type": "Microsoft.Resources/resourceGroups"
   }
   ```

2. **创建虚拟网络。**

   运行 OpenShift 4 的 Azure Red Hat OpenShift 群集需要一个包含两个空子网（用于主节点和工作器节点）的虚拟网络。 可以为此创建新的虚拟网络，也可以使用现有虚拟网络。

   在之前创建的同一资源组中创建新的虚拟网络：

   ```azurecli-interactive
   az network vnet create \
      --resource-group $RESOURCEGROUP \
      --name aro-vnet \
      --address-prefixes 10.0.0.0/22
   ```

   以下示例输出显示已成功创建了虚拟网络：

   ```json
   {
     "newVNet": {
       "addressSpace": {
         "addressPrefixes": [
           "10.0.0.0/22"
         ]
       },
       "dhcpOptions": {
         "dnsServers": []
       },
       "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
       "location": "eastus",
       "name": "aro-vnet",
       "provisioningState": "Succeeded",
       "resourceGroup": "aro-rg",
       "type": "Microsoft.Network/virtualNetworks"
     }
   }
   ```

3. 为主节点添加一个空子网。

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name master-subnet \
     --address-prefixes 10.0.0.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

4. 为工作器节点添加一个空子网。

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name worker-subnet \
     --address-prefixes 10.0.2.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

5. 在主子网上[禁用子网专用终结点策略](../private-link/disable-private-link-service-network-policy.md)。 为了服务能够连接到群集并管理群集，必须执行此操作。

   ```azurecli-interactive
   az network vnet subnet update \
     --name master-subnet \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --disable-private-link-service-network-policies true
   ```

## <a name="create-the-cluster"></a>创建群集

运行以下命令以创建群集。 如果选择使用以下任一选项，请相应地修改命令：
* （可选）可以[传递 Red Hat 拉取机密](#get-a-red-hat-pull-secret-optional)，该机密使群集能够访问 Red Hat 容器注册表以及其他内容。 将 `--pull-secret @pull-secret.txt` 参数添加到命令中。
* 或者，也可选择[使用自定义域](#prepare-a-custom-domain-for-your-cluster-optional)。 将 `--domain foo.example.com` 参数添加到命令中，将 `foo.example.com` 替换为你自己的自定义域。

> [!NOTE]
> 如果要将任何可选参数添加到命令中，请确保对命令前一行的参数使用尾随反斜杠。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

执行 `az aro create` 命令之后，创建群集通常需要大约 35 分钟。

## <a name="next-steps"></a>后续步骤

本教程的此部分介绍了如何：
> [!div class="checklist"]
> * 安装必备组件并创建所需的虚拟网络和子网
> * 部署群集

转到下一教程：
> [!div class="nextstepaction"]
> [连接到 Azure Red Hat OpenShift 群集](tutorial-connect-cluster.md)
