---
title: 清除 Azure CDN 终结点 | Microsoft Docs
description: 了解如何从 Azure 内容分发网络终结点清除缓存内容。 边缘节点缓存资产，直到其生存时间到期。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/30/2021
ms.author: allensu
ms.openlocfilehash: d54b181ee55d841f8739008a2fb6657f7885cb96
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126439"
---
# <a name="purge-an-azure-cdn-endpoint"></a>清除 Azure CDN 终结点
## <a name="overview"></a>概述
直到资产的生存时间 (TTL) 到期之前，Azure CDN 边缘节点会将缓存资产。  资产的 TTL 到期后，当客户端从边缘节点请求资产时，边缘节点将检索具有最新更新的资产副本，以满足客户端请求并存储刷新缓存。

确保用户始终获取资产的最新副本的最佳做法是针对每次更新将资产版本化，并将其发布为新 URL。  CDN 会立即检索下用于一个客户端请求的新资产。  有时候可能希望从所有边缘节点清除缓存的内容，并强制其全部检索新的已更新资产。  这可能是由于 Web 应用程序获得了更新，或快速更新的资产包含不正确的信息。

> [!TIP]
> 请注意，清除仅限于清除 CDN 边缘服务器上缓存的内容。  任何下游缓存，如代理服务器和本地浏览器缓存，仍会保留该文件的缓存副本。  设置生存时间时请务必记住这一点。  可以在每次更新文件时为其提供一个唯一的名称，或利用[查询字符串缓存](cdn-query-string.md)来强制让下游客户端请求文件的最新版本。  
> 
> 

本教程会逐步指导完成从某个终结点的所有边缘节点清除资产。

## <a name="walkthrough"></a>演练
1. 在 [Azure 门户](https://portal.azure.com)中，浏览到包含要清除的终结点的 CDN 配置文件。
2. 从 CDN 配置文件的边栏选项卡，单击“清除”按钮。
   
    ![CDN 配置文件边栏选项卡](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    “清除”边栏选项卡打开。
   
    ![CDN 清除边栏选项卡](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. 在“清除”边栏选项卡上，从 URL 下拉列表中选择要清除的服务地址。
   
    ![清除窗体](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > 也可以通过单击 CDN 终结点边栏选项卡上的 **清除** 按钮进入“清除”边栏选项卡。  在这种情况下，**URL** 字段将预填充该特定终结点的服务地址。
   > 
   > 
4. 选择要从边缘节点清除的资产。  如果想清除所有资产，单击“**全部清除**”复选框。  否则，请在“路径”文本框中输入要清除的每个资产的路径。 路径支持以下格式。
    1. **单个 URL 清除**：通过指定完整 URL 清除单个资产（包含或不包含文件扩展名，例如 `/pictures/strasbourg.png`；`/pictures/strasbourg`）
    2. **通配符清除**：星号 (\*) 可用作通配符。 清除路径中含 `/*` 的终结点下的所有文件夹、子文件夹和文件，或通过指定后跟 `/*`（例如 `/pictures/*`）的文件夹来清除特定文件夹下的所有子文件夹和文件。  请注意，目前来自 Akamai 的 Azure CDN 不支持通配符清除。 
    3. **根域清除**：清除路径中具有“/”的终结点的根。
   
   > [!TIP]
   > 1. 必须指定用于清除的路径，且路径必须是符合以下[正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)的相对 URL。 目前，Akamai 的 Azure CDN 不支持“全部清除”和“通配符清除”  。
   >
   >    1. 单个 URL 清除 `@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   >    1. 查询字符串 `@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   >    1. 通配符清除 `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`。 
   > 
   >    输入文本后，会出现更多“**路径**”文本框，以便允许生成包含多个资产的列表。  通过单击省略号 (...) 按钮，可以从列表中删除资产。
   > 
   > 1. 在 Microsoft 的 Azure CDN 中，不考虑清除 URL 路径中的查询字符串。 如果要清除的路径以 `/TestCDN?myname=max` 的形式提供，则仅考虑 `/TestCDN`。 查询字符串 `myname=max` 将被忽略。 `TestCDN?myname=max` 和 `TestCDN?myname=clark` 都将被清除。

5. 单击 **清除** 按钮。
   
    ![“清除”按钮](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> 对于来自 Microsoft 的 Azure CDN，清除请求需要大约 10 分钟的时间来处理；对于来自 Verizon 的 Azure CDN（标准和高级）大约需要 2 分钟；对于来自 Akamai 的 Azure CDN，大约需要 10 分钟  。  在任何给定时间，Azure CDN 在配置文件级别的并发清除请求限制均为 100。 
> 
> 

## <a name="see-also"></a>另请参阅
* [在 Azure CDN 终结点上预加载资产](cdn-preload-endpoint.md)
* [Azure CDN REST API 参考 - 清除或预加载终结点](/rest/api/cdn/cdn/endpoints)

