---
title: 激活和设置本地管理控制台
description: 管理控制台激活和安装程序确保传感器注册到 Azure 并将信息发送到本地管理控制台，本地管理控制台在连接的传感器上执行管理任务。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/12/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 94a1db30419e5d7e52f369392d94b817d0dc273a
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623748"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>激活和设置本地管理控制台 

激活和设置本地管理控制台可确保：

- 通过连接的传感器监视的网络设备将注册到 Azure 帐户。

- 传感器将信息发送到本地管理控制台。

- 本地管理控制台在连接的传感器上执行管理任务。

- 已安装 SSL 证书。

## <a name="sign-in-for-the-first-time"></a>首次登录

若要登录到管理控制台：

- 打开 web 浏览器，输入系统安装期间为本地管理控制台收到的 IP 地址和密码。 如果忘记了密码，请选择 " **恢复密码** " 并参阅 " [密码恢复](how-to-manage-the-on-premises-management-console.md#password-recovery)"。

## <a name="upload-an-activation-file"></a>上传激活文件

首次登录后，通过从 Azure Defender for IoT 门户的 **定价** 页下载激活文件，激活本地管理控制台。 此文件包含在载入过程中定义的已提交的聚合设备。 已 **提交的设备** 指示每个订阅的 IoT 用于监视的设备数。

上载激活文件：

1. 请参阅适用于 IoT **定价** 的 Defender 页。
1. 选择 " **下载管理控制台** " 选项卡的激活文件。下载激活文件。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="下载激活文件。":::

1. 从管理控制台中选择 " **系统设置** "。
1. 选择 " **激活**"。
1. 选择 " **选择文件** "，然后选择已保存的文件。

初始激活后，受监视的设备数可能会超过在载入期间定义的已提交设备数。 例如，如果将更多的传感器连接到管理控制台，就可能会发生这种情况。 如果监视的设备数和已提交设备数之间存在差异，则管理控制台中会出现警告。 如果发生这种情况，你应该上载新的激活文件。

## <a name="set-up-a-certificate"></a>设置证书

在安装管理控制台后，将生成一个本地自签名证书并用于访问控制台。 管理员首次登录到管理控制台后，系统会提示该用户加入 SSL/TLS 证书。 

提供两个安全级别：

- 通过上传 CA 签名证书来满足组织要求的特定证书和加密要求。
- 允许在管理控制台和连接的传感器之间进行验证。 对照证书吊销列表和证书到期日期对验证进行评估。 *如果验证失败，则会停止管理控制台和传感器之间的通信，并在控制台中显示验证错误。* 默认情况下，安装后会启用此选项。  

控制台支持以下类型的证书：

- 专用 PKI 和企业密钥基础结构 (专用 PKI) 

- 公共密钥基础结构 (公共 PKI) 

- 在设备上本地生成 (本地自签名)  

  > [!IMPORTANT]
  > 建议你不要使用自签名证书。 证书不安全，只应用于测试环境。 无法验证证书的所有者，并且无法维护系统的安全。 请勿将此选项用于生产网络。

上载证书：

1. 登录后，请定义证书名称。
1. 上传 CRT 和密钥文件。
1. 如果需要，请输入通行短语并上传 PEM 文件。

上载 CA 签名的证书后，你可能需要刷新屏幕。

在管理控制台和连接的传感器之间禁用验证：

1. 选择“下一步”。
1. 关闭 " **启用系统范围的验证** " 切换。

有关上传新证书、支持的证书文件和相关项目的信息，请参阅 [管理本地管理控制台](how-to-manage-the-on-premises-management-console.md)。

## <a name="connect-sensors-to-the-on-premises-management-console"></a>将传感器连接到本地管理控制台

必须确保传感器向本地管理控制台发送信息，并且本地管理控制台可以执行备份、管理警报和在传感器上执行其他活动。 为此，请使用以下过程来验证是否在传感器与本地管理控制台之间建立了初始连接。

有两个选项可用于将用于 IoT 的 Azure Defender 传感器连接到本地管理控制台：

- 从传感器控制台进行连接

- 使用隧道连接

连接之后，必须使用这些传感器设置站点。

### <a name="connect-sensors-from-the-sensor-console"></a>从传感器控制台连接传感器

要从传感器控制台将特定传感器连接到本地管理控制台，请执行以下操作：

1. 在传感器控制台的左窗格中，选择 " **系统设置**"。

