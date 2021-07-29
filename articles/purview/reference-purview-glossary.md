---
title: Purview 产品词汇表
description: 词汇表定义了在整个 Azure Purview 中使用的术语
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: ec011e4c9b0cab17bd9427020ba8842734e1f590
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111811905"
---
# <a name="azure-purview-product-glossary"></a>Azure Purview 产品词汇表

以下是在整个 Azure Purview 中使用的术语的词汇表。

## <a name="annotation"></a>Annotation
与 Azure Purview 中的数据资产关联的信息，例如，词汇表术语和分类。 应用后，可以在“搜索”中使用批注来帮助发现数据资产。 
## <a name="approved"></a>已批准
由有权更改请求状态的个人或团体为任何请求提供的一种状态，表示该请求已经被接受，并且令人满意。 
## <a name="asset"></a>资产
存储在 Azure Purview 数据目录中的任何单个对象。
> [!NOTE]
> 目录中的一个对象可能表示存储中的很多对象，例如，资源集是一各资产，但它是由存储中的多个分区文件组成的。
## <a name="azure-information-protection"></a>Azure 信息保护
这是一种云解决方案，支持对文档和电子邮件进行标记，以便对信息进行分类和保护。 标记项可以通过加密进行保护，可以使用水印进行标记，也可仅限于特定操作或用户，并且可以与某个项绑定。 这个基于云的解决方案依赖于 Azure Rights Management 服务 (RMS) 来强制实施限制。 
## <a name="business-glossary"></a>业务术语表
专业术语及其定义的列表（可搜索），组织使用这些专业术语来描述关键的业务词汇。 使用业务术语表可在整个组织内提供一致的数据使用方式。 
## <a name="classification-report"></a>分类报告
一份报告，显示有关扫描数据的关键分类详细信息。  
## <a name="classification"></a>分类
注释的类型，注释用于标识资产或列的属性，如“年龄”、“电子邮件地址”和“街道地址”。 这些属性可以在扫描期间分配，也可以手动添加。 
## <a name="classification-rule"></a>分类规则
分类规则是一组条件，决定当内容与指定的模式匹配时，如何对扫描的数据进行分类。  
## <a name="contact"></a>联系人
与数据目录中的某个实体关联的个人 
## <a name="control-plane"></a>控制面板
用于管理订阅中的资源（例如基于角色的访问控制和 Azure 策略）的操作，这些操作将被发送到 Azure 资源管理器终结点。 
## <a name="credential"></a>凭据
验证访问控制系统中使用的身份或工具。 凭据可用于对个人或团体进行身份验证，目的是授予对数据资产的访问权限。 
## <a name="data-catalog"></a>数据目录
Azure Purview 的功能，使客户能够查看和管理数据实体中资产的元数据。
## <a name="data-map"></a>数据映射
Azure Purview 的功能，使客户能够管理其数据实体，如扫描、世系和移动。
## <a name="expert"></a>专家
组织中了解数据资产或术语表术语的完整背景的个人。 
## <a name="fully-qualified-name-fqn"></a>完全限定名称 (FQN)
一个路径，定义资产在其数据源中的位置。  
## <a name="glossary-term"></a>术语表术语
业务术语表中的条目，用于定义特定于组织的概念。 术语表术语可以包含有关同义词、首字母缩写词和相关术语的信息。 
## <a name="insights"></a>洞察力
Azure Purview 中的一个区域，您可以在其中查看汇总了有关数据信息的报告。
## <a name="lineage"></a>沿袭
数据在从其源到目标的移动时转换和流动的方式。 了解跨数据实体的这一流程可帮助组织查看其数据的历史记录，以及帮助进行故障排除或影响分析。 
## <a name="management-center"></a>管理中心
Azure Purview 中的一个区域，你可以在其中管理连接、用户、角色和凭据。
## <a name="on-premises-data"></a>本地数据
数据中心内由客户控制的数据，例如，云或服务型软件 (SaaS) 之外的数据。 
## <a name="owner"></a>所有者
负责管理数据资产的个人或团体。  
## <a name="purview-instance"></a>Purview 实例
单个 Azure Purview 资源。 
## <a name="registered-source"></a>已注册源
已添加到 Azure Purview 实例并且现在作为数据目录的一部分进行管理的源。 
## <a name="related-terms"></a>相关术语
链接到组织内其他术语的词汇表术语。  
## <a name="resource-set"></a>资源集
单个资产，表示存储中多个已分区文件或对象。 例如， Azure Purview 将分区 Apache Spark 输出存储为单个资源集，而不是为每个单独文件存储唯一资产。 
## <a name="role"></a>角色
为 Azure Purview 实例中的用户分配的权限。 Purview Data Curator 或 Purview Data Reader 等角色，确定可以在产品中执行哪些操作。 
## <a name="scan"></a>扫描
一个 Azure Purview 进程，用于检查源或源集，并将其元数据引入到数据目录中。 可以手动运行扫描，也可以使用扫描触发器按计划运行扫描。 
## <a name="scan-ruleset"></a>扫描规则集
一组规则，用于定义扫描要在目录中引入的数据类型和分类。 
## <a name="scan-trigger"></a>扫描触发器
一项计划，用于确定运行扫描时的重复执行。 
## <a name="sensitivity-label"></a>敏感度标签
用于对组织的数据进行分类和保护的注释。 Azure Purview 与 Microsoft 信息保护集成，以创建敏感度标签。 
## <a name="sensitivity-label-report"></a>敏感度标签报告
在数据实体中应用的敏感度标签的摘要。 
## <a name="service"></a>服务
提供独立功能并按订阅或许可证提供给客户的产品。 
## <a name="source"></a>Source
存储数据的系统。 源可以在不同的位置（例如云或本地）托管。 注册并扫描源，以便可以在 Azure Purview 中对其进行管理。 
## <a name="source-type"></a>源类型
对 Azure Purview 实例中使用的注册源（例如，Azure SQL 数据库、Azure Blob 存储、Amazon S3 或 SAP ECC）进行的分类。 
## <a name="steward"></a>专员
为词汇表术语定义标准的个人。 它们负责为分配的实体维护质量标准、命名法和规则。 

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure Purview，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。