---
title: Azure 云服务 (扩展支持) 定义架构 (有的文件) |Microsoft Docs
description: '与云服务的定义架构 () 的相关信息 (扩展的支持) '
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ab85067184ebe5b34097a3c81aa521d509ae4b9a
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744257"
---
# <a name="azure-cloud-services-extended-support-definition-schema-csdef-file"></a>Azure 云服务 (扩展支持) 定义架构 (有的文件) 

服务定义文件定义应用程序的服务模型。 文件包含可用于云服务的角色的定义、指定服务终结点并建立服务的配置设置。 配置设置值是在服务配置文件中设置的，如 [云服务 (扩展支持) 配置架构](schema-cscfg-file.md)) 中所述。

默认情况下，Azure 诊断配置架构文件将安装到 `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` 目录。 将 `<version>` 替换为 [Azure SDK](https://www.windowsazure.com/develop/downloads/) 的已安装版本。

服务定义文件的默认扩展名为 "已设置"。

## <a name="basic-service-definition-schema"></a>基本服务定义架构
服务定义文件必须包含一个 `ServiceDefinition` 元素。 服务定义必须包含至少一个角色（`WebRole` 或 `WorkerRole`）元素。 它可以包含在单个定义中定义的最多 25 个角色，并且可以混合使用角色类型。 服务定义还包含可选的 `NetworkTrafficRules` 元素，该元素限制哪些角色可以与指定的内部终结点进行通信。 服务定义还包含可选的 `LoadBalancerProbes` 元素，该元素包含客户为终结点定义的运行状况探测。

服务定义文件的基本格式如下所示。

```xml
<ServiceDefinition name="<service-name>" topologyChangeDiscovery="<change-type>" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" upgradeDomainCount="<number-of-upgrade-domains>" schemaVersion="<version>">
  
  <LoadBalancerProbes>
         …
  </LoadBalancerProbes>
  
  <WebRole …>
         …
  </WebRole>
  
  <WorkerRole …>
         …
  </WorkerRole>
  
  <NetworkTrafficRules>
         …
  </NetworkTrafficRules>

</ServiceDefinition>
```

## <a name="schema-definitions"></a>架构定义
以下主题对架构进行了介绍：

- [LoadBalancerProbe 架构](schema-csdef-loadbalancerprobe.md)
- [WebRole 架构](schema-csdef-webrole.md)
- [WebRole 架构](schema-csdef-workerrole.md)
- [NetworkTrafficRules 架构](schema-csdef-networktrafficrules.md)

##  <a name="servicedefinition-element"></a><a name="ServiceDefinition"></a> Servicedefinition.csdef 元素
`ServiceDefinition` 元素是服务定义文件的顶层元素。

下表介绍了 `ServiceDefinition` 元素的属性。

| Attribute               | 说明 |
| ----------------------- | ----------- |
| name                    |必需。 服务的名称。 该名称在服务帐户中必须是唯一的。|
| topologyChangeDiscovery | 可选。 指定拓扑更改通知的类型。 可能的值包括：<br /><br /> -   `Blast` - 将更新尽早发送到所有角色实例。 如果选择此选项，则角色应该能够在不重启的情况下处理拓扑更新。<br />-   `UpgradeDomainWalk` – 在前一个实例成功接受更新后，按顺序将更新发送到每个角色实例。|
| schemaVersion           | 可选。 指定服务定义架构的版本。 如果并行安装多个版本的 SDK，架构版本允许 Visual Studio 选择正确的 SDK 工具用于架构验证。|
| upgradeDomainCount      | 可选。 指定在其中分配了此服务中的角色的升级域的数目。 在部署服务时会将角色实例分配到升级域。 有关详细信息，请参阅 [更新云服务角色或部署](sample-update-cloud-service.md) 和 [管理虚拟机的可用性](../virtual-machines/manage-availability.md) 您最多可以指定20个升级域。 如果未指定，则升级域的默认数目为 5 个。|

## <a name="see-also"></a>另请参阅

[Azure 云服务 (扩展支持) 配置架构 (.Cscfg 文件) ](schema-cscfg-file.md)。