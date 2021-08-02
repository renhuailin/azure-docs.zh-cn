---
title: 创建 Azure 对等互连服务连接 - Azure 门户
description: 了解如何使用 Azure 门户创建 Azure 对等互连服务
services: peering-service
author: gthareja
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 04/07/2021
ms.author: gatharej
ms.openlocfilehash: b75c0ec5be4ac26189a0e847a58cbe81461c5ff2
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952470"
---
# <a name="create-peering-service-connection-using-the-azure-portal"></a>使用 Azure 门户创建对等互连服务连接

Azure 对等互连服务是一种网络服务，可增强客户与 Microsoft 公有云服务（例如 Microsoft 365、Dynamics 365、服务型软件 (SaaS) 服务、Azure 或可通过公共 Internet 访问的任何 Microsoft 服务）建立的连接。

在本文中，你将了解如何使用 Azure 门户创建对等互连服务连接。

如果还没有 Azure 订阅，请现在就创建一个[帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> 

## <a name="prerequisites"></a>先决条件

必须满足以下条件：

### <a name="azure-account"></a>Azure 帐户

必须具有使用中的有效 Microsoft Azure 帐户。 设置对等互连服务连接时需要使用此帐户。 对等互连服务连接是 Azure 订阅中的资源。 

### <a name="connectivity-provider"></a>连接服务提供商

你可以选用任何 [Azure 对等互连服务提供商](./location-partners.md)来获取对等互连服务，以最佳方式连接你的网络和 Microsoft 网络。




## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在浏览器中，转到 Azure 门户，然后使用你的 Azure 帐户登录。

## <a name="create-a-peering-service-connection"></a>创建对等互连服务连接

1. 若要创建对等互连服务连接，请选择“创建资源” ****  >“对等互连服务” **** 。

    ![创建对等互连服务](./media/peering-service-portal/peering-servicecreate.png)

1. 在“创建对等互连服务连接”页面上的“基本信息”选项卡上输入以下详细信息。

 
1. 选择订阅以及与订阅关联的资源组。

   ![创建对等互连基本选项卡](./media/peering-service-portal/peering-servicebasics.png)

1. 输入对等互连服务实例应注册到的“名称”。
 
1. 现在，选择页面底部的“下一步:配置”按钮。 “配置”页面随即出现。

## <a name="configure-the-peering-service-connection"></a>配置对等互连服务连接

1. 在“配置”页面上，选择必须启用对等互连服务的客户网络位置，只需从“对等互连服务位置”下拉列表中选择相同位置即可。 

1. 通过从“对等互连服务提供商”下拉列表中选择提供商名称，来选择必须从中获取对等互连服务的服务提供商。

1. 选择最接近客户网络位置的提供商主对等互连位置。 这是 Microsoft 与合作伙伴之间的对等互连服务位置。

1. 选择提供商的备用对等互连位置，作为下一个最接近客户网络位置的位置。 仅当主对等互连服务位置在灾难恢复中出现问题时，对等互连服务才会通过备用位置激活。 如果选择“无”，则在主对等互连服务位置出现问题时，Internet 将成为默认故障转移路由。

 
1. 选择“前缀”部分底部的“创建新前缀”，文本框随即出现。 现在，请输入前缀资源的名称以及与服务提供商关联的前缀。

1. 选择“前缀键”并添加提供商（ISP 或 IXP）提供给你的前缀键。 此前缀使 MS 可以验证前缀以及分配 IP 前缀的提供商。
   > ![屏幕截图显示了“创建对等互连服务连接”页的“配置”选项卡，可以在其中输入前缀键。](./media/peering-service-portal/peering-serviceconfiguration.png)

1. 在页面左下角选择“审阅 + 创建”按钮。 “审阅 + 创建”页面随即出现，Azure 会验证你的配置。
    

1. 当看到显示“验证通过”消息时，选择“创建” 。

   > ![屏幕截图显示了“创建对等互连服务连接”页的“查看 + 创建”选项卡。](./media/peering-service-portal/peering-service-prefix.png)


1. 创建对等互连服务连接之后，对包含的前缀执行其他验证。 可以在资源名称的“前缀”部分下查看验证状态。 如果验证失败，则显示以下错误消息之一：

   - 对等互连服务前缀无效，前缀应采用有效格式，目前仅支持 IPv4 前缀。
   - 未收到对等互连服务提供商提供的前缀，请与对等互连服务提供商联系。
   - 前缀公告没有有效的 BGP 社区，请与对等互连服务提供商联系。
   - 收到的前缀的 AS 路径较长 (>3)，请与对等互连服务提供商联系。
   - 收到的前缀在路径中包含专用 AS，请与对等互连服务提供商联系。

### <a name="add-or-remove-a-prefix"></a>添加或删除前缀

在“前缀”页面上选择“添加前缀”以添加前缀。

选择列出的前缀旁的省略号 (...)，然后选择“删除”选项。

### <a name="delete-a-peering-service-connection"></a>删除对等互连服务连接

在“所有资源”页面上，选中“对等互连服务”的复选框，然后选择页面顶部的“删除”选项。

> [!NOTE]
> 不能修改现有前缀。
>

## <a name="next-steps"></a>后续步骤

- 若要了解对等互连服务连接，请参阅[对等互连服务连接](connection.md)。
- 若要了解对等互连服务连接遥测数据，请参阅[对等互连服务连接遥测数据](connection-telemetry.md)。
- 若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。
- 若要使用 Azure PowerShell 创建对等互连服务连接，请参阅[创建对等互连服务连接 - Azure PowerShell](powershell.md)。
- 若要使用 Azure CLI 创建此连接，请参阅[创建对等互连服务连接 - Azure CLI](cli.md)。