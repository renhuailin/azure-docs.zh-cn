---
title: 配置自定义设置
description: 配置应用到整个 Azure 应用服务环境的设置。 了解如何通过 Azure 资源管理器模板来这样做。
author: ccompy
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 5c1e81d02aa35a40a296f04e456be09eeed10331
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226383"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>应用服务环境的自定义配置设置
## <a name="overview"></a>概述
由于应用服务环境 (ASE) 对单个客户是隔离的，因此有一些可专门应用于应用服务环境的配置设置。 本文介绍各种可用于应用服务环境的特定自定义设置。

如果没有应用服务环境，请参阅 [How to Create an App Service Environment](app-service-web-how-to-create-an-app-service-environment.md)（如何创建应用服务环境）。

可以在新的 **clusterSettings** 属性中使用数组存储应用服务环境自定义设置。 可以在 *hostingEnvironments* Azure 资源管理器实体的“Properties”字典中找到此属性。

以下简略的 Resource Manager 模板代码片段显示了 **clusterSettings** 属性：

```json
"resources": [
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "workerPools": [ ...],
        etc...
    }
}
```

**clusterSettings** 属性可以包含在 Resource Manager 模板中，以更新应用服务环境。

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>使用 Azure 资源浏览器更新应用服务环境
也可以使用 [Azure 资源浏览器](https://resources.azure.com)更新应用服务环境。  

1. 在资源浏览器中，转到应用服务环境的节点（“订阅” > “resourceGroups” > “提供程序” > “Micrososft.Web” > “hostingEnvironments”）。 然后单击要更新的特定应用服务环境。
2. 在右窗格中，单击上方工具栏中的“读/写”，允许在资源浏览器中进行交互式编辑。  
3. 单击蓝色的“编辑”按钮，使 Resource Manager 模板可供编辑。
4. 滚动到右窗格底部。 **clusterSettings** 属性位于最底部，可以在此输入或更新其值。
5. 键入（或复制并粘贴）希望在 **clusterSettings** 属性中出现的配置值数组。  
6. 单击右窗格顶部的绿色“PUT”按钮，以提交对应用服务环境的更改。

不过，提交更改后，约需 30 分钟乘以应用服务环境中前端数量的时间，更改才会生效。
例如，如果应用服务环境有四个前端，大约需要两个小时才能完成配置更新。 实行配置更改时，就无法在应用服务环境中进行其他缩放操作或配置更改操作。

## <a name="enable-internal-encryption"></a>启用内部加密

应用服务环境作为一个黑框系统运行，你将看不到系统中的内部组件或通信。 为了实现更高的吞吐量，默认情况下，在内部组件之间不启用加密。 系统很安全，因为流量无法访问，不管你是要监视流量还是要访问流量。 如果你的合规性要求必须从端到端对数据路径进行完全加密，则可通过一种方法使用 clusterSetting 启用对完整数据路径的加密。  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
将 InternalEncryption 设置为 true 可对前端和辅助角色之间的 ASE 中的内部网络流量进行加密，还可对页面文件和辅助角色磁盘进行加密。 启用 InternalEncryption clusterSetting 后，可能会影响系统性能。 进行更改以启用 InternalEncryption 后，ASE 会处于不稳定状态，直到更改传播完毕。 更改的传播可能需要几个小时才能完成，具体取决于 ASE 中有多少实例。 强烈建议不要在 InternalEncryption 仍处于使用状态的情况下在 ASE 上启用它。 如果需要对主动使用的 ASE 启用 InternalEncryption，强烈建议将流量转移到备份环境，直到操作完成。 


## <a name="disable-tls-10-and-tls-11"></a>禁用 TLS 1.0 和 TLS 1.1

若要逐个应用地管理 TLS 设置，则可按[实施 TLS 设置](../configure-ssl-bindings.md#enforce-tls-versions)文档提供的指南进行操作。 

对于 ASE 中的所有应用，若要禁用所有入站 TLS 1.0 和 TLS 1.1 流量，可以设置以下 **clusterSettings** 条目：

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

设置的名称显示 1.0，但在配置以后，却禁用了 TLS 1.0 和 TLS 1.1。

## <a name="change-tls-cipher-suite-order"></a>更改 TLS 密码套件顺序
ASE 支持更改默认密码套件。 默认密码集与多租户服务中使用的密码集相同。 更改密码套件会影响整个应用服务部署，因此只能在单租户 ASE 中实现此功能。 ASE 需要两个密码套件：TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 和 TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256。 如果你希望使用最强和最小的密码套件集来操作 ASE，请仅使用两个必需的密码。 若要将 ASE 配置为仅使用它所需的密码，请修改 clusterSettings，如下所示。 

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
    }
],
```

> [!WARNING]
> 如果对 SChannel 无法理解的密码套件设置不正确的值，与服务器的所有 TLS 通信可能会停止运行。 在这种情况下，必须从 **clusterSettings** 中删除 *FrontEndSSLCipherSuiteOrder* 条目，并提交更新的 Resource Manager 模板以还原回默认的密码套件设置。  请谨慎使用此功能。

## <a name="get-started"></a>入门
Azure 快速入门 Resource Manager 模板站点包含具有[创建应用服务环境](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)基本定义的模板。

<!-- LINKS -->

<!-- IMAGES -->
