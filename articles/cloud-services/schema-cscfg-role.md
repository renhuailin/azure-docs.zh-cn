---
title: Azure 云服务 (经典) 角色架构 |Microsoft Docs
description: 服务配置文件的 Role 元素指定要为每个角色、配置值和证书指纹部署多少个角色实例。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2dc8e14a4e4d8855abb615632bb7d43b9034d360
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743434"
---
# <a name="azure-cloud-services-classic-config-role-schema"></a>Azure 云服务 (经典) Config 角色架构

> [!IMPORTANT]
> [Azure 云服务 (扩展支持) ](../cloud-services-extended-support/overview.md) 是适用于 Azure 云服务产品的新的基于 azure 资源管理器的部署模型。进行此更改后，基于 Azure Service Manager 的部署模型运行的 Azure 云服务已重命名为云服务 (经典) ，所有新部署应使用 [云服务 (扩展支持) ](../cloud-services-extended-support/overview.md)。

配置文件的 `Role` 元素指定要为服务中的每个角色部署的角色实例数，任何配置设置的值，以及与角色关联的任何证书的缩略图。

有关 Azure 服务配置架构的详细信息，请参阅[云服务（经典）配置架构](schema-cscfg-file.md)。 有关 Azure 服务定义架构的详细信息，请参阅[云服务（经典）定义架构](schema-csdef-file.md)。

##  <a name="role-element"></a><a name="Role"></a> Role 元素
下面的示例显示了 `Role` 元素及其子元素。

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

下表介绍了 `Role` 元素的属性。

| Attribute | 说明 |
| --------- | ----------- |
| name   | 必需。 指定角色的名称。 该名称必须与在服务定义文件中为角色提供的名称匹配。|
| vmName | 可选。 指定虚拟机的 DNS 名称。 该名称不能超过 10 个字符。|

下表介绍了 `Role` 元素的子元素。

| 元素 | 说明 |
| ------- | ----------- |
| 实例数 | 必需。 指定要为角色部署的实例数。 实例数是由用于 `count` 属性的整数定义的。|
| 设置   | 可选。 在角色的设置集合中指定设置名称和值。 设置名称是由用于 `name` 属性的字符串定义的，设置值是由用于 `value` 属性的字符串定义的。|
| 证书 | 可选。 指定要与角色关联的服务证书的名称、指纹和算法。 证书名称是由用于 `name` 属性的字符串定义的。 证书指纹是由用于 `thumbprint` 属性的一串不含空格的十六进制数字定义的。 必须使用数字和大写字母字符来表示十六进制数字。 证书算法是由用于 `thumbprintAlgorithm` 属性的字符串定义的。|

## <a name="see-also"></a>另请参阅
[云服务（经典）配置架构](schema-cscfg-file.md)