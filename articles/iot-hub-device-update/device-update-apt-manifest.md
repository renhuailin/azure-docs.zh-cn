---
title: 了解 Azure IoT 中心的设备更新 APT 清单 |Microsoft Docs
description: 了解 IoT 中心的设备更新如何使用 apt 清单进行基于包的更新。
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b68b78499aa3bf0d84d8bd0fa5ab55d1f969113
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679119"
---
# <a name="device-update-apt-manifest"></a>设备更新 APT 清单

APT 清单是一种 JSON 文件，用于描述 APT 更新处理程序所需的更新详细信息。 此文件可以导入到 IoT 中心的设备更新中，就像任何其他更新一样。

[了解](import-update.md) 有关将更新导入到设备更新的详细信息。

## <a name="overview"></a>概述

将 APT 清单作为更新传送到设备更新代理后，代理将处理清单并执行所需的操作。 这些操作包括下载和安装 APT 清单文件中指定的包及其依赖项。

设备更新支持 APT UpdateType 和 APT 更新处理程序。 此支持允许设备更新代理评估已安装的 Debian 包，并更新所需的包。 

## <a name="schema"></a>架构

APT 清单文件是具有版本控制架构的 JSON 文件。

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

示例：

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

此 APT 清单的名称。 这可以是任何名称或 ID 对于你的方案都有意义。 例如 `contoso-iot-edge`。

### <a name="version"></a>version

此 APT 清单的版本号。 例如 `1.0.0.0`。


### <a name="packages"></a>packages

包含特定于包的属性的对象的列表。

#### <a name="name"></a>name

包的名称或 ID。 例如 `iotedge`。

#### <a name="version"></a>version

包的所需版本条件。 例如 `1.0.8-2`。

目前仅支持准确版本号。 版本号是 [epoch：] upstream_version [-debian_revision] 格式所需的 Debian 包版本。

**epoch** 是一个无符号整数。

**upstream_version** 可以包含字母数字和字符，如 "."、"+"、"-" 和 "~"。 应以数字开头。
> [!NOTE]
> "1.0.8" 等于 "1.0.8-0"

例如， **`"name":"iotedge" and "version":"1.0.8-2"`** 等效于使用命令安装包 `apt-get install iotedge=1.0.8-2`

> [!NOTE]
> 版本值不包含等号

如果省略版本，将安装指定的包的最新可用版本。

[详细了解](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) 如何对 Debian 包进行版本控制。

> [!NOTE]
> 当自动解析要安装的相关程序包时，APT 程序包管理器将忽略由包提供的版本控制要求。 除非为依赖包的显式版本提供，否则它们将使用最新的，即使包本身可以在给定版本上指定严格要求 (=) 。 此自动解决方法可能会导致有关不符合依赖项的错误。 [了解详细信息](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

如果要更新 Azure IoT Edge security daemon 的特定版本，则应 `iotedge` `libiothsm-std` 在 APT 清单中包含包及其依赖包的所需版本。
[了解详细信息](https://docs.microsoft.com/azure/iot-edge/how-to-update-iot-edge#update-the-security-daemon)

> [!NOTE]
> Apt 清单可用于更新设备更新代理及其依赖项。 在 apt 清单中列出设备更新代理名称和所需版本，就像对任何其他包一样。 然后，可以通过 IoT 中心管道的设备更新导入和部署此 apt 清单。 

## <a name="removing-packages"></a>删除包

还可以使用 apt 清单从设备中删除已安装的程序包。 单个 apt 清单可用于删除、添加和更新多个包。 若要删除某个包，请在包名称后面添加一个减号 "-"。 不应为要删除的包包含版本号。 通过 apt 清单删除包不会删除其依赖项和配置。

示例：

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
此 apt 清单将从部署包)  (的设备中删除包 "foo"。 

## <a name="recommended-value-for-installed-criteria"></a>建议安装的条件值

APT 清单的已安装条件是 `<name>-<version>` `<name>` APT 清单的名称， `<version>` 是 APT 清单的版本。 例如 `contoso-iot-edge-1.0.0.0`。 

## <a name="guidelines-on-creating-an-apt-manifest"></a>有关创建 APT 清单的准则

创建 APT 清单时，需要牢记一些准则：

- 请始终确保 APT 清单是格式正确的 json 文件
- 每个 APT 清单都应具有唯一的版本。 尝试使用标准化的方法来递增 APT 清单的版本，使其适合你的方案，并可以轻松地遵循
- 对于每个单独包的所需状态，请指定要在设备上安装的包的确切名称和版本。 始终针对要用作包源的包存储库验证值
- 确保 APT 清单中的包按应安装/删除的顺序列出。
- 在测试设备上始终验证包的安装，以确保结果是必需的
- 安装包的特定版本时 (例如 `iotedge 1.0.9-1`) ，最佳做法是在 APT 清单中安装要安装的依赖包的显式版本 (例如， `libiothsm 1.0.9-1`) 
- 尽管这并不是强制性的，但请务必确保 APT 清单具有累积性，以避免设备进入未知状态。 累积更新将确保你的设备具有所需的每个包的所需版本，即使设备由于安装失败或脱机而跳过了 APT 更新部署

例如：

**基本 APT 清单**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**更新错误**

此更新包括条形包，但不包括 foo 包。

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**良好更新**

此更新包括 foo 包，并且还包括 bar package。

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [导入新更新](import-update.md)

