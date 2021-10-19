---
title: 在工作区部署Azure 门户 - Azure 医疗保健 API
description: 本文档将指导用户如何在工作区部署Azure 门户。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 07/12/2021
ms.author: ginle
ms.openlocfilehash: 1b77b05f5780f41a9b4c573d117b18f2493b635a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121782034"
---
# <a name="deploy-healthcare-apis-preview-workspace-using-azure-portal"></a>使用门户将医疗保健 API (预览) 工作区Azure 门户

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文将了解如何通过门户部署 Azure 医疗保健 API 来创建Azure 门户。 工作区是一个集中式逻辑容器，适用于所有医疗保健 API 服务，例如 FHIR 服务、DICOM®服务和 IoT 连接器。 通过它，可以组织和管理所有基础数据集和服务之间共享的某些配置设置（如果适用）。


## <a name="prerequisite"></a>先决条件

必须先拥有帐户订阅，然后才能Azure 门户工作区。 如果没有 Azure 订阅，请参阅创建免费的[Azure 帐户。](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)

## <a name="create-new-azure-service"></a>创建新的 Azure 服务

在 Azure 门户中，选择“创建资源”。

[![创建资源 ](media/create-resource.png) ](media/create-resource.png#lightbox)

## <a name="search-for-azure-healthcare-apis"></a>搜索 Azure 医疗保健 API

在搜索框中，输入 **"Azure 医疗保健 API"。**

[![搜索医疗保健 API ](media/search-for-healthcare-apis.png) ](media/search-for-healthcare-apis.png#lightbox)

## <a name="create-azure-healthcare-api-account"></a>创建 Azure 医疗保健 API 帐户

选择 **"** 创建"以创建新的 Azure 医疗保健 API 帐户。

   [![创建工作区预览 ](media/create-workspace-preview.png) ](media/create-workspace-preview.png#lightbox)

## <a name="enter-subscription-and-instance-details"></a>输入订阅和实例详细信息

1. 从 **下拉列表中选择**"**订阅**"和"资源组"，或选择"**新建"。**

   [![新建工作区 ](media/create-healthcare-api-workspace-new.png) ](media/create-healthcare-api-workspace-new.png#lightbox)

2. 输入 **工作区** 的名称，然后选择"区域 **"。** 名称必须为 3 到 24 个字母数字字符，全部为小写。 请勿使用连字符"-"，因为它是无效的名称字符。 有关区域和可用性区域的信息，请参阅 Azure [中的可用性区域和可用性](../availability-zones/az-overview.md)。

3.  (**可选)** 选择"下一步 **： 标记>"。** 输入"**名称"** 和 **"值**"，然后选择"下一 **步： 查看 + 创建"。** 

   [![标记 ](media/tags-new.png) ](media/tags-new.png#lightbox)

   标记是用于对资源进行分类的名称/值对。 有关标记详细信息，请参阅使用 [标记来组织 Azure 资源和管理层次结构](.././azure-resource-manager/management/tag-resources.md)。

4. 选择“创建”  。

[![工作区条款 ](media/workspace-terms.png) ](media/workspace-terms.png)


   **可选**：可以选择 **"下载模板"以自动化** 新创建的工作区。


## <a name="next-steps"></a>后续步骤

创建工作区后，可以：

* 部署 FHIR 服务
* 部署 DICOM 服务
* 将IoT Connector数据部署到 FHIR 服务。
* 通过转换和取消标识 API 将数据转换为不同的格式和次要用途。


[![部署不同的服务 ](media/healthcare-apis-deploy-services.png) ](media/healthcare-apis-deploy-services.png)

>[!div class="nextstepaction"]
>[工作区概述](workspace-overview.md)

