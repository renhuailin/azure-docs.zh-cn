---
title: 使用 Azure 门户-Azure 医疗保健 Api 部署 DICOM 服务
description: 本文介绍如何在 Azure 门户中部署 DICOM 服务。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: b07d9bba578aeac026864e40a23236816b510f1e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121782965"
---
# <a name="deploy-dicom-service-using-the-azure-portal"></a>使用 Azure 门户部署 DICOM 服务

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本快速入门中，你将了解如何使用 Azure 门户部署 DICOM 服务。

部署完成后，可以使用 Azure 门户导航到新创建的 DICOM 服务，查看详细信息，包括服务 URL。 用于访问 DICOM 服务的服务 URL 将为： ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` 。 发出请求时，请确保将版本指定为 url 的一部分。 有关详细信息，请参阅 [DICOM 服务文档的 API 版本控制](api-versioning-dicom-service.md)。

## <a name="prerequisite"></a>先决条件

若要部署 DICOM 服务，必须在 Azure 门户中创建一个工作区。 有关创建工作区的详细信息，请参阅 **在 Azure 门户中部署工作区**。

## <a name="deploying-dicom-service"></a>部署 DICOM 服务

1. 在 "Azure 门户的" **资源组** "页上，选择" **医疗保健 Api "工作区** 的名称。

   [![选择工作区资源组。 ](media/select-workspace-resource-group.png) ](media/select-workspace-resource-group.png#lightbox)

2. 选择 " **部署 DICOM 服务**"。

   [![部署 dicom 服务。 ](media/workspace-deploy-dicom-services.png) ](media/workspace-deploy-dicom-services.png#lightbox)


3. 选择 " **添加 DICOM 服务**"。

   [![添加 dicom 服务。 ](media/add-dicom-service.png) ](media/add-dicom-service.png#lightbox)


4. 输入 DICOM 服务的名称，然后选择 " **查看 + 创建**"。 

    [![dicom 服务名称。 ](media/enter-dicom-service-name.png) ](media/enter-dicom-service-name.png#lightbox)


    (**可选**) 选择 " **下一步：标记 >**"。

    标记是用于对资源进行分类的名称/值对。 有关标记的信息，请参阅 [使用标记来组织 Azure 资源和管理层次结构](../../azure-resource-manager/management/tag-resources.md)。

5. 注意到绿色验证复选标记时，选择 " **创建** " 以部署 DICOM 服务。

6. 部署过程完成后，选择 " **前往资源**"。  

   [![dicom 中转到资源。 ](media/go-to-resource.png) ](media/go-to-resource.png#lightbox)



   新部署的 DICOM 服务的结果如下所示。

   [![dicom 完成了部署。 ](media/results-deployed-dicom-service.png) ](media/results-deployed-dicom-service.png#lightbox)



## <a name="next-steps"></a>后续步骤

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)






