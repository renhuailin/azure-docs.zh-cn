---
title: 排查错误：Azure Functions 运行时不可访问
description: 了解如何排查存储帐户无效的问题。
ms.topic: article
ms.date: 09/05/2018
ms.openlocfilehash: 9f6592b6d5ef88127a9dfca1e868564be0aa4ed5
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217288"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>排查错误：“Azure Functions 运行时不可访问”

本文帮助你排查 Azure 门户中显示的以下错误字符串：

> “错误:Azure Functions 运行时不可访问。 单击此处获取存储配置的详细信息。”

当函数运行时无法启动时，会出现此问题。 导致此情况的最常见原因是函数应用无法访问其存储帐户。 有关详细信息，请参阅[存储帐户要求](storage-considerations.md#storage-account-requirements)。

本文的其余部分将帮助你解决此错误的具体原因，包括如何识别和解决每种情况。

## <a name="storage-account-was-deleted"></a>存储帐户已被删除

每个函数应用都需要存储帐户才能运行。 如果删除了该帐户，则函数将不起作用。

首先在应用程序设置中查找你的存储帐户名称。 `AzureWebJobsStorage`或 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 包含存储帐户的名称作为连接字符串的一部分。 有关详细信息，请参阅 [Azure Functions 的应用设置参考](./functions-app-settings.md#azurewebjobsstorage)。

在 Azure 门户中搜索你的存储帐户，确定它是否仍然存在。 如果它已被删除，请重新创建存储帐户，并替换存储连接字符串。 函数代码已丢失，需要重新部署。

## <a name="storage-account-application-settings-were-deleted"></a>存储帐户应用程序设置已被删除

在上一步骤中，如果找不到存储帐户连接字符串，则可能是该字符串已被删除或覆盖。 使用部署槽位或 Azure 资源管理器脚本设置应用程序设置时，通常会删除应用程序设置。

### <a name="required-application-settings"></a>必需的应用程序设置

* 必需：
    * [`AzureWebJobsStorage`](./functions-app-settings.md#azurewebjobsstorage)
* 需要它才能使用和高级计划功能：
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](./functions-app-settings.md)
    * [`WEBSITE_CONTENTSHARE`](./functions-app-settings.md)

有关详细信息，请参阅 [Azure Functions 的应用设置参考](./functions-app-settings.md)。

### <a name="guidance"></a>指南

* 请勿检查任何这些设置的 **槽设置** 。 如果交换部署槽位，函数应用将会中断。
* 在自动部署过程中，请不要修改这些设置。
* 必须在创建时提供这些设置并使其生效。 不包含这些设置的自动部署会导致函数应用不运行，即使以后添加设置，也是如此。

## <a name="storage-account-credentials-are-invalid"></a>存储帐户凭据无效

如果重新生成存储密钥，则必须更新上述存储帐户连接字符串。 有关存储密钥管理的详细信息，请参阅[创建 Azure 存储帐户](../storage/common/storage-account-create.md)。

## <a name="storage-account-is-inaccessible"></a>存储帐户不可访问

函数应用必须能够访问存储帐户。 阻止函数应用访问存储帐户的常见问题是：

* 函数应用部署到应用服务环境 (ASE) ，而不包含正确的网络规则，以允许与存储帐户通信。

* 存储帐户防火墙已启用，但未配置为允许在函数中传入和传出流量。 有关详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

## <a name="daily-execution-quota-is-full"></a>每日执行配额已满

如果配置了每日执行配额，则会暂时禁用函数应用，导致许多门户控制措施不可用。 

若要在 [Azure 门户](https://portal.azure.com)中验证配额，请在函数应用中选择“平台功能” > “函数应用设置”。   如果超出了设置的“每日使用配额”，将显示以下消息： 

  > “函数应用已达到使用配额并将停止使用 24 小时。”

若要解决此问题，请删除或提高每日配额，然后重启应用。 否则，应用的执行将被阻止一天。

## <a name="app-is-behind-a-firewall"></a>应用受防火墙保护

由于以下任一原因，可能无法访问函数应用：

* 函数应用托管在[内部负载均衡的应用服务环境](../app-service/environment/create-ilb-ase.md)中，并配置为阻止入站 Internet 流量。

* 函数应用的[入站 IP 限制](functions-networking-options.md#inbound-access-restrictions)配置为阻止 Internet 访问。 

Azure 门户直接调用正在运行的应用以提取函数列表，并对 Kudu 终结点发出 HTTP 调用。 仍可使用“平台功能”选项卡下的平台级设置。 

验证 ASE 配置：
1. 请参阅网络安全组 (ASE 所在子网的 NSG) 。
1. 验证入站规则是否允许你在其中访问应用程序的计算机的公共 IP 传出的流量。 
   
还可以通过连接到运行应用的虚拟网络的计算机或虚拟网络中运行的虚拟机来使用门户。 

有关入站规则配置的详细信息，请参阅[应用服务环境的网络注意事项](../app-service/environment/network-info.md#network-security-groups)的“网络安全组”部分。

## <a name="next-steps"></a>后续步骤

了解如何监视函数应用：

> [!div class="nextstepaction"]
> [监视 Azure Functions](functions-monitoring.md)
