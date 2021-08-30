---
title: Azure 虚拟网络中的 Cloud Shell
description: 将 Cloud Shell 部署到 Azure 虚拟网络中
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2ce108b7f36b7f7d4caf5133aafe99eb837df1ce
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742597"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>将 Cloud Shell 部署到 Azure 虚拟网络中

常规 Cloud Shell 会话在 Microsoft 网络中独立于你的资源的容器中运行。 这意味着，在该容器内运行的命令无法访问只能从特定虚拟网络访问的资源。 例如，无法使用 SSH 从 Cloud Shell 连接到只有专用 IP 地址的虚拟机，也无法使用 kubectl 连接到锁定了访问权限的 Kubernetes 群集。 

此可选功能解决了这些限制，允许你将 Cloud Shell 部署到所控制的 Azure 虚拟网络中。 从那里，容器能够与你选择的虚拟网络中的资源进行交互。  

下面，你可以看到将在此场景中部署和使用的资源体系结构。

![展示 Cloud Shell 隔离的 VNET 体系结构。](media/private-vnet/data-diagram.png)

在你自己的 Azure 虚拟网络中使用 Cloud Shell 之前，你需要创建多个资源来支持此功能。 本文介绍了如何使用 ARM 模板设置所需的资源。

> [!NOTE]
> 对于虚拟网络来说，这些资源只需要设置一次。 然后，这些资源就可以供有权访问虚拟网络的所有管理员共享。

## <a name="required-network-resources"></a>所需的网络资源

### <a name="virtual-network"></a>虚拟网络
虚拟网络定义在其中创建一个或多个子网的地址空间。

需要标识将要用于 Cloud Shell 的所需虚拟网络。 这通常是一个现有的虚拟网络，其中包含你要管理的资源，或包含与你的资源所在的网络对等互连的网络。

### <a name="subnet"></a>子网
在选定的虚拟网络中，必须为 Cloud Shell 容器使用专用子网。 此子网委托给 Azure 容器实例 (ACI) 服务。  当用户请求虚拟网络中的 Cloud Shell 容器时，Cloud Shell 使用 ACI 来创建此委托子网中的容器。  不能在此子网中创建其他资源。

### <a name="network-profile"></a>网络配置文件
网络配置文件是 Azure 资源的网络配置模板，用于指定资源的某些网络属性。

### <a name="azure-relay"></a>Azure 中继
[Azure 中继](../azure-relay/relay-what-is-it.md)允许无法直接联系的两个终结点进行通信。 在这种情况下，使用它就可以让管理员的浏览器与专用网络中的容器通信。

可以配置用于 Cloud Shell 的 Azure 中继实例，以便对哪些网络可以访问容器资源进行控制： 
- 可从公共 Internet 访问：在此配置中，Cloud Shell 提供了一种方法，允许通过其他方式从外部访问内部资源。 
- 可从指定的网络访问：在此配置中，管理员必须从在合适网络中运行的计算机访问 Azure 门户，才能使用 Cloud Shell。

## <a name="storage-requirements"></a>存储要求
与在标准 Cloud Shell 中一样，在虚拟网络中使用 Cloud Shell 时需要一个存储帐户。 每个管理员都需要一个文件共享来存储其文件。  需要能够从 Cloud Shell 使用的虚拟网络中访问该存储帐户。 

## <a name="virtual-network-deployment-limitations"></a>虚拟网络部署限制
* 由于涉及到额外的网络资源，在虚拟网络中启动 Cloud Shell 通常比标准 Cloud Shell 会话要慢。

* 当前支持除印度中部之外的所有 Cloud Shell 主要区域。 

* [Azure 中继](../azure-relay/relay-what-is-it.md)不是免费服务，请查看其[定价](https://azure.microsoft.com/pricing/details/service-bus/)。 在 Cloud Shell 方案中，每个管理员在使用 Cloud Shell 时都将使用一个混合连接。 Cloud Shell 会话完成后，该连接会自动关闭。

## <a name="register-the-resource-provider"></a>注册资源提供程序

需要在包含你要使用的虚拟网络的订阅中注册 Microsoft.ContainerInstances 资源提供程序。 通过 `Set-AzContext -Subscription {subscriptionName}` 选择合适的订阅，然后运行以下代码：

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

如果 RegistrationState 为 `Registered`，则无需执行任何操作。 如果为 `NotRegistered`，请运行 `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance`。 

## <a name="deploy-network-resources"></a>部署网络资源
 
### <a name="create-a-resource-group-and-virtual-network"></a>创建资源组和虚拟网络
如果你已有要连接到的所需 VNET，请跳过此部分。

通过 Azure 门户、Azure CLI、Azure PowerShell 等方式创建一个资源组，并在新资源组中创建一个虚拟网络，资源组和虚拟网络必须位于同一区域。

### <a name="arm-templates"></a>ARM 模板
使用 [Azure 快速入门模板](https://aka.ms/cloudshell/docs/vnet/template)在虚拟网络中创建 Cloud Shell 资源，并使用 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/cloud-shell-vnet-storage/)创建必要的存储。 记下你的资源名称，主要是文件共享名称。

### <a name="open-relay-firewall"></a>打开中继防火墙
导航到使用上述模板创建的中继，在“设置”中选择“网络”，允许从浏览器网络访问中继。 默认情况下，只能从已在其中创建中继的虚拟网络访问中继。 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>配置 Cloud Shell 以使用虚拟网络。
> [!NOTE]
> 每个将使用 Cloud Shell 的管理员都必须完成此步骤。

部署完成上述步骤后，请在 Azure 门户中或 https://shell.azure.com 上导航到 Cloud Shell。 每次需要连接到隔离的 Cloud Shell 体验时，都必须使用这些体验之一。

> [!NOTE]
> 如果已使用过 Cloud Shell，则必须卸载现有 clouddrive。 若要执行此操作，请从活动 Cloud Shell 会话运行 `clouddrive unmount`，然后刷新页面。

连接到 Cloud Shell，你的首次运行体验会收到系统提示。 依次选择你的首选 shell 体验、“显示高级设置”、“显示 VNET 隔离设置”框。 填充弹出窗口中的字段。  大多数字段会自动填充到可与虚拟网络中的 Cloud Shell 关联的可用资源。  用户必须填充文件共享名。


![展示 Cloud Shell 隔离 VNET 首次体验设置。](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>后续步骤
[了解 Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)
