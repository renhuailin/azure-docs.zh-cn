---
title: 在 Defender for IoT 门户中管理传感器
description: 了解如何在 Defender for IoT 门户中加入、查看和管理传感器。
ms.date: 06/03/2021
ms.topic: how-to
ms.openlocfilehash: 47bdcbdcab807efae9a39383837b6c863ec6fd06
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015661"
---
# <a name="manage-sensors-in-the-defender-for-iot-portal"></a>在 Defender for IoT 门户中管理传感器

本文介绍了如何在 [Defender for IoT 门户](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)中加入、查看和管理传感器。

## <a name="onboard-sensors"></a>加入传感器

通过向 Azure Defender for IoT 注册传感器并下载传感器激活文件来加入传感器。

### <a name="register-the-sensor"></a>注册传感器

**若要注册，请执行以下操作：**

1. 转到 [Defender for IoT 门户](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)中的“欢迎”页。

1. 选择“加入传感器”。

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/onboard-a-sensor.png" alt-text="选择“加入传感器”以启动传感器的载入进程。":::

1. 创建传感器名称。 

    建议在名称中包含已安装的传感器的 IP 地址，或使用易于识别的名称。 这将确保更轻松地跟踪 Azure [Defender for IoT 门户](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)中的注册名称和传感器控制台中显示的已部署传感器的 IP，并确保这两者的命名一致。

1. 将传感器与 Azure 订阅相关联。

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/name-subscription.png" alt-text="输入有意义的名称，并将传感器连接到订阅。":::

1. 使用“连接到云”开关选择传感器连接模式。 如果打开此开关，则传感器将连接到云。 如果关闭此开关，则传感器将在本地进行管理。

   - 连接到云的传感器：传感器检测到的信息显示在传感器控制台中。 警报信息通过 IoT 中心进行传递，并且可以与其他 Azure 服务（例如 Azure Sentinel）共享。 此外，威胁智能包可以从 Azure Defender for IoT 门户推送到传感器。 相反，当传感器未连接到云时，你必须下载威胁情报包，然后将其上传到企业传感器。 若要允许 Defender for IoT 将包推送到传感器，请启用“自动威胁情报更新”开关。 有关详细信息，请参阅[威胁情报研究和包](how-to-work-with-threat-intelligence-packages.md)。
   
   对于连接到云的传感器，在加入过程中定义的名称就是在传感器控制台中显示的名称。 无法直接从控制台更改此名称。 对于在本地管理的传感器，在加入期间应用的名称会存储在 Azure 中，但可以在传感器控制台中进行更新。

   - 本地管理的传感器：传感器检测到的信息会在传感器控制台中显示。 如果使用气隙网络，并且想要统一查看由多个本地托管的传感器检测到的所有信息，请使用本地管理控制台。

1. 在 IoT 中心内选择要将传感器关联到的站点。 IoT 中心将作为此传感器与 Azure Defender for IoT 之间的网关。 定义显示名称和区域。 还可以添加说明性标记。 显示名称、区域和标记是[“站点和传感器”页](#view-onboarded-sensors)上的说明性条目。

1. 选择“注册”。 

### <a name="download-the-sensor-activation-file"></a>下载传感器激活文件

注册传感器后，即可下载激活文件。 传感器激活文件包含有关传感器管理模式的说明。 请下载所部署的每个传感器的唯一激活文件。 首次登录传感器控制台的用户将激活文件上传到传感器。

**若要下载激活文件，请执行以下操作：**

1. 在“加入传感器”页上，选择“注册”

1. 选择“下载激活文件”。

1. 使文件可供首次登录到传感器控制台的用户访问。

## <a name="view-onboarded-sensors"></a>查看已加入的传感器

在 [Defender for IoT 门户](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)上，你可以查看有关已加入的传感器的重要操作信息。

1. 选择“站点和传感器”。 此页显示已加入的传感器数量、已连接到云的传感器数量、在本地管理的传感器数量，以及：

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/sites-and-sensors.png" alt-text="选择“站点和传感器”页，查看所有关联的传感器。":::

    - 加入期间分配的传感器名称。
    - 连接类型（连接到云或在本地托管）。
    - 与传感器关联的区域。
    - 安装的传感器版本。
    - 传感器的云连接状态。
    - 上次检测到传感器连接到云的时间。

## <a name="manage-onboarded-sensors"></a>管理已加入的传感器

将 [Defender for IoT 门户](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)用于与传感器相关的管理任务。

可以在“站点和传感器”页上查看已加入的传感器。 你还可以在此页中编辑传感器信息。

### <a name="export-sensor-details"></a>导出传感器详细信息

若要导出已加入的传感器的信息，请选择“站点和传感器”页顶部的“导出”图标。

:::image type="content" source="media/how-to-manage-sensors-on-the-cloud/export-sensors.png" alt-text="选择“导出”按钮导出传感器信息。":::

### <a name="edit-sensor-zone-details"></a>编辑传感器区域详细信息

使用“站点和传感器”编辑选项来编辑传感器名称和区域。

**若要进行编辑，请执行以下操作：**

1. 选择与要编辑的传感器对应的省略号 (...)。

1. 选择“编辑”。

1. 更新传感器区域，或创建一个新区域。

### <a name="delete-a-sensor"></a>删除传感器

如果你删除连接到云的传感器，则信息不会被发送到 IoT 中心。 不再使用本地连接的传感器时，请将其删除。

**若要删除传感器，请执行以下操作：**

1. 选择与要删除的传感器对应的省略号 (...)。

1. 选择“删除传感器”。

### <a name="reactivate-a-sensor"></a>重新激活传感器 

你可能需要重新激活传感器，因为你希望实现以下目的：

- 在连接到云的模式下而非本地管理模式下工作：重新激活后，传感器检测结果将显示在传感器中，新检测到的警报信息将通过 IoT 中心传送。 可以与其他 Azure 服务（例如 Azure Sentinel）共享该信息。

- 在本地管理模式下而非连接到云的模式下工作：重新激活后，传感器检测信息将仅显示在传感器中。

- 将传感器关联到新的 IoT 中心：若要执行此操作，请向一个新的中心重新注册传感器，然后下载新的激活文件。

**若要重新激活传感器，请执行以下操作：**

1. 转到 [Defender for IoT 门户](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started)的“站点和传感器”页。

1. 选择要为其上传新的激活文件的传感器。

1. 选择省略号 (...)，然后选择“删除传感器”。

    :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/delete-a-sensor.png" alt-text="选择省略号，然后选择“删除传感器”。":::

1. 重新在新模式下[加入传感器](#onboard-sensors)，或者在“入门”页中选择“加入传感器”，通过新 IoT 中心加入传感器。

1. 下载激活文件。

1. 登录到 Defender for IoT 传感器控制台。

1. 在传感器控制台中，选择“系统设置”，然后选择“重新激活”。

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="上传激活文件以重新激活传感器。":::

1. 选择“上传”，然后从“加入传感器”页中选择你保存的文件。

1. 选择“激活”  。

## <a name="next-steps"></a>后续步骤

[激活并设置传感器](how-to-activate-and-set-up-your-sensor.md)
