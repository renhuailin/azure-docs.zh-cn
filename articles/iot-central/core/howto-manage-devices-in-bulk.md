---
title: 在 Azure IoT Central 应用程序中创建并运行作业 | Microsoft Docs
description: Azure IoT Central 作业允许批量设备管理功能，例如更新设备属性或执行命令。
ms.service: iot-central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/08/2021
ms.topic: how-to
ms.openlocfilehash: c24eac9d7446f983bd4dc1e8573d3c831c7ff7fb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442711"
---
# <a name="manage-devices-in-bulk-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中批量管理设备

可以使用 Azure IoT Central 并通过作业大规模地管理连接的设备。 通过这些作业可以批量更新设备和云属性，以及运行命令。 还可以使用 CSV 文件批量导入和导出设备。 本文介绍如何开始在自己的应用程序中使用作业以及如何使用导入和导出功能。

## <a name="create-and-run-a-job"></a>创建并运行作业

下面的示例演示如何创建和运行作业，以便为一组逻辑网关设备设置光阈值。 使用作业向导创建和运行作业。 可保存作业以便稍后运行。

1. 在左侧窗格中选择“作业”。

1. 选择“+ 新建作业”。

1. 在“配置作业”页面上，输入名称和描述以标识要创建的作业。

1. 选择要将作业应用到的设备组。 可在“设备组”选项下查看作业配置适用于的设备数。

1. 选择“云属性”、“属性”或“命令”来作为“作业类型”：

    如要配置“属性”作业，请选择属性并设置其新值。 如要配置“命令”作业，请选择要运行的命令。 属性作业可以设置多个属性。

    :::image type="content" source="media/howto-manage-devices-in-bulk/configure-job.png" alt-text="屏幕截图显示用于创建名为“设置光阈值”的属性作业的选项":::

    选择“保存并退出”，将作业添加到“作业”页上保存的作业列表中。 稍后可以从已保存作业列表返回到作业。

1. 选择“下一步”转到“递送选项”页。 在“递送选项”页面，用户可为此作业设置递送选项：“批处理”和“取消阈值”。

    通过批处理可将大量设备的作业错开。 作业分为多个批处理，每个批处理包含设备的一个子集。 将批处理排队并按顺序运行。

    如果错误数超出设置的限制，则通过取消阈值可自动取消作业。 阈值可应用于作业中的所有设备，或应用于单个批处理。

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-delivery-options.png" alt-text="作业向导递送选项页面屏幕截图":::

1. 选择“下一步”转到“计划”页面。 在“计划”页面，用户可以启用日后运行作业的计划：

    为计划选择运行频率选项。 你可以将作业设置为按以下频率运行：

    * 一次性
    * 每日
    * 每周

    为已计划的作业设置开始日期和时间。 日期和时间是根据用户所在时区设置，而不是设备的本地时间。

    如要结束运行频率计划，请选择：

    * “当天”，设置计划的结束日期。
    * “之后”，设置运行作业的次数。

    已计划的作业始终在设备组中的设备上运行，即使设备组成员身份随时间推移而发生变化也不受影响。

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-schedule.png" alt-text="作业向导“计划选项”页面屏幕截图":::

1. 选择“下一步”转到“查看”页。 “查看”页将显示作业配置的详细信息。 选择“计划”，为作业安排运行计划：

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-schedule-review.png" alt-text="已计划的作业向导“查看”页面屏幕截图":::

1. “作业详细信息”页面显示有关已计划的作业的信息。 当执行已计划的作业时，将看到作业实例的列表。 已计划的作业执行也位于“过去 30 天历史记录”作业列表中。

    在此页上，可以为作业“取消计划”或“编辑”已计划的作业。 可以从已计划的作业列表返回到已计划的作业。

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-schedule-details.png" alt-text="已计划的作业详细信息页面屏幕截图":::

1. 在作业向导中，可以选择不计划作业并立即运行该作业。 以下屏幕截图显示了一个无计划立即运行的作业。 选择“运行”，运行作业：

    :::image type="content" source="media/howto-manage-devices-in-bulk/job-wizard-schedule-immediate.png" alt-text="作业向导“查看”页面屏幕截图":::

