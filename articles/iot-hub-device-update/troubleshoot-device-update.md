---
title: 排查 Azure IoT 中心的常见设备更新问题 |Microsoft Docs
description: 本文档提供了一些提示和技巧的列表，可帮助解决你在 IoT 中心的设备更新中可能遇到的许多问题。
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: 3c1f60b214397b1f97e0157b5beca32d504102d6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030624"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>IoT 中心的设备更新疑难解答指南

本文档列出了设备更新用户报告的常见问题。 随着设备更新在公共预览版中的进展，此故障排除指南会定期更新，并提供新的问题和解决方案。 如果遇到此故障排除指南中未显示的问题，请参阅 [联系 Microsoft 支持部门](#contact) 部分以记录你的情况。

## <a name="importing-updates"></a><a name="import"></a>导入更新

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>问：将设备更新实例连接到 IoT 中心实例时遇到问题。
_请确保 IoT 中心消息路由的配置正确，如 [设备更新资源](./device-update-resources.md) 文档中所述。_

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>问：我遇到了与角色相关的错误 (Azure 门户出现错误消息或) 403 API 错误。
_您可能没有正确配置访问权限。请确保已根据 [设备更新访问控制](./device-update-control-access.md) 文档正确配置了访问权限。_

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>问：在将内容导入到设备更新服务时遇到500类型的错误。
_500范围内的错误代码可能表示设备更新服务有问题。请等待5分钟，然后重试。如果同样的错误仍然存在，请按照 [联系 Microsoft 支持部门](#contact) 部分中的说明向 Microsoft 提供支持请求。_

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>问：我在导入内容时遇到了错误代码，要对其进行分析。
_有关分析错误代码的信息，请参阅 [设备更新错误代码](./device-update-error-codes.md) 文档。_

## <a name="device-failures"></a><a name="device-failure"></a>设备故障

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>问：如何确保我的设备已连接到 IoT 中心的设备更新？
_可以通过检查 "Azure 门户的" 符合性 "视图中的" 未分组的设备 "部分，验证设备是否已连接到设备更新。_

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>问：我的一个或多个设备更新失败。
_设备更新失败的根本原因有很多。请验证设备是否为： 1) 连接到 IoT 中心实例，2) 连接到设备更新实例，3) 传递优化 () 服务是否正在运行。如果设备满足所有三个条件，请按照 [联系 Microsoft 支持部门](#contact) 部分中的说明向 Microsoft 提供支持请求。_

## <a name="deploying-an-update"></a><a name="deploy"></a> 部署更新

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>问：我已将 (设备的更新部署到) ，但相容性状态指出它不在最新更新上。 应采取何种操作？
_刷新设备符合性状态最多可能需要5分钟。请稍候，然后再次检查。_
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>问：我的设备的部署状态显示不兼容，我该怎么办？
_将设备连接到 IoT 中心后，目标设备的制造商和型号属性可能已更改，导致设备现在被视为与当前部署的更新内容不兼容。_

_检查 [ADU 核心接口](./device-update-plug-and-play.md) ，查看设备向设备更新服务报告的制造商和型号，并确保它与在要部署的更新内容的 [导入清单](./import-concepts.md) 中指定的制造商和型号匹配。你可以使用 [设备更新配置文件](./device-update-configuration-file.md)更改给定设备的这些属性。_

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>问：我看到我的部署处于 "活动" 阶段，但没有 "正在进行" 更新。 应采取何种操作？
_确保你的部署开始日期在将来未设置。当你创建新部署时，除非你显式更改，否则部署开始日期将默认为下一天。你可以等待部署开始日期到达，或取消正在进行的部署并使用所需的开始日期创建新的部署。_

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>问：我尝试将设备分组，但在创建组时，不会在下拉标记中看到标记。
_请确保已按照 [设备更新资源](./device-update-resources.md) 文档在 IoT 中心中正确配置了消息路由。配置路由后，必须再次标记你的设备。_

_另一个根本原因可能是你在将设备连接到 IoT 中心的设备更新之前应用了标记。确保设备已连接到设备更新。可以通过检查设备是否显示在 "符合性" 视图中的 "未分组" 设备下，验证设备是否已连接到 IoT 中心的设备更新。临时添加不同值的标记，然后在设备连接后再次添加所需的标记。_

_如果使用设备预配服务 (DPS) ，请确保在预配设备后（而不是在设备创建过程中）对其进行标记。如果在设备创建步骤中已经标记了设备，则必须在预配设备后使用其他值对其进行临时标记，然后再次添加所需的标记。_

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>问：我的部署已成功完成，但某些设备未能更新。
_这可能是由出现故障的设备上的客户端错误引起的。请参阅此故障排除指南的 "设备故障" 部分。_

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>问：我在尝试启动部署时遇到了 UX 错误。
_这可能是由服务/UX bug 或 API 权限问题导致的。请按照 [联系 Microsoft 支持部门](#contact) 部分中的说明向 Microsoft 提供支持请求。_

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>问：我启动了一个部署，但它没有到达结束状态。
_这可能是由服务性能问题、服务错误或客户端错误引起的。请在10分钟后重试部署。如果遇到相同的问题，请请求设备日志，并参阅此故障排除指南的 "设备故障" 部分。如果同样的问题仍然存在，请按照 [联系 Microsoft 支持部门](#contact) 部分中的说明向 Microsoft 提供支持请求。_

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> 将更新下载到设备上

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>问：当设备在一段断开连接后重新连接时，如何实现恢复下载？
_如果在24小时内恢复连接，下载将自行恢复。24小时后，用户将需要重新启动下载内容。_
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> 使用 Microsoft 连接缓存 (MCC) 

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>问：尝试在 IoT Edge 设备上部署 MCC 模块时遇到问题。
_请参阅 [IoT Edge 文档]() ，以将边缘模块部署到 IoT Edge 设备。您可以通过导航到来检查您的 IoT Edge 设备上的 MCC 模块是否成功运行 http://localhost:5100/Summary 。_
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>问：我的一个 IoT 设备尝试通过 MCC 下载更新，但却失败了。
_有几个问题可能导致 IoT 设备无法连接到 MCC。若要诊断此问题，请从故障设备中收集 DO client and Nginx logs (参阅 [联系 Microsoft 支持部门](#contact) 部分，了解有关收集客户端日志) 的说明。_

_你的设备可能无法从 Internet 请求内容传递到其 MCC 模块，因为不允许使用其所使用的 URL。若要确定是否需要，需要检查 Azure 门户中的 IoT Edge 环境变量。_
## <a name="contacting-microsoft-support"></a><a name="contact"></a> 正在联系 Microsoft 支持部门

如果遇到无法使用上述常见问题解决的问题，可以通过 Azure 门户接口使用 Microsoft 支持部门来处理支持请求。 根据你的问题属于哪个类别，可能会要求你收集和共享其他数据，以帮助 Microsoft 支持部门调查你的问题。 

有关如何收集每个数据类型的说明，请参阅下面的说明。 你可以使用 [getDevices]() 在 API 的负载响应中检查其他信息。

此外，以下信息可用于缩小问题的根本原因：
* 要尝试更新的设备类型 (Azure Percept、IoT Edge 关、其他) 
* 使用的设备更新客户端类型 (基于映像、基于包的模拟器) 
* 设备运行的操作系统
* 有关设备体系结构的详细信息
* 是否已成功使用设备更新在之前更新设备

如果有上述任何信息可用，请将其包含在问题说明中。

### <a name="collecting-client-logs"></a>收集客户端日志

* 在 Raspberry Pi 设备上，可在此处找到两组日志：

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* 对于打包的客户端，可在此处找到日志：

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* 对于模拟器，可以在此处找到日志：

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>错误代码
报告与导入更新、设备故障或部署更新相关的问题时，可能会要求你提供错误代码。

可以通过查看 [ADUCoreInterface](./device-update-plug-and-play.md) 接口获取错误代码。 请参阅 [设备更新错误代码](./device-update-error-codes.md) 文档，以获取有关如何分析错误代码以便自行诊断和排除故障的信息。

### <a name="trace-id"></a>跟踪 ID
报告与导入或部署更新相关的问题时，可能会要求你提供跟踪 ID。

给定用户操作的跟踪 ID 可以在 API 响应中找到，也可以在 Azure 门户用户界面的 "导入历史记录" 部分中找到。 

目前，只能通过 API 响应来访问部署操作的跟踪 Id。

### <a name="deployment-id"></a>部署 ID
报告与部署更新相关的问题时，可能会要求你提供部署 ID。

部署 ID 是在调用 API 来启动部署时由用户创建的。

目前，会自动生成从 Azure 门户用户界面启动的部署的部署 Id，而不会向用户显示。

### <a name="iot-hub-instance-name"></a>IoT 中心实例名称
报告与设备故障有关的问题或部署更新时，系统可能会要求你提供 IoT 中心实例的名称。

IoT 中心名称是用户首次预配时选择的名称。

### <a name="device-update-account-name"></a>设备更新帐户名称
报告与导入更新、设备故障或部署更新相关的问题时，可能会要求你提供设备更新帐户的名称。

第一次注册服务时，用户会选择设备更新帐户名称。 有关详细信息，请参阅 [设备更新资源](./device-update-resources.md) 文档。

### <a name="device-update-instance-name"></a>设备更新实例名称
报告与导入更新、设备故障或部署更新相关的问题时，可能会要求你提供设备更新实例的名称。

首次预配时，用户会选择设备更新实例名称。 有关详细信息，请参阅 [设备更新资源](./device-update-resources.md) 文档。

### <a name="device-id"></a>设备 ID
报告与设备故障有关的问题或部署更新时，系统可能会要求你提供设备 ID。

设备 ID 是在第一次预配设备时由客户定义的。 还可以从设备的设备克隆中检索它。

### <a name="update-id"></a>更新 ID
报告与部署更新相关的问题时，可能会要求你提供更新 ID。

更新 ID 由客户在启动部署时定义。

### <a name="nginx-logs"></a>Nginx 日志
报告与 Microsoft 连接缓存相关的问题时，可能会要求你提供 Nginx 日志。

### <a name="adu-conftxt"></a>ADU-conf.txt
报告与部署更新相关的问题时，可能会要求你提供设备更新配置文件 ( "adu-conf.txt" ) 。

配置文件是可选的，由用户按照 [设备更新配置](./device-update-configuration-file.md) 文档中的说明创建。

### <a name="import-manifest"></a>导入清单
报告与导入或部署更新相关的问题时，可能会要求你提供导入清单文件。

导入清单是客户在将更新内容导入到设备更新服务时创建的文件。

**[下一步：了解有关设备更新错误代码的详细信息](.\device-update-error-codes.md)**