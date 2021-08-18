---
title: 使用 Power BI 连接到 Azure Analysis Services | Microsoft Docs
description: 了解如何使用 Power BI 连接到 Azure Analysis Services 服务器。 连接后，用户可以浏览模型数据。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/30/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a956d5e3be0a5f92e22dcd1536339ff5244e19dc
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113213339"
---
# <a name="connect-with-power-bi"></a>使用 Power BI 进行连接

在 Azure 中创建服务器并向其部署表格模型后，组织中的用户便可以连接并开始浏览数据。 

> [!NOTE]
> 如果将 Power BI Desktop 模型发布到 Power BI 服务，则在 Azure Analysis Services 服务器上，请确保未选择区分大小写排序规则服务器属性（默认未选择此属性）。 可使用 SQL Server Management Studio 设置区分大小写服务器属性。
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>在 Power BI Desktop 中连接

1. 在 Power BI Desktop 中，单击“获取数据”   > “Azure”   > “Azure Analysis Services 数据库”  。

2. 在“服务器”  中，输入服务器名称。 请务必包括完整 URL；例如，asazure://westcentralus.asazure.windows.net/advworks。

3. 在“数据库”  中，如果知道要连接到的表格模型数据库或透视的名称，请将其粘贴在此处。 如果不知道，可以将此字段留空，并在稍后选择数据库或透视。

4. 选择连接选项，然后按“连接”  。 

    同时支持“实时连接”  和“导入”  选项。 但是，我们建议你使用实时连接，因为导入模式确实存在一些限制；最重要的是，导入过程中可能会影响服务器性能。
    
    如果 Power BI 模型处于[混合存储模式](/power-bi/transform-model/desktop-composite-models)，则“实时连接”选项将替换为 [DirectQuery](/power-bi/connect-data/desktop-directquery-datasets-azure-analysis-services) 选项 。 如果模型从“导入”切换到“混合存储”模式，实时连接也会自动升级到 DirectQuery。

5. 当系统提示你输入凭据时，请选择“Microsoft 帐户”，然后单击“登录”。 

    :::image type="content" source="media/analysis-services-connect-pbi/aas-sign-in.png" alt-text="登录到 Azure 的身份":::

   > [!NOTE]
   > 不支持 Windows 身份验证和基本身份验证。 

6. 在 **导航器** 中，展开服务器，选择要连接到的模型或透视，并单击“连接”。 单击模型或透视可显示该视图的所有对象。

    Power BI Desktop 中会打开模型，并且在“报表”视图中显示空白报表。 “字段”列表中会显示所有非隐藏的模型对象。 连接状态显示在右下角。

## <a name="connect-in-power-bi-service"></a>在 Power BI（服务）中进行连接

1. 在服务器上创建一个与模型具有实时连接的 Power BI Desktop 文件。
2. 在 [Power BI](https://powerbi.microsoft.com) 中，单击“获取数据” > “文件”，然后找到 .pbix 文件并选择该文件。

## <a name="request-memory-limit"></a>请求内存限制

为了保护系统的性能，无论 Azure 分析服务服务器上配置的[查询内存限制](/analysis-services/server-properties/memory-properties?view=azure-analysis-services-current&preserve-view=true)如何，都会对 Power BI 报告针对 Azure 分析服务发出的所有查询强制实施内存限制。 如果查询占用过多内存，则用户应考虑简化查询或其计算。

|查询类型| 请求内存限制 |
|-----------------------------------------------------------|----------------------|
| 从 Power BI 进行实时连接                            | 10 GB  |
| 来自共享工作区中 Power BI 报表的 DirectQuery  | 1 GB   |
| 来自高级工作区中 Power BI 报表的 DirectQuery | 10 GB  |
| Power BI 问答 | 100 MB |

## <a name="see-also"></a>另请参阅
[连接到 Azure Analysis Services](analysis-services-connect.md)   
[客户端库](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)
