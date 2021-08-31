---
title: 为 FHIR 服务导出去标识化数据（预览版）
description: 本文介绍了如何设置和使用去标识化导出
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: ranku
ms.openlocfilehash: b0f8a1a9ddce7648d26bf73ebb0eadead06dba6c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778657"
---
# <a name="exporting-de-identified-data-preview"></a>导出去标识化数据（预览版）

> [!Note] 
> 使用去标识化导出时的结果取决于输入的数据和客户选择的功能等因素。 Microsoft 无法评估去标识化导出输出或确定客户用例和合规性需求的可接受性。 不保证去标识化导出符合任何特定的法律、法规或合规性要求。

$export 命令还可以用于从 FHIR 服务器导出去标识化数据。 它使用 [FHIR 匿名化工具](https://github.com/microsoft/FHIR-Tools-for-Anonymization)中的匿名化引擎，并在查询参数中采用匿名化配置详细信息。 你可以创建自己的匿名化配置文件，或使用 HIPAA 安全港方法的[示例配置文件](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method)作为起点。 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> 现在，FHIR 服务仅支持系统级别 ($export) 的去标识化导出。

|查询参数            | 示例 |可选性| 说明|
|---------------------------|---------|-----------|------------|
| \_anonymizationConfig   |DemoConfig.json|对于去标识化导出是必需的 |配置文件的名称。 请在[此处](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)查看配置文件格式。 此文件应保留在同一 Azure 存储帐户中配置为导出位置的容器 anonymization 内。 |
| \_anonymizationConfigEtag|"0x8D8494A069489EC"|对于去识别化导出是可选的|这是配置文件的 Etag。 可以使用 Azure 存储资源管理器从 blob 属性中获取 Etag|

> [!IMPORTANT]
> 原始导出和去标识化导出都会写入在导出配置过程中指定的相同 Azure 存储帐户中。 建议使用与不同的去标识化配置对应的不同容器，并在容器级别管理用户访问权限。
