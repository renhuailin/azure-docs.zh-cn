---
title: 排查 Azure IoT 中心设备更新的常见问题 | Microsoft Docs
description: 本文档列出了一些提示和技巧，可帮助你解决有关 IoT 中心设备更新可能出现的许多问题。
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: f5ea8cfe1df2ae89bb67675c9bf235d62dca4bf5
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110082065"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>IoT 中心设备更新故障排除指南

本文档列出了设备更新用户报告的一些常见问题。 设备更新为公共预览版，因此会定期更新本故障排除指南，使其包含新的问题和解决方案。 如果遇到本故障排除指南中未出现的问题，请参阅[联系 Microsoft 支持部门](#contact)部分以记录你的情况。

## <a name="importing-updates"></a><a name="import"></a>导入更新

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>问：将设备更新实例连接到 IoT 中心实例时遇到问题。
_请确保根据 [设备更新资源](./device-update-resources.md)文档正确配置 IoT 中心消息路由。_

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>问：遇到与角色相关的错误（Azure 门户出现错误消息或 403 API 错误）。
_未正确配置访问权限。请确保根据 [设备更新访问控制](./device-update-control-access.md)文档正确配置访问权限。_

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>问：将内容导入设备更新服务时遇到 500 类型的错误。
_500 范围的错误代码表示设备更新服务可能出现问题。请等待 5 分钟，然后重试。如果相同错误仍然存在，请按照 [联系 Microsoft 支持部门](#contact)部分中的说明向 Microsoft 提出支持请求。_

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>问：导入内容时遇到错误代码，想要对其进行分析。
_有关分析错误代码的信息，请参阅 [设备更新错误代码](./device-update-error-codes.md)文档。_

## <a name="device-failures"></a><a name="device-failure"></a>设备故障

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>问：如何确保我的设备已连接到 IoT 中心设备更新？
_通过检查设备是否在 Azure 门户合规性视图的“未分组”设备部分下显示，来验证设备是否已连接到设备更新。_

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>问：一个或多个设备更新失败。
_设备更新失败可能存在许多根本原因。请验证此设备：1) 是否连接到 IoT 中心实例，2) 是否连接到设备更新实例，以及 3) 传递优化 (DO) 服务是否正在运行。如果设备满足这三个条件，请按照 [联系 Microsoft 支持部门](#contact)部分中的说明向 Microsoft 提出支持请求。_

## <a name="deploying-an-update"></a><a name="deploy"></a>部署更新

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>问：我已将更新部署到设备，但合规性状态指出设备未使用最新更新。 应采取何种操作？
_设备合规性状态最多需要 5 分钟才会刷新。请等待，然后重新检查。_
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>问：设备部署状态显示不兼容，我该怎么办？
_将目标设备连接到 IoT 中心后，设备的制造商和型号属性可能已更改，导致设备现在被视为与当前部署的更新内容不兼容。_

_检查 [ADU Core 接口](./device-update-plug-and-play.md)查看设备报告给设备更新服务的制造商和型号，确保其与正在部署的更新内容的 [导入清单](./import-concepts.md)中指定的制造商和型号相匹配。你可以使用 [设备更新配置文件](./device-update-configuration-file.md)更改给定设备的这些属性。_

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>问：看到部署处于“活动”阶段，但没有任何设备“正在进行”更新。 应采取何种操作？
_请确保部署的开始日期是否未设置为将来的日期。为安全起见，创建新部署时，部署的开始日期默认为第二天，除非进行显式更改。你可等待部署开始日期到来，也可取消正在进行的部署，并创建具有所需开始日期的新部署。_

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>问：尝试将设备分组，但在创建组时，在下拉菜单中没有看到标记。
_请确保根据 [设备更新资源](./device-update-resources.md)文档在 IoT 中心中正确配置消息路由。配置路由后，必须再次对设备进行标记。_

_另一个根本原因可能是，在将设备连接到 IoT 中心设备更新之前已应用了标记。请确保已将设备连接到设备更新。通过检查设备是否在合规性视图的“未分组”设备下显示，来验证设备是否已连接到 IoT 中心设备更新。临时添加一个其他值的标记，然后在连接设备后再次添加预期标记。_

_如果使用的是设备预配服务 (DPS)，请确保在预配设备之后标记设备，而不是在设备创建过程中。如果已在设备创建步骤期间标记设备，则必须在设备预配后使用其他值临时标记设备，然后再次添加预期标记。_

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>问：部署已成功完成，但某些设备更新失败。
_这可能是由失败的设备上的客户端错误引起。请参阅本故障排除指南的“设备故障”部分。_

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>问：尝试启动部署时遇到 UX 错误。
_这可能是由服务/UX bug 或 API 权限问题引起的。请按照 [联系 Microsoft 支持部门](#contact)部分中的说明向 Microsoft 提出支持请求。_

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>问：启动了部署，但未完成。
_这可能是由服务性能问题、服务 bug 或客户端 bug 引起的。请在 10 分钟后重新尝试部署。如果遇到相同问题，请拉取设备日志并参阅本故障排除指南的“设备故障”部分。如果相同问题仍然存在，请按照 [联系 Microsoft 支持部门](#contact)部分中的说明向 Microsoft 提出支持请求。_

### <a name="q-i-migrated-from-a-device-level-agent-to-adding-the-agent-as-a-module-identity-on-the-device-and-my-update-shows-as-in-progress-even-though-it-has-been-applied-to-the-device"></a>问：我从设备级别代理进行了迁移，现在可以在设备上将代理添加为模块标识。我的更新显示为“正在进行中”，虽然它已应用到了设备。
如果你未删除通过设备孪生通信的较旧代理，则可能会导致此问题。 将 Device Update 代理预配为模块（参见[操作方法](device-update-agent-provisioning.md)）时，设备和 Device Update 服务之间的所有通信都通过模块孪生进行。因此，请记住：在创建[组](device-update-groups.md)时标记设备的模块孪生；所有[通信](device-update-plug-and-play.md)都必须通过模块孪生进行。

## <a name="downloading-updates-onto-devices"></a><a name="download"></a>将更新下载到设备

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>问：当设备断开连接一段时间后重新连接时，如何恢复下载？
_恢复连接后 24 小时内将自行恢复下载。24 小时后，将需要用户重新启动下载。_
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a>使用 Microsoft 联网缓存 (MCC)

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>问：尝试在 IoT Edge 设备上部署 MCC 模块时遇到问题。
_请参阅 [IoT Edge 文档]()了解如何将 Edge 模块部署到 IoT Edge 设备。可以通过导航到 http://localhost:5100/Summary ，检查 IoT Edge 设备上是否正在成功运行 MCC 模块。_
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>问：其中一个 IoT 设备尝试通过 MCC 下载更新，但失败了。
_有多个问题可能导致 IoT 设备无法连接到 MCC。为对问题做出诊断，请从失败的设备中收集 DO 客户端和 Nginx 的日志（有关收集客户端日志的说明，请参阅 [联系 Microsoft 支持部门](#contact)部分。）_

_设备可能无法从 Internet 拉取内容以传递到其 MCC 模块，因为它使用的 URL 未获得允许。若要确定是否如此，需要在 Azure 门户中检查 IoT Edge 环境变量。_
## <a name="contacting-microsoft-support"></a><a name="contact"></a>联系 Microsoft 支持部门

如果遇到使用上述 FAQ 无法解决的问题，可以通过 Azure 门户接口向 Microsoft 支持部门提出支持请求。 根据你指出的问题所属类别，你可能需要收集和共享其他数据，以帮助 Microsoft 支持部门调查你的问题。 

请参阅以下说明了解如何收集每种数据类型。 你可以使用 [getDevices]() 以在 API 的有效负载响应中检查其他信息。

此外，以下信息有助于确定问题的根本原因：
* 尝试进行更新的设备类型（Azure Percept、IoT Edge 网关等）
* 使用的设备更新客户端类型（基于映像、基于包、模拟器）
* 设备运行的 OS
* 有关设备体系结构的详细信息
* 曾经是否成功使用设备更新来更新过设备

如果可以提供上述任何信息，请将其包含在问题说明中。

### <a name="collecting-client-logs"></a>收集客户端日志

* 在 Raspberry Pi 设备上，可在以下位置找到两组日志：

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* 对于打包的客户端，可在以下位置找到日志：

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* 对于模拟器，可在以下位置找到日志：

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>错误代码
报告有关导入更新、设备故障或部署更新的问题时，你可能需要提供错误代码。

通过查看 [ADUCoreInterface](./device-update-plug-and-play.md) 接口，可以获取错误代码。 有关如何分析错误代码以便自行诊断和排除故障的信息，请参阅[设备更新错误代码](./device-update-error-codes.md)文档。

### <a name="trace-id"></a>跟踪 ID
报告有关导入或部署更新的问题时，你可能需要提供跟踪 ID。

在 API 响应或 Azure 门户用户界面的“导入历史记录”部分中，可以找到给定用户操作的跟踪 ID。 

目前，仅能通过 API 响应来访问部署操作的跟踪 ID。

### <a name="deployment-id"></a>部署 ID
报告有关部署更新的问题时，你可能需要提供部署 ID。

部署 ID 是用户在调用 API 来启动部署时创建的。

目前，从 Azure 门户用户界面启动的部署的部署 ID 会自动生成，且不会向用户显示。

### <a name="iot-hub-instance-name"></a>IoT 中心实例名称
报告有关设备故障或部署更新的问题时，你可能需要提供 IoT 中心实例的名称。

IoT 中心名称是用户在首次预配时选择的名称。

### <a name="device-update-account-name"></a>设备更新帐户名称
报告有关导入更新、设备故障或部署更新的问题时，你可能需要提供设备更新帐户的名称。

设备更新帐户名称是用户在首次注册服务时选择的名称。 可以在[设备更新资源](./device-update-resources.md)文档中找到更多信息。

### <a name="device-update-instance-name"></a>设备更新实例名称
报告有关导入更新、设备故障或部署更新的问题时，你可能需要提供设备更新实例的名称。

设备更新实例名称是用户在首次预配时选择的名称。 可以在[设备更新资源](./device-update-resources.md)文档中找到更多信息。

### <a name="device-id"></a>设备 ID
报告有关设备故障或部署更新的问题时，你可能需要提供设备 ID。

设备 ID 是客户在首次预配设备时定义的。 此外，也可以从设备的设备孪生中检索它。

### <a name="update-id"></a>更新 ID
报告有关部署更新的问题时，你可能需要提供更新 ID。

更新 ID 是客户在启动部署时定义的。

### <a name="nginx-logs"></a>Nginx 日志
报告有关 Microsoft 联网缓存的问题时，你可能需要提供 Nginx 日志。

### <a name="adu-conftxt"></a>ADU-conf.txt
报告有关部署更新的问题时，你可能需要提供设备更新配置文件（“adu-conf.txt”）。

配置文件是可选的，它由用户按照[设备更新配置](./device-update-configuration-file.md)文档中的说明来创建。

### <a name="import-manifest"></a>导入清单
报告有关导入或部署更新的问题时，你可能需要提供导入清单文件。

导入清单是客户在将更新内容导入设备更新服务时创建的文件。

**[下一步：了解有关设备更新错误代码的详细信息](.\device-update-error-codes.md)**