1. 作业将经过“挂起”、“正在运行”和“已完成”阶段。 作业执行详细信息包含结果指标、持续时间详细信息和设备列表网格。

    作业完成后，可以选择“结果日志”下载作业详细信息的 CSV 文件，包括设备及其状态值。 这些信息可用于故障排除。

    :::image type="content" source="media/howto-manage-devices-in-bulk/download-details.png" alt-text="显示设备状态的屏幕截图":::

1. 作业现在显示在“作业”页上的“过去 30 天历史记录”列表中。 此页显示当前正在运行的作业，以及以前运行的或保存的作业的历史记录。

    > [!NOTE]
    > 用户可以查看以前运行的作业 30 天内的历史记录。

## <a name="manage-jobs"></a>管理作业

如要停止运行作业，打开该作业并选择“停止”。 作业状态将更改，表示作业已停止。 “概述”部分显示已完成、已失败或仍处于挂起状态的设备。

:::image type="content" source="media/howto-manage-devices-in-bulk/manage-job.png" alt-text="显示正在运行的作业和用于停止作业的按钮的屏幕截图":::

当作业处于停止状态时，可以选择“继续”以继续运行作业。 作业状态将更改，表示作业重新运行。 “概述”部分继续更新以获得最新进度信息。

:::image type="content" source="media/howto-manage-devices-in-bulk/stopped-job.png" alt-text="显示已停止的作业和用于继续作业的按钮的屏幕截图":::

## <a name="copy-a-job"></a>复制作业

如要复制现有的作业，请选择已执行的作业。 在“作业结果”页或“作业详细信息”页上选择“复制”：

:::image type="content" source="media/howto-manage-devices-in-bulk/job-details-copy.png" alt-text="显示复制按钮的屏幕截图":::

此时会打开一个作业配置副本供用户编辑，并且“副本”将附加到作业名称中。

## <a name="view-job-status"></a>查看作业状态

创建作业后，“状态”列将会更新，包含作业的最新状态消息。 下表列出了可能的“设备状态”值：

| 状态消息       | 状态的含义                                          |
| -------------------- | ------------------------------------------------------- |
| 已完成            | 此作业在所有设备上运行。              |
| 失败               | 此作业已失败，没有在设备上完全运行。  |
| 挂起的              | 此作业尚未开始在设备上运行。         |
| 正在运行              | 此作业当前正在设备上运行。             |
| 已停止              | 用户已手动停止此作业。           |
| 已取消             | 此作业已取消，因为超出了“递送选项”页面上设置的阈值。 |

状态消息后面是作业中设备的概述。 下表列出了可能的设备状态值：

| 状态消息       | 状态的含义                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 已成功            | 已成功执行该作业的设备数。       |
| 失败               | 作业运行失败的设备数。       |

如要查看作业的状态和所有受影响设备，请打开该作业。 每个设备名称的旁边显示了以下状态消息之一：

| 状态消息       | 状态的含义                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| 已完成            | 此设备上运行的作业。                                     |
| 失败               | 此设备上运行失败的作业。 错误消息中显示了更多信息。  |
| 挂起的              | 作业尚未在此设备上运行。                                   |

如要下载包含作业详细信息和设备列表及其状态值的 CSV 文件，请选择“结果日志”。

## <a name="filter-the-device-list"></a>筛选设备列表

可通过选择“筛选器”图标来筛选“作业详细信息”页上的设备列表。 可在“设备 ID”或“状态”字段上进行筛选：

:::image type="content" source="media/howto-manage-devices-in-bulk/filter.png" alt-text="显示用于筛选设备列表的选项的屏幕截图。":::

## <a name="customize-columns-in-the-device-list"></a>自定义设备列表中的列

可以通过选择“列选项”图标，将列添加到设备列表：

:::image type="content" source="media/howto-manage-devices-in-bulk/column-options.png" alt-text="显示列选项图标的屏幕截图。":::

使用“列选项”对话框可以选择设备列表列。 选择要显示的列，选择向右箭头，然后选择“确定”。 如要选择所有可用列，请选择“全选”。 所选的列将在设备列表中显示。

