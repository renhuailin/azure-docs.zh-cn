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
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008713"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>排查 Azure 数据工厂安全性和访问控制问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨了 Azure 数据工厂中的安全性和访问控制的常见故障排除方法。

## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>禁用公共网络访问后，身份验证密钥问题无效或为空

#### <a name="symptoms"></a>症状

在为数据工厂禁用公共网络访问后，自承载集成运行时将引发错误 "身份验证密钥无效或为空"。

#### <a name="cause"></a>原因

此问题很可能是由 DNS 解析问题引起的，因为禁用公共连接和建立专用终结点不会对重新连接有帮助。

#### <a name="resolution"></a>解决方法

1. PsPing 到 ADF 的 FQDN，并发现该缓冲区将进入 ADF 的公共终结点，即使在禁用后也是如此。

1. 更改 VM 中的主机文件，将专用 IP 映射到 FQDN 并再次运行 PsPing。 缓冲区将能够到达 ADF 的正确专用 IP。

1. 重新注册自承载集成运行时，将会发现它已启动并运行。
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>由于专用链接，无法在自承载 Vm 上注册 IR 身份验证密钥

#### <a name="symptoms"></a>症状

由于启用了专用链接，无法在自承载 VM 上注册 IR 身份验证密钥。

错误信息如下所示：

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>原因

此问题可能由 SHIR 尝试安装的 VM 引起。 应启用公共网络访问以连接到云。

#### <a name="resolution"></a>解决方法

 **解决方案1：** 你可以执行以下步骤来解决此问题：

1. 导航到下面的链接： 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. 单击 " **试用** " 选项，并填写所需的全部详细信息。 在 **正文** 中也粘贴以下属性：

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. 单击页面底部的 " **运行** " 以运行函数。 请确保收到响应代码200。 我们粘贴的属性也将显示在 JSON 定义中。

1. 然后，你可以尝试在集成运行时中再次添加 IR 身份验证密钥。


**解决方案2：** 可以参考以下文章了解解决方案：

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

尝试使用用户界面启用公用网络访问。

![启用公共网络访问](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>后续步骤

有关故障排除的更多帮助，请尝试以下资源：

*  [用于数据工厂的专用链接](data-factory-private-link.md)
*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft 问答页](/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)