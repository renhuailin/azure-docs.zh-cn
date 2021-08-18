---
title: Azure 媒体服务概述 | Microsoft Docs
description: Microsoft Azure 媒体服务是一个可扩展的基于云的平台，使开发人员能够生成可缩放的媒体管理和传送应用程序。 本文概述了 Azure 媒体服务。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 1e394c29d1f1a707b30b8d04568ff92421e2916f
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114712736"
---
# <a name="azure-media-services-overview"></a>Azure 媒体服务概述

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [v2 deprecation notice](../latest/includes/v2-deprecation-notice.md)]

Microsoft Azure 媒体服务 (AMS) 是一个可扩展的基于云的平台，使开发人员能够生成可缩放的媒体管理和传送应用程序。 媒体服务基于 REST API，使用这些 API 可以安全地上传、存储、编码和打包视频或音频内容，以供点播以及以实时传送视频流的形式传送到各种客户端（例如，电视、电脑和移动设备）。

可以完全使用媒体服务构建端到端工作流。 也可以选择使用第三方组件来构建工作流的某些组成部分。 例如，使用第三方编码器进行编码。 然后，使用媒体服务进行上传、保护、打包和传送。 可以选择实时流式播放你的内容，或者根据点播情况交付内容。 


## <a name="compliance-privacy-and-security"></a>符合性、隐私和安全性

需要重点提醒的是，在使用 Azure 媒体服务时，你必须遵守所有适用法律，不得以侵犯他人权利或可能对他人有害的方式使用媒体服务或任何 Azure 服务。

在将任何视频/图像上传到媒体服务之前，必须拥有该视频/图像的适当使用权限，包括根据法律的要求，获得视频/图像中的个人（如果有）授予的，在媒体服务和 Azure 中使用、处理和存储其数据的所有必要许可。 某些司法辖区可能会对收集、在线处理和存储某些类别的数据（例如生物识别数据）施加特殊的法律要求。 在根据特殊法律要求使用媒体服务和 Azure 处理与存储任何数据之前，必须确保符合可能适用于你的任何法律要求。

若要了解媒体服务中的合规性、隐私性和安全性，请访问 Microsoft [信任中心](https://www.microsoft.com/trust-center/?rtc=1)。 若要了解 Microsoft 的隐私义务、数据处理和保留惯例，包括如何删除数据，请查看 Microsoft 的[隐私声明](https://privacy.microsoft.com/PrivacyStatement)、[联机服务条款](https://www.microsoft.com/licensing/product-licensing/products?rtc=1)（“OST”）和[数据处理附录](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)（“DPA”）。 使用媒体服务即表示你同意遵守 OST、DPA 和隐私声明。
 
## <a name="prerequisites"></a>先决条件

若要开始使用 Azure 媒体服务，应该具备以下条件：

* 一个 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com)。
* Azure 媒体服务帐户。 有关详细信息，请参阅[创建帐户](media-services-portal-create-account.md)。
* （可选）设置开发环境。 为开发环境选择“.NET”或“REST API”。 有关详细信息，请参阅 [设置环境](media-services-dotnet-how-to-use.md)。

    此外，请学习如何[以编程方式连接到 AMS API](media-services-use-aad-auth-to-access-ams-api.md)。
* 处于已启动状态的标准或高级流式处理终结点。  有关详细信息，请参阅[管理流式处理终结点](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDK 和工具

要构建媒体服务解决方案，可以使用：

* [媒体服务 REST API](/rest/api/media/operations/azure-media-services-rest-api-reference)
* 可用的客户端 SDK 之一：
    * 适用于 .NET 的 Azure 媒体服务 SDK
    
        * [NuGet 包](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [GitHub 源代码](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK for Java](https://github.com/Azure/azure-sdk-for-java)，
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)，
    * [适用于 Node.js 的 Azure 媒体服务](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) （这是 Node.js SDK 的非 Microsoft 版本。 它由社区维护，当前未包括所有的 AMS API）。
* 现有工具：
    * [Azure 门户](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) （Azure 媒体服务资源管理器 (AMSE) 是适用于 Windows 的 Winforms/C# 应用程序）

> [!NOTE]
> 若要获取最新版本的 Java SDK 并开始使用 Java 进行开发，请参阅[媒体服务的 Java 客户端 SDK 入门](./media-services-java-how-to-use.md)。 <br/>
> 若要下载最新的媒体服务 PHP SDK，请在 [Packagist 存储库](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)中查找 0.5.7 版 Microsoft/WindowAzure 包。  

## <a name="code-samples"></a>代码示例

在“Azure 代码示例”库中查找多个代码示例：[Azure 媒体服务代码示例](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0)。

## <a name="concepts"></a>概念

有关 Azure 媒体服务的概念，请参阅 [概念](media-services-concepts.md)。

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>支持的媒体服务功能跨数据中心方案和可用性

有关 Azure 常见方案的详细信息，请参阅 [AMS 方案](scenarios-and-availability.md)。
有关区域可用性的信息，请参阅[媒体服务可用性](availability-regions-v-2.md)。

## <a name="service-level-agreement-sla"></a>服务级别协议 (SLA)

有关详细信息，请参阅 [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)。

## <a name="support"></a>支持

[Azure 支持](https://azure.microsoft.com/support/options/) 为 Azure（包括媒体服务）提供支持选项。

## <a name="provide-feedback"></a>提供反馈

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
