---
title: 在 Azure 市场中发布容器产品/服务的指南
description: 本文介绍在 Azure 市场中发布容器产品/服务的要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 11/30/2020
ms.openlocfilehash: 83c575aa40b80d9a8e39263e89a5e7860c8f8774
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "95741655"
---
# <a name="publishing-guide-for-azure-container-offers"></a>Azure 容器产品/服务的发布指南

Azure 容器产品/服务可帮助你将容器映像发布到 Azure 市场。 使用本指南了解此产品/服务类型的要求。

Azure 容器产品/服务是通过 Azure 市场部署并计费的交易产品/服务。 用户看到的列表选项为“立即获取”。

如果解决方案是设置为基于 Kubernetes 的 Azure 容器实例的 Docker 容器映像，请使用“Azure 容器”产品/服务类型。

> [!NOTE]
> Azure 容器实例为运行时 Docker 实例，提供在 Azure 中运行容器的最简捷方式，既无需管理任何虚拟机，也不必采用更高级的服务。 容器实例可以直接部署到 Azure，也可以由 Azure Kubernetes 服务或 Azure Kubernetes 服务引擎协调安排。  

Microsoft 目前支持免费和自带许可 (BYOL) 许可模型。

## <a name="container-offer-requirements"></a>容器套餐要求

| 要求 | 详细信息 |  
|:--- |:--- |  
| 计费和计量 | 支持免费或 BYOL 计费模型。<br><br> |  
| 从 Dockerfile 生成映像 | 容器映像必须基于 Docker 映像规范，并且从 Dockerfile 生成。<br> <br>有关生成 Docker 映像的详细信息，请参阅 [Dockerfile 参考](https://docs.docker.com/engine/reference/builder/#usage)的“使用情况”部分。<br><br> |  
| 在 Azure 容器注册表存储库中托管 | 容器映像必须在 Azure 容器注册表存储库中托管。<br> <br>有关使用 Azure 容器注册表的详细信息，请参阅[快速入门：使用 Azure 门户创建专用容器注册表](../container-registry/container-registry-get-started-portal.md)。<br><br> |  
| 图像标记 | 容器映像必须至少包含 1 个标记（最大标记数：16）。<br><br>有关标记映像的详细信息，请参阅 [Docker 文档](https://docs.docker.com/engine/reference/commandline/tag)站点上的 `docker tag` 页。<br><br> |  

## <a name="next-steps"></a>后续步骤

- 若要为容器产品/服务准备技术资产，请参阅[创建 Azure 容器技术资产](create-azure-container-technical-assets.md)。

- 若要创建 Azure 容器产品/服务，请参阅[在 Azure 市场中创建 Azure 容器产品/服务](create-azure-container-offer.md)获取详细信息。
