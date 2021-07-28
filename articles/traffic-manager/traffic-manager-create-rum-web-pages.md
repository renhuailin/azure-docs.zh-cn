---
title: 使用网页发送真实用户度量 - Azure 流量管理器
description: 本文介绍如何设置网页，将真实用户度量发送到 Azure 流量管理器。
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/06/2021
ms.author: duau
ms.openlocfilehash: 7ec91945c901f46d7036e8c474f9f5f0714ce65e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580372"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>如何使用网页将实际用户度量发送到 Azure 流量管理器

可以配置网页，通过获取实际用户度量 (RUM) 密钥并将生成的代码嵌入到网页，将实际用户度量发送到流量管理器。

## <a name="obtain-a-real-user-measurements-key"></a>获取实际用户度量密钥

从客户端应用程序获取并发送到流量管理器的度量由使用唯一字符串的服务标识，称为“真实用户度量 (RUM) 密钥”。 可以使用 Azure 门户、REST API、PowerShell 或 Azure CLI 获取 RUM 密钥。

若要使用 Azure 门户获取 RUM 密钥，请执行以下操作：
1. 在浏览器中，登录 Azure 门户。 如果还没有帐户，可以注册免费一个月试用版。

1. 在门户的搜索栏中，搜索要修改的流量管理器配置文件名称，然后在显示的结果中选择该流量管理器配置文件。

1. 在“流量管理器配置文件”页上，选择“设置”下的“真实用户度量”。

1. 选择“生成密钥”新建 RUM 密钥。

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/generate-rum-key.png" alt-text="如何生成密钥的屏幕截图。"::: 

   **图 1：生成实际用户度量密钥**

1. 该页面现在显示生成的 RUM 密钥和需要嵌入到 HTML 页中的 JavaScript 代码片段。

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png" alt-text="生成的 JavaScript 代码的屏幕截图。"::: 

    **图 2：实际用户度量密钥和度量 JavaScript**
 
1. 选择“复制”按钮复制 JavaScript 代码。 

> [!IMPORTANT]
> 使用生成的 JavaScript 使“实际用户度量”功能正常工作。 对此脚本或“实际用户度量”使用的脚本进行任何更改可能会导致不可预知的行为。

## <a name="embed-the-code-to-an-html-web-page"></a>将代码嵌入到 HTML 网页

获取 RUM 密钥后，下一步是将此复制的 JavaScript 嵌入到最终用户访问的 HTML 页面。 编辑 HTML 可以使用不同的工具和工作流以多种方式完成。 本示例演示如何更新 HTML 页面以添加此脚本。 可以使用本指南使它适合于 HTML 源管理工作流。

1. 在文本编辑器中打开 HTML 页面。

1. 将上一部分中复制的 JavaScript 代码粘贴到 HTML 的正文部分。 复制的代码位于第 8 行和第 9 行，请参见图 3。

    :::image type="content" source="./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png" alt-text="生成的嵌入到网页中的 JavaScript 的屏幕截图。"::: 

    **图 3：嵌入实际用户度量 JavaScript 的简单 HTML**

1. 保存 HTML 文件并将托管在连接到 Internet 的 Web 服务器上。

1. 下一次在 Web 浏览器中呈现此页时，将下载引用的 JavaScript，并且脚本将执行度量和报告操作。

## <a name="next-steps"></a>后续步骤
- 详细了解[实际用户度量](traffic-manager-rum-overview.md)
- 了解[流量管理器的工作原理](traffic-manager-overview.md)
- 详细了解流量管理器支持的[流量路由方法](traffic-manager-routing-methods.md)
- 了解如何[创建流量管理器配置文件](./quickstart-create-traffic-manager-profile.md)
