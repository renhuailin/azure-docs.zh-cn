---
title: 解决安全和访问控制问题
description: 了解如何对 Azure 数据工厂中的安全性和访问控制问题进行故障排除。
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 99c03ae4430d1a4caf575bdb9900200af0217bf1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109737"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>排查 Azure 数据工厂安全性和访问控制问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨了 Azure 数据工厂中的安全性和访问控制的常见故障排除方法。

## <a name="common-errors-and-messages"></a>常见错误和消息


### <a name="connectivity-issue-of-copy-activity-in-cloud-data-store"></a>云数据存储中复制活动的连接问题

#### <a name="symptoms"></a>症状

如果源/接收器数据存储发生连接问题，可能会返回各种类型的错误消息。

#### <a name="cause"></a>原因 

此问题主要由以下因素引起：

1. 自承载 IR 的节点中的代理设置 (如果使用的是自承载 IR) 

1. 如果使用自承载 IR) ，则自承载 IR 的节点中的防火墙设置 (

1. 云数据存储中的防火墙设置

#### <a name="resolution"></a>解决方法

1. 请先检查以下几点，以确保此问题是由连接问题引起的：

   - 错误由源/接收器连接器引发。

   - 该活动在副本开始时失败

   - 与一个节点 Azure IR 或自承载 IR 的一致性不一致，因为如果只有一部分节点出现问题，则可能是多节点自承载 IR 的随机失败。

1. 如果使用 **自承载 IR** ，请检查代理、防火墙和网络设置，因为在 Azure IR 中运行相同的数据存储可能会成功。 有关疑难解答，请参阅以下链接：

   [自承载 IR 端口和防火墙](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls) 
   [ADLS 连接器](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
1. 如果使用 **Azure IR**，请尝试禁用数据存储的防火墙设置。 通过这种方式，可以解决以下两种情况的问题：
  
   * [Azure IR 的 IP 地址](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) 不在允许列表中。

   * *允许受信任的 Microsoft 服务访问* [Azure Blob 存储](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) 和 [ADLS 第2代](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities)的此存储帐户功能。

   * 不 *允许访问 Azure 服务* ADLS Gen1。

1. 如果上面的方法不起作用，请联系 Microsoft 获取帮助。


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>禁用公共网络访问后，身份验证密钥问题无效或为空

#### <a name="symptoms"></a>症状

为数据工厂禁用公共网络访问后，因为自承载集成运行时引发了以下错误： "身份验证密钥无效或为空。"

#### <a name="cause"></a>原因

此问题很可能是由 DNS 解析问题引起的，因为禁用公共连接和建立专用终结点不会对重新连接有帮助。

你可以执行以下步骤来验证数据工厂 FQDN 是否解析为公共 IP 地址：

1. 确认已在 "数据工厂" 专用终结点所在的 VNET 中创建了 Azure VM。

2. 运行 PsPing 并从 Azure VM Ping 到数据工厂 FQDN：

   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`

   > [!Note]
   > 需要为 PsPing 命令指定端口，而443端口不是必需的。

3. 检查这两个命令是否解析为基于指定区域的 ADF 公共 IP (格式为 xxx) 。

#### <a name="resolution"></a>解决方法

- [有关 Azure 数据工厂的 Azure 专用链接](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints)，请参阅。 此说明用于配置专用 DNS 区域/服务器，以将数据工厂 FQDN 解析为专用 IP 地址。

- 如果你不愿意配置专用 DNS 区域/服务器，请采取以下步骤作为临时解决方案。 但是，仍建议将自定义 DNS 作为长期解决方案。

  1. 更改 windows 中的主机文件，并将专用 IP (ADF 专用终结点) 映射到 ADF FQDN。
  
     导航到 Azure VM 中的路径 "C:\Windows\System32\drivers\etc"，并打开带有记事本的 **主机** 文件。 将 "将专用 IP 映射到 FQDN" 行添加到文件末尾，并保存更改。
     
     ![向主机添加映射](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. 在上述验证步骤中重新运行相同的命令，以检查应包含专用 IP 的响应。

  1. 重新注册自承载集成运行时，应解决该问题。
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>由于专用链接，无法在自承载 Vm 上注册 IR 身份验证密钥

#### <a name="symptoms"></a>症状

由于启用了专用链接，无法在自承载 VM 上注册 IR 身份验证密钥。

错误信息如下所示：

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>原因

此问题可能是由尝试安装自承载 IR 的 VM 导致的。 若要连接到云，应确保已启用公共网络访问。

#### <a name="resolution"></a>解决方法

 **解决方案1：** 你可以执行以下步骤来解决此问题：

1. 中转到 " [工厂-更新](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) " 页。

1. 在右上角，选择 " **试用** " 按钮。

1. 在 " **参数**" 下，填写所需的信息。 

1. 在 " **正文**" 下，粘贴以下属性：
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. 选择 " **运行** " 以运行函数。 

1. 确认显示 **响应代码： 200** 。 你粘贴的属性也应显示在 JSON 定义中。

1. 在集成运行时中再次添加 IR 身份验证密钥。


**解决方案2：** 可以参考以下文章了解解决方案：

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

尝试在用户界面上启用公用网络访问，如以下屏幕截图所示：

![启用公共网络访问](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>后续步骤

有关故障排除的更多帮助，请尝试以下资源：

*  [用于数据工厂的专用链接](data-factory-private-link.md)
*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&页面](/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
