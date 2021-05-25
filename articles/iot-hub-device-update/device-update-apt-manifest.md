---
title: 了解 Device Update for Azure IoT Hub APT 清单 | Microsoft Docs
description: 了解 Device Update for IoT Hub 如何使用 APT 清单进行基于包的更新。
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 878748fcfc9b096e340b53c06969962af99f603f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561162"
---
# <a name="device-update-apt-manifest"></a>Device Update APT 清单

APT 清单是 JSON 文件，用于描述 APT 更新处理程序所需的更新详细信息。 此文件可以导入到 Device Update for IoT Hub 中，就像其他任何更新一样。

[详细了解](import-update.md)如何将更新导入到 Device Update。

## <a name="overview"></a>概述

当 APT 清单作为更新传递到 Device Update 代理时，代理会处理清单，并执行必要操作。 这些操作包括下载并安装 APT 清单文件中指定的包及其依赖项。

Device Update 支持 APT UpdateType 和 APT 更新处理程序。 借助这种支持，Device Update 代理可以评估已安装的 Debian 包，并更新必要的包。 

## <a name="schema"></a>架构

APT 清单文件是包含版本控制架构的 JSON 文件。

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

此 APT 清单的名称。 这可以是任何对你的方案有意义的名称或 ID。 例如，`contoso-iot-edge`。

### <a name="version"></a>版本

此 APT 清单的版本号。 例如，`1.0.0.0`。


### <a name="packages"></a>packages

包含特定于包的属性的对象列表。

#### <a name="name"></a>name

包的名称或 ID。 例如，`iotedge`。

#### <a name="version"></a>版本

包所需的版本条件。 例如，`1.0.8-2`。

目前只支持准确的版本号。 版本号是所需的 Debian 包版本，格式为 [epoch:]upstream_version[-debian_revision]。

epoch 是无符号整数。

upstream_version 可以包含字母数字和“.”、“+”、“-”、“~”等字符。 它应以数字开头。
> [!NOTE]
> “1.0.8”等于“1.0.8-0”

例如，`"name":"iotedge" and "version":"1.0.8-2"` 相当于使用命令 `apt-get install iotedge=1.0.8-2` 安装包

> [!NOTE]
> 版本值不包含等于号

如果省略版本，则安装指定包的最新可用版本。

[详细了解](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version)如何对 Debian 包进行版本控制。

> [!NOTE]
> 当要安装的从属包被自动解析时，APT 包管理器会忽略由包给出的版本控制要求。 除非给出从属包的显式版本，否则它们将使用最新版本，即使包本身可能对给定版本指定了严格的要求 (=)，也不例外。 这种自动解析可能会导致有关未满足的依赖项的错误。 [了解详细信息](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

若要更新 Azure IoT Edge 安全守护程序的特定版本，则应在 APT 清单中包含所需版本的 `iotedge` 包及其从属 `libiothsm-std` 包。
[了解详细信息](../iot-edge/how-to-update-iot-edge.md#update-the-security-daemon)

> [!NOTE]
> APT 清单可用于更新 Device Update 代理及其依赖项。 在 APT 清单中列出 Device Update 代理名称和所需的版本，就像对其他任何包所做的那样。 然后，可以通过 Device Update for IoT Hub 管道导入并部署此 APT 清单。 

## <a name="removing-packages"></a>删除包

还可以使用 APT 清单从设备中删除已安装的包。 一个 APT 清单可用于删除、添加和更新多个包。 若要删除包，请在包名称后面添加一个减号“-”。 不得包含要删除的包的版本号。 通过 APT 清单删除包并不会删除其依赖项和配置。

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
此 APT 清单从包“foo”部署到的设备中删除它。 

## <a name="recommended-value-for-installed-criteria"></a>已安装条件的建议值

APT 清单的已安装条件是 `<name>-<version>`，其中 `<name>` 是 APT 清单的名称，`<version>` 是 APT 清单的版本。 例如，`contoso-iot-edge-1.0.0.0`。 

## <a name="guidelines-on-creating-an-apt-manifest"></a>创建 APT 清单的准则

创建 APT 清单时，需要牢记一些准则：

- 请始终确保 APT 清单是格式标准的 json 文件
- 每个 APT 清单都应有唯一版本。 尝试提出标准化方法来递增 APT 清单版本，使其对你的方案有意义，并且可以很容易地遵循
- 当涉及到每个单独包的所需状态时，指定要在设备上安装的包的确切名称和版本。 始终根据要用作包源的包存储库来验证值
- 确保 APT 清单中的包按照预期的安装/删除顺序列出
- 始终在测试设备上验证包的安装，以确保实现预期结果
- 当安装特定版本的包时（例如 `iotedge 1.0.9-1`），根据最佳做法，最好也在 APT 清单中包含要安装的从属包的显式版本（例如 `libiothsm 1.0.9-1`）
- 虽然这不是强制的，但一定要确保 APT 清单是累积的，以避免设备进入未知状态。 累积更新将确保设备有你关注的每个包的所需版本，即使设备由于安装失败或脱机而跳过了 APT 更新部署，也不例外

例如：

基础 APT 清单

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

不良更新

此更新包括 bar 包，但不包括 foo 包。

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

良好更新

此更新既包括 foo 包，也包括 bar 包。

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
> [导入新的更新](import-update.md)