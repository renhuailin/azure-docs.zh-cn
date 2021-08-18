---
title: 导入设备信息
description: Defender for IoT 传感器监视和分析镜像流量。 在这些情况下，可能需要导入数据，以扩充已检测到的设备的信息。
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: eb82a3e5d360913acfead06adc03194d80f7fccb
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015761"
---
# <a name="import-device-information-to-a-sensor"></a>将设备信息导入传感器

Azure Defender for IoT 传感器监视和分析镜像流量。 在某些情况下，由于特定于组织的网络配置策略，某些信息可能无法传输。

在这些情况下，可能需要导入数据，以扩充已检测到的设备的信息。 有两个选项可用于将信息导入传感器：

- **从映射导入**：将设备名称、类型、组或 Purdue 层更新到映射。

- **从导入设置导入**：导入设备 OS、IP 地址、修补级别或授权状态。

## <a name="import-from-the-map"></a>从映射导入

本部分介绍如何将设备名称、类型、组或 Purdue 层导入到设备映射。 从映射执行此操作。

重要要求如下：

- **名称**：最多 30 个字符。

- **类型** 或 **Purdue 层**：使用“设备属性”对话框中显示的选项。 （右键单击设备，然后选择“查看属性”。）

- **设备组**：创建最多 30 个字符的新组。 

> [!NOTE]
> 若要避免冲突，请不要将从一个传感器导出的数据导入到另一个传感器。

进行导入操作：

1. 在侧边菜单上，选择“设备”。

2. 在“设备”窗口的右上角，选择 :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false":::。

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="“设备”窗口的屏幕截图。":::

3. 选择“导出设备”。 导出的文件中将显示范围广泛的信息。 此信息包括设备使用的协议和设备授权状态。

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="导出文件中的信息。":::

4. 在 CSV 文件中，仅更改设备名称、类型、组和 Purdue 层。 然后保存文件。 

   使用导出的文件中显示的大写标准。 例如，对于 Purdue 层，使用所有首字母大写。

5. 在“设备”窗口中的“导入/导出”下拉菜单中，选择“导入设备”  。

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="通过“设备”窗口导入设备。":::

6. 选择“导入设备”，然后选择要导入的 CSV 文件。 导入状态消息将在屏幕上显示，直到“导入设备”对话框关闭。

## <a name="import-from-import-settings"></a>从导入设置导入

本部分介绍如何将设备 IP 地址、OS、修补级别或授权状态导入设备映射。 从“导入设置”对话框执行此操作。

导入 IP 地址、OS 和修补级别：

1. 从[帮助中心](https://cyberx-labs.zendesk.com/hc/en-us)下载 [devices_info_2.2.8 和 up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 文件，并按如下所示输入信息：

   - **IP 地址**：输入设备 IP 地址。

   - **操作系统**：从下拉列表中选择。

   - **上次更新时间**：使用 YYYY-MM-DD 格式。

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="选项屏幕。":::

2. 在侧边菜单中选择“导入设置”。

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="导入设置。":::

3. 若要上传所需的配置，请在“设备信息”部分选择“添加”，然后上传准备好的 CSV 文件 。

导入授权状态：

1. 从 Defender for IoT 帮助中心下载并保存 [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 文件。 验证是否已将文件保存为 CSV。

2. 按如下所示输入信息：

   - **IP 地址**：设备 IP 地址。

   - **名称**：授权的设备名称。 请确保名称准确无误。 为导入列表中的设备指定的名称将覆盖设备映射中显示的名称。

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="包含导入设备列表的 Excel 文件。":::

3. 在侧边菜单中选择“导入设置”。

4. 在“授权设备”部分中，选择“添加”，然后上传已保存的 CSV 文件 。

导入信息后，会收到有关未在此列表中显示的所有设备的未授权设备的警报。

## <a name="import-device-information-to-the-sensor"></a>将设备信息导入传感器

传感器监视和分析镜像流量。 在某些情况下，由于特定于组织的网络配置策略，某些信息可能无法传输。

在这些情况下，可能需要导入数据，以扩充已检测到的设备的设备信息。 有两个选项可用于将信息导入传感器：

- **从映射导入**：将设备名称、类型、组或 Purdue 层更新到映射。

- **从导入设置导入**：导入设备 OS、IP 地址、修补级别或授权状态。

### <a name="import-from-the-map"></a>从映射导入

本部分介绍如何将设备名称、类型、组或 Purdue 层导入到设备映射。 从映射执行此操作。

重要要求如下：

- **名称**：最多 30 个字符。

- **类型** 或 **Purdue 层**：使用“设备属性”对话框中显示的选项。 （右键单击设备，然后选择“查看属性”。）

- **设备组**：创建最多 30 个字符的新组。 

> [!NOTE]
> 若要避免冲突，请不要将从一个传感器导出的数据导入到另一个传感器。

进行导入操作：

1. 在侧边菜单上，选择“设备”。

2. 在“设备”窗口的右上角，选择 :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false":::。

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="从中选取设备的窗口。":::

3. 选择“导出设备”。 导出的文件中将显示范围广泛的信息。 示例包括设备使用的协议和设备授权状态。

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="导出文件中的信息。":::

4. 在 CSV 文件中，仅更改设备名称、类型、组和 Purdue 层。 然后保存文件。 

   使用导出的文件中显示的大写标准。 例如，对于 Purdue 层，使用所有首字母大写。

5. 在“设备”窗口中的“导入/导出”下拉菜单中，选择“导入设备”  。

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="导入设备。":::

6. 选择“导入设备”，然后选择要导入的 CSV 文件。 导入状态消息将在屏幕上显示，直到“导入设备”对话框关闭。

### <a name="import-from-import-settings"></a>从导入设置导入 

本部分介绍如何将设备 IP 地址、OS、修补级别或授权状态导入设备映射。 从“导入设置”对话框执行此操作。

导入 IP 地址、OS 和修补级别：

1. 从[帮助中心](https://cyberx-labs.zendesk.com/hc/en-us)下载 [devices_info_2.2.8 和 up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 文件，并按如下所示输入信息：

   - **IP 地址**：设备 IP 地址。

   - **操作系统**：从下拉列表中选择。

   - **上次更新日期**：使用 YYYY-MM-DD 格式。

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="屏幕上的内容。":::

2. 在侧边菜单中选择“导入设置”。

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="填写“导入设置”屏幕。":::

3. 若要上传所需的配置，请在“设备信息”部分中选择 “添加”，然后上传已准备的 CSV 文件 。

导入授权状态：

1. 从 Defender for IoT 帮助中心下载并保存 [authorized_devices - examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 文件。 验证是否已将文件保存为 CSV。

2. 按如下所示输入信息：

   - **IP 地址**：设备 IP 地址。

   - **名称**：授权的设备名称。 验证名称是否准确无误。 为导入列表中的设备指定的名称将覆盖设备映射中显示的名称。

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="导入到设备映射的列表。":::

3. 在侧边菜单中选择“导入设置”。

4. 在“授权设备”部分中，选择“添加”，然后上传已保存的 CSV 文件 。

导入信息后，会收到有关未在此列表中显示的所有设备的未授权设备的警报。

## <a name="see-also"></a>另请参阅

[控制要监视的流量](how-to-control-what-traffic-is-monitored.md)

[调查设备清单中的传感器检测](how-to-investigate-sensor-detections-in-a-device-inventory.md)
