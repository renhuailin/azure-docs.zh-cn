---
title: Azure 云服务（经典）定义架构（.cscfg 文件）| Microsoft Docs
description: 服务配置 (.cscfg) 文件指定要为每个角色部署的角色实例数、配置值和角色的证书指纹。
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: dd4945d38516ba36140ca098a84a37c92f98bdee
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113088433"
---
# <a name="azure-cloud-services-classic-config-schema-cscfg-file"></a>Azure 云服务（经典）配置架构（.cscfg 文件）

> [!IMPORTANT]
> [Azure 云服务（外延支持）](../cloud-services-extended-support/overview.md)是 Azure 云服务产品基于 Azure 资源管理器的新型部署模型。 进行此更改后，在基于 Azure 服务管理器的部署模型上运行的 Azure 云服务已重命名为云服务（经典），所有新部署都应使用[云服务（外延支持）](../cloud-services-extended-support/overview.md)。

服务配置文件指定要为服务中每个角色部署的角色实例数、任何配置设置的值，以及与角色关联的任何证书的缩略图。 如果服务属于虚拟网络，必须在服务配置文件以及虚拟网络配置文件中提供网络的配置信息。 服务配置文件的默认扩展名为 .csdef。

通过[云服务（经典）定义架构](schema-csdef-file.md)描述服务模型。

默认情况下，Azure 诊断配置架构文件将安装到 `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` 目录。 将 `<version>` 替换为 [Azure SDK](https://azure.microsoft.com/downloads/) 的已安装版本。

有关在服务中配置角色的详细信息，请参阅[什么是云服务模型](cloud-services-model-and-package.md)。

## <a name="basic-service-configuration-schema"></a>基本服务配置架构
服务配置文件的基本格式如下所示。

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>架构定义
以下主题介绍 `ServiceConfiguration` 元素的架构：

- [Role 架构](schema-cscfg-role.md)
- [NetworkConfiguration 架构](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>服务配置命名空间
服务配置文件的 XML 命名空间是：`http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`。

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a> ServiceConfiguration 元素
`ServiceConfiguration` 元素是服务配置文件的顶层元素。

下表介绍了 `ServiceConfiguration` 元素的属性。 所有属性值均为字符串类型。

| Attribute | 说明 |
| --------- | ----------- |
|serviceName|必需。 云服务的名称。 此处提供的名称需要匹配服务定义文件中指定的名称。|
|osFamily|可选。 指定要在云服务的角色实例上运行的来宾 OS。 有关支持的来宾 OS 版本的信息，请参阅 [Azure 来宾 OS 版本和 SDK 兼容性对照表](cloud-services-guestos-update-matrix.md)。<br /><br /> 如果不包含 `osFamily` 值，并且未将 `osVersion` 属性设置为特定的来宾 OS 版本，将使用默认值 1。|
|osVersion|可选。 指定要在云服务的角色实例上运行的来宾 OS 版本。 有关来宾 OS 版本的详细信息，请参阅 [Azure 来宾 OS 版本和 SDK 兼容性对照表](cloud-services-guestos-update-matrix.md)。<br /><br /> 可以指定应自动将来宾 OS 升级到最新版本。 若要执行此操作，将 `osVersion` 属性的值设为 `*`。 如果设置为 `*`，将使用指定 OS 系列的最新来宾 OS 版本部署角色实例，并且在发布新版来宾 OS 后自动进行升级。<br /><br /> 若要手动指定特定版本，请使用来自 [Azure 来宾 OS 版本和 SDK 兼容性对照表](cloud-services-guestos-update-matrix.md)的“将来、当前和过渡来宾 OS 版本”部分的表格中的 `Configuration String`。<br /><br /> `osVersion` 属性的默认值为 `*`。|
|schemaVersion|可选。 指定服务配置架构的版本。 如果并行安装多个版本的 SDK，架构版本允许 Visual Studio 选择正确的 SDK 工具用于架构验证。 有关架构和版本兼容性的信息，请参阅 [Azure 来宾 OS 版本和 SDK 兼容性对照表](cloud-services-guestos-update-matrix.md)|

服务配置文件必须包含一个 `ServiceConfiguration` 元素。 `ServiceConfiguration` 元素可包含任意数量的 `Role` 元素或 0 到 1 个 `NetworkConfiguration` 元素。