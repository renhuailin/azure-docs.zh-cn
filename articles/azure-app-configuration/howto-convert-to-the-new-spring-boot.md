---
title: 转换为 Spring Boot 库
titleSuffix: Azure App Configuration
description: 了解如何从以前的版本转换为新的应用程序配置 Spring Boot 库。
ms.service: azure-app-configuration
author: mrm9084
ms.author: mametcal
ms.topic: how-to
ms.date: 07/08/2021
ms.openlocfilehash: 1bff486cb226de6bb9b6c8a0f065dbca134bd684
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113552992"
---
# <a name="convert-to-new-app-configuration-spring-boot-library"></a>转换为新的应用程序配置 Spring Boot 库

现在有新版本的应用程序配置库适用于 Spring Boot。 该版本引入了推送刷新等新功能，同时也引入了一些中断性变更。 这些变更与使用以前的库版本的配置设置不向后兼容。 对于下列主题：

* 组和项目 ID
* Spring 配置文件
* 配置加载和重新加载
* 功能标志加载

本文提供有关迁移到新库版本所需的更改和操作的参考。

## <a name="group-and-artifact-id-changed"></a>组和项目 ID 已更改

所有 Azure Spring Boot 库已更新它们的组和项目 ID，以匹配新格式。 新的包名称为：

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
```

## <a name="use-of-spring-profiles"></a>Spring 配置文件的使用

在之前的版本中，Spring 配置文件用作配置的一部分，以确保它们与配置文件的格式匹配。 例如，

```properties
/<application name>_dev/config.message
```

这已更改，因此查询的默认标签是具有以下格式的 Spring 配置文件，其标签与 Spring 配置文件匹配：

```properties
/application/config.message
```

 若要转换为新格式，可以使用存储名称运行以下命令：

```azurecli
az appconfig kv export -n your-stores-name -d file --format properties --key /application_dev* --prefix /application_dev/ --path convert.properties --skip-features --yes
az appconfig kv import -n your-stores-name -s file --format properties --label dev --prefix /application/ --path convert.properties --skip-features --yes
```

或使用门户中的“导入”/“导出”功能。

完全移动到新版本后，可以通过运行以下命令来删除旧密钥：

```azurecli
az appconfig kv delete -n ConversionTest --key /application_dev/*
```

此命令将列出要删除的所有密钥，以确保不会删除意外的密钥。 还可以在门户中删除密钥。

## <a name="which-configurations-are-loaded"></a>加载了哪些配置

加载配置匹配 `/applicaiton/*` 的默认情况未更改。 所做的更改是，除非设置，否则不再自动额外使用 `/${spring.application.name}/*`。 相反，若要使用 `/${spring.application.name}/*`，可以使用新的 Selects 配置。

```properties
spring.cloud.azure.appconfiguration.stores[0].selects[0].key-filter=/${spring.application.name}/*
```

## <a name="configuration-reloading"></a>配置重新加载

现在默认禁用所有配置存储的监视。 向库添加了一个新配置，以允许配置存储启用监视。 此外，缓存过期时间已重命名为刷新间隔，并且已更改为因每个配置存储而不同。 此外，如果启用了对配置存储的监视，则至少需要配置一个带有可选标签的监视密钥。

```properties
spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled
spring.cloud.azure.appconfiguration.stores[0].monitoring.refresh-interval
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].key
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].label
```

刷新间隔的工作方式没有变化，更改将重命名配置以阐明功能。 受监视密钥的要求确保在更改配置时，库不会尝试加载配置，直到完成所有更改。

## <a name="feature-flag-loading"></a>功能标志加载

现在，功能标志的加载在默认情况下禁用。 此外，功能标志现在具有标签筛选器和刷新间隔。

```properties
spring.cloud.azure.appconfiguration.stores[0].feature-flags.enable
spring.cloud.azure.appconfiguration.stores[0].feature-flags.label-filter
spring.cloud.azure.appconfiguration.stores[0].monitoring.feature-flag-refresh-interval
```
