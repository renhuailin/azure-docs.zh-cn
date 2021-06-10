---
title: 导出已取消标识的数据 (预览) Azure API for FHIR
description: 本文介绍如何设置和使用已取消标识的导出
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: ranku
ms.openlocfilehash: ba0af51eaf15211c2214f9457235afa250d92b7b
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111814438"
---
# <a name="exporting-de-identified-data-preview"></a>导出已取消标识的数据 (预览) 

> [!Note] 
> 使用已取消标识的导出时，结果因数据输入和客户选择的函数等因素而异。 Microsoft 无法评估已取消标识的导出输出或确定客户用例和符合性需求的可接受性。 不保证取消标识的导出满足任何特定的法律、法规或合规性要求。

$export命令还可用于从 FHIR 服务器导出已取消标识的数据。 它使用 [FHIR](https://github.com/microsoft/FHIR-Tools-for-Anonymization)工具中的匿名引擎进行匿名化 ，并使用查询参数中的匿名化配置详细信息。 你可以创建自己的匿名配置文件，或使用 HIPAA 安全方法的示例[](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method)配置文件作为起点。 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> 目前，Azure API for FHIR仅在系统级别支持取消标识导出 ($export) 。

|查询参数            | 示例 |可选性| 说明|
|---------------------------|---------|-----------|------------|
| _\_匿名配置_   |DemoConfig.js打开|对已取消标识的导出是必需的 |配置文件的名称。 请参阅此处的配置文件 [格式](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)。 此文件应保存在配置为导出位置的同一Azure 存储帐户中名为"匿名"的容器中。 |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|用于取消标识导出的可选选项|这是配置文件的 Etag。 可以使用 blob 属性中的Azure 存储资源管理器获取 Etag|

> [!IMPORTANT]
> 原始导出和已取消标识的导出写入操作都写入到作为导出配置的一部分指定的同一 Azure 存储帐户。 建议使用对应于不同已取消标识配置的不同容器，并管理容器级别的用户访问。