2. 选择 " **连接到管理**"。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-not-connected.png" alt-text="本地管理控制台状态窗口的屏幕截图，显示 &quot;未连接&quot;。":::

3. 在 " **地址** " 文本框中，输入要连接到的本地管理控制台的 IP 地址。

4. 选择“连接”。 状态更改：

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/connection-status-window-connected.png" alt-text="本地管理控制台状态窗口的屏幕截图，显示 &quot;已连接&quot;。":::

### <a name="connect-sensors-by-using-tunneling"></a>使用隧道连接传感器

在组织传感器与本地管理控制台之间启用安全隧道连接。 此设置可避免与组织防火墙交互，因此会减少攻击面。

通过使用隧道，你可以从其 IP 地址和单个端口 (（9000) 到任何传感器）连接到本地管理控制台。

若要在本地管理控制台上设置隧道：

- 登录到本地管理控制台并运行以下命令：

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

在传感器上设置隧道：

1. 在传感器上打开 TCP 端口 9000 (") 手动"。 如果端口未打开，则传感器将拒绝本地管理控制台的连接。

2. 登录到每个传感器，并运行以下命令：

   ```bash
   sudo cyberx-xsense-management-connect -ip <centralmanagerIPAddress>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,102,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>设置站点

默认的企业地图根据多个地理位置级别提供设备的总体视图。

组织结构和用户权限非常复杂时，可能需要设备的视图。 在这些情况下，除了标准站点或区域结构之外，站点设置可能由全局组织结构确定。

若要支持此环境，需要创建基于你的组织的业务部门、区域、站点和区域的全局业务拓扑。 还需要使用访问组来定义针对这些实体的用户访问权限。

访问组可以更好地控制用户在 IoT 平台的 Defender 中管理和分析设备的位置。

### <a name="how-it-works"></a>工作原理

对于每个站点，你可以定义一个业务单位和一个区域。 然后，可以添加区域，这些区域是网络中的逻辑实体。 

对于每个区域，应至少分配一个传感器。 五级模型提供提供保护系统所需的灵活性和粒度，以反映组织的结构。

您可以直接从任何地图视图编辑您的网站。 从地图视图打开站点时，每个区域旁边会出现打开的警报数。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="使用柏林数据覆盖的本地管理控制台映射屏幕截图。":::

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="显示传感器和区域关系的图表。":::

设置站点：

1. 添加新的业务部门，以反映组织的逻辑结构。

2. 添加新区域以反映组织的区域。

3. 添加站点。

4. 将区域添加到站点。

5. 连接传感器。

6. 将传感器分配到站点区域。

添加业务部门：

1. 从 "企业" 视图中，选择 "**所有网站**" "  >  **管理业务单位**"。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-business-unit-screen.png" alt-text="显示 &quot;管理业务部门&quot; 视图的屏幕截图。":::

2. 输入新的业务部门名称，然后选择 " **添加**"。

添加新区域：

1. 从 "企业" 视图中，选择 "**所有区域**" "  >  **管理区域**"。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/manage-regions-screen.png" alt-text="显示 &quot;管理区域&quot; 视图的屏幕截图。":::

2. 输入新的区域名称，然后选择 " **添加**"。

添加新站点：

1. 从 "企业" 视图的 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false"::: 顶部栏中选择。 光标显示为加号 (**+**) 。

2. 将放置在 **+** 新站点的位置并选择它。 此时将打开 "新建 **站点** " 对话框。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="创建新站点视图的屏幕截图。":::

3. 为新站点定义名称和物理地址，然后选择 " **保存**"。 新站点将显示在站点图上。

删除站点：

1. 在 " **站点管理** " 窗口中， :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 从包含站点名称的栏中进行选择，然后选择 " **删除站点**"。 此时将显示确认框，验证你是否要删除此站点。

2. 在确认框中，选择 **"是"**。 确认框将关闭，并且 " **站点管理** " 窗口将显示，但不会删除已删除的站点。

## <a name="create-enterprise-zones"></a>创建企业区域

区域是逻辑实体，使你能够根据各种特征将站点中的设备划分为组。 例如，你可以为生产线、变电站、站点区域或设备类型创建组。 你可以根据适用于你的组织的任何特性来定义区域。

将区域配置为站点配置过程的一部分。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="站点管理区域视图的屏幕截图。":::

下表描述了 " **站点管理** " 窗口中的参数。

| 参数 | 说明 |
|--|--|
| 名称 | 传感器的名称。 只能从传感器更改此名称。 有关详细信息，请参阅用于 IoT 的 Defender 用户指南。 |
| IP | 传感器 IP 地址。 |
| 版本 | 传感器版本。 |
| 连接 | 传感器连接状态。 状态可以为 " **已连接** " 或 "已 **断开** 连接"。 |
| 上次升级 | 上次升级的日期。 |
| 升级进度 | 进度栏显示升级过程的状态，如下所示：<br />-正在上传包<br />-正在准备安装<br />-正在停止进程<br />-备份数据<br />-拍摄快照<br />-正在更新配置<br />-更新依赖项<br />-更新库<br />-修补数据库<br />-正在启动进程<br />-正在验证系统是否稳定<br />-验证成功<br />-成功<br />-失败<br />-升级已启动<br />-正在启动安装ogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >有关升级的详细信息，请参阅 [Microsoft 支持部门](https://support.microsoft.com/) 获取帮助。 |
| 设备 | 传感器监视的设备的数量。 |
| 警报 | 传感器上的警报数。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | 允许将传感器分配给区域。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| 允许从站点中删除断开连接的传感器。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | 指示当前连接到区域的传感器的数量。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | 指示当前已将多少个 OT 资产连接到该区域。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | 指示分配给该区域的传感器发送的警报数。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | 从区域其分配传感器。 |

向站点添加区域：

1. 在 " **站点管理** " 窗口中， :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 从包含站点名称的栏中进行选择，然后选择 " **添加区域**"。 此时将显示 " **创建新区域** " 对话框。

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="&quot;创建新的区域&quot; 视图的屏幕截图。":::

2. 输入区域名称。

3. 为新区域输入说明，该说明清楚地指出了用于将站点划分为区域的特征。

4. 选择“保存”  。 新区域将显示在此区域所属站点下的 " **站点管理** " 窗口中。

编辑区域：

1. 在 " **站点管理** " 窗口中， :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 从包含区域名称的栏中进行选择，然后选择 " **编辑区域**"。 此时将显示 " **编辑区域** " 对话框。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="显示 &quot;编辑区域&quot; 对话框的屏幕截图。":::

2. 编辑区域参数并选择 " **保存**"。

若要删除区域，请执行以下操作：

1. 在 " **站点管理** " 窗口中， :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false"::: 从包含区域名称的栏中进行选择，然后选择 " **删除区域**"。

2. 在确认框中，选择 **"是"**。

根据连接状态进行筛选：

- 从左上角选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: " **连接**"，然后选择下列选项之一：

  - **全部**：显示向此本地管理控制台报告的所有传感器。

  - **已连接**：仅显示连接的传感器。

  - **断开连接**：仅显示断开连接的传感器。

根据升级状态进行筛选：

- 从左上角选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false"::: " **升级状态** " 旁边，然后选择下列选项之一：

  - **全部**：显示向此本地管理控制台报告的所有传感器。

  - **有效**：向传感器显示有效的升级状态。

  - **正在进行**：显示处于升级过程中的传感器。

  - **Failed**：显示升级过程失败的传感器。

## <a name="assign-sensors-to-zones"></a>将传感器分配到区域

对于每个区域，需要分配执行本地流量分析和警报的传感器。 只能分配连接到本地管理控制台的传感器。

分配传感器：

1. 选择 " **站点管理**"。 未分配的传感器将显示在对话框的左上角。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="未分配的传感器视图的屏幕截图。":::

2. 验证 **连接** 状态是否为 "已连接"。 如果不是，请参阅 [将传感器连接到本地管理控制台](#connect-sensors-to-the-on-premises-management-console) ，以获取有关连接的详细信息。 

3. 选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: 要分配的传感器。

4. 在 " **分配传感器** " 对话框中，选择要分配的业务部门、区域、站点和区域。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="分配传感器视图的屏幕截图。":::

5. 选择 " **分配**"。

要取消分配和删除传感器：

1. 断开传感器与本地管理控制台的连接。 有关详细信息，请参阅 [将传感器连接到本地管理控制台](#connect-sensors-to-the-on-premises-management-console) 。

2. 在 " **站点管理** " 窗口中，选择传感器，然后选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: 。 几分钟后，传感器将显示在未分配传感器的列表中。

3. 若要从站点中删除未分配的传感器，请从未分配传感器列表中选择该传感器，然后选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false"::: 。

## <a name="see-also"></a>另请参阅

[排查传感器和本地管理控制台问题](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