选定的列会在一个用户会话中或在可以访问应用程序的用户会话中持续存在。

## <a name="rerun-jobs"></a>重新运行作业

可重新运行具有运行失败设备的作业。 选择“在运行失败的设备上重新运行作业”：

:::image type="content" source="media/howto-manage-devices-in-bulk/rerun.png" alt-text="屏幕截图显示用于在运行失败的设备上重新运行作业的按钮。":::

输入“作业名称”和“说明”，然后选择“重新运行作业”。 提交新作业，以重试对运行失败的设备的操作。

> [!NOTE]
> 用户不能从 Azure IoT Central 应用程序同时运行五个以上的作业。
>
> 当作业完成并且删除作业设备列表中的设备时，设备名称中的设备条目将显示为“已删除”。 “详细信息”链接对已删除的设备不可用。

## <a name="import-devices"></a>导入设备

若要将大量设备连接到应用程序，可从 CSV 文件批量导入设备。 你可以在 [Azure 示例存储库](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/bulk-upload-devices)中找到示例 CSV 文件。 该 CSV 文件应包含以下列标头：

| 列 | 说明 |
| - | - |
| IOTC_DEVICEID | 设备 ID 是此设备用于连接的唯一标识符。 设备 ID 中可以包含字母、数字和 `-` 字符（不包含空格）。 最大长度为 128 个字符。 |
| IOTC_DEVICENAME | 可选。 设备名是将在整个应用程序中显示的易记名称。 如果未指定设备名，则该名称与设备 ID 相同。 最大长度为 148 个字符。 |

若要在应用程序中批量注册设备：

1. 在左侧窗格中选择“设备”。

1. 在左面板中，选择要为其批量创建设备的设备模板。

    > [!NOTE]
    > 如果还没有设备模板，可以在“所有设备”下导入设备，并在没有模板的情况下注册这些设备。 导入设备后，可将其关联到某个模板。

1. 选择“导入”。

    :::image type="content" source="media/howto-manage-devices-in-bulk/bulk-import-1.png" alt-text="显示导入操作设置的屏幕截图。":::

1. 选择包含要导入的设备 ID 列表的 CSV 文件。

1. 上传文件后，会立即开始导入设备。 可以在“设备操作”面板中跟踪导入状态。 导入开始后，此面板会自动显示；也可以通过右上角的钟形图标访问此面板。

1. 导入完成后，“设备操作”面板中会显示成功消息。

    :::image type="content" source="media/howto-manage-devices-in-bulk/bulk-import-2.png" alt-text="显示导入成功的屏幕截图。":::

如果设备导入操作失败，“设备操作”面板中会显示错误消息。 系统会生成一个捕获所有错误的可下载的日志文件。

## <a name="export-devices"></a>导出设备

若要将真实设备连接到 IoT Central，需要其连接字符串。 可以批量导出设备详细信息，以获取创建设备连接字符串所需的信息。 导出过程会为所有选定的设备创建一个 CSV 文件，其中包含设备标识、设备名称和密钥。

从应用程序中批量导出设备：

1. 在左侧窗格中选择“设备”。

1. 在左侧面板中，选择要从中导出设备的设备模板。

1. 选择要导出的设备，然后选择“导出”操作。

    :::image type="content" source="media/howto-manage-devices-in-bulk/export-1.png" alt-text="显示导出操作设置的屏幕截图。":::

1. 导出过程开始。 可以使用“设备操作”面板跟踪状态。

1. 导出完成后，将显示一条成功消息，并提供一个用来下载生成文件的链接。

1. 选择“下载文件”链接将文件下载到磁盘上的本地文件夹。

    ![导出成功](./media/howto-manage-devices-in-bulk/export-2.png)

1. 导出的 CSV 文件包含以下列：设备 ID、设备名称、设备密钥和 X509 证书指纹：

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

有关将真实设备连接到 IoT Central 应用程序的详细信息，请参阅 [Azure IoT Central 中的设备连接](concepts-get-connected.md)。

## <a name="next-steps"></a>后续步骤

现在，你了解了如何在 Azure IoT Central 应用程序中批量管理设备，建议接下来了解如何[编辑设备模板](howto-edit-device-template.md)。
