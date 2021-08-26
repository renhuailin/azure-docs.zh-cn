---
title: 激活和设置本地管理控制台
description: 激活管理控制台可确保将传感器注册到 Azure 并将信息发送到本地管理控制台，还能确保本地管理控制台在连接的传感器上执行管理任务。
ms.date: 05/05/2021
ms.topic: how-to
ms.openlocfilehash: 84506e9ebd12dab4198d075c6afea8ae23604a42
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445857"
---
# <a name="activate-and-set-up-your-on-premises-management-console"></a>激活和设置本地管理控制台 

激活和设置本地管理控制台可确保：

- 通过连接传感器监视的网络设备会注册到 Azure 帐户。

- 传感器会将信息发送到本地管理控制台。

- 本地管理控制台在连接的传感器上执行管理任务。

- 已安装 SSL 证书。

## <a name="sign-in-for-the-first-time"></a>首次登录

**若要登录到管理控制台，请执行以下操作：**

1. 导航到系统安装期间收到的本地管理控制台的 IP 地址。
 
1. 输入系统安装期间收到的本地管理控制台的用户名和密码。 


如果忘记了密码，请选择“恢复密码”选项，并参阅[密码恢复](how-to-manage-the-on-premises-management-console.md#password-recovery)以获取有关如何恢复密码的说明。

## <a name="activate-the-on-premises-management-console"></a>激活本地管理控制台

首次登录后，需要获取并上传激活文件来激活本地管理控制台。 

**激活本地管理控制台：**

1. 登录到本地管理控制台。

1. 在屏幕顶部的警报通知中，选择“执行操作”链接。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/take-action.png" alt-text="在屏幕顶部的警报中选择“执行操作”链接。":::

1. 在“激活”弹出窗口中，选择“Azure 门户”链接。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/azure-portal.png" alt-text="从弹出消息中选择“Azure 门户”链接。":::
 
1. 选择要将本地管理控制台关联到的订阅，然后选择“下载本地管理控制台激活文件”按钮。 此时将下载激活文件。

   本地管理控制台可以关联到一个或多个订阅。 激活文件将与所有选定的订阅相关联，在下载时将与提交的设备数相关联。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/multiple-subscriptions.png" alt-text="可以选择多个订阅以将本地管理控制台加入到其中。":::

   如果尚未加入订阅，则[加入订阅](how-to-manage-subscriptions.md#onboard-a-subscription)。

   > [!Note]
   > 如果删除某个订阅，则需要将一个与该订阅关联的新激活文件上传到所有本地管理控制台。

1. 导航回“激活”弹出屏幕，然后选择“选择文件” 。

1. 选择下载的文件。

初始激活后，受监视的设备数可能会超过在载入过程中定义的已提交设备数。 如果将更多传感器连接到管理控制台，就会发生此问题。 如果受监视的设备数与已提交的设备数之间存在差异，则管理控制台中会出现警告。 

:::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/device-commitment-update.png" alt-text="如果看到设备提交警告，则需要上传新激活文件。":::

如果显示此警告，则需要上传[新激活文件](#activate-the-on-premises-management-console)。

### <a name="activate-an-expired-license-versions-under-100"></a>激活已过期的许可证（低于 10.0 的版本）

对于使用低于 10.0 版本的用户，如果其许可证已过期，将显示以下警报。 

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/activation-popup.png" alt-text="许可证过期时，需要通过激活文件更新许可证。":::

若要激活许可证，请执行以下操作：

1. 向[支持人员](https://ms.portal.azure.com/?passwordRecovery=true&Microsoft_Azure_IoT_Defender=canary#create/Microsoft.Support)提出案例。

1. 向支持人员提供激活 ID 号。

1. 支持人员将以一串字母的形式为你提供新的许可证信息。

1. 阅读条款和条件，并勾选同意复选框。

1. 将字符串粘贴到提供的空白位置。

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/add-license.png" alt-text="将字符串粘贴到提供的字段中。":::

1. 选择“激活”  。

## <a name="set-up-a-certificate"></a>设置证书

安装管理控制台后，将生成一个本地自签名证书。 此证书用于访问控制台。 管理员首次登录管理控制台后，系统会提示用户载入 SSL/TLS 证书。 

提供两个安全性级别：

- 通过上传 CA 签名的证书来满足组织请求的特定证书和加密要求。

- 允许在管理控制台和连接的传感器之间进行验证。 根据证书吊销列表和证书到期日期对验证进行评估。 “如果验证失败，则管理控制台和传感器之间的通信将停止，且控制台中会出现验证错误。” 安装后默认启用此选项。  

控制台支持以下类型的证书：

- 专用和企业密钥基础结构（专用 PKI）

- 公钥基础结构（公共 PKI）

- 在设备上本地生成（本地自签名） 

  > [!IMPORTANT]
  > 建议不使用自签名证书。 该证书不安全，应仅用于测试环境。 无法验证证书的所有者，也无法维护系统的安全性。 请勿将此选项用于生产网络。

**若要上传证书，请执行以下操作：**

1. 登录后出现提示信息时，请定义证书名称。

1. 上传 CRT 和密钥文件。

1. 输入密码并上传 PEM 文件（如有必要）。

上传 CA 签名的证书后，你可能需要刷新屏幕。

**若要禁用管理控制台和连接的传感器之间的验证，请执行以下操作：**

1. 选择“下一步”  。

1. 关闭“启用系统范围的验证”切换。

有关上传新证书、受支持的证书文件和相关项目的信息，请参阅[管理本地管理控制台](how-to-manage-the-on-premises-management-console.md)。

## <a name="connect-sensors-to-the-on-premises-management-console"></a>将传感器连接到本地管理控制台

确保传感器向本地管理控制台发送信息，且本地管理控制台可以执行备份、管理警报及在传感器上执行其他活动。 为此，请使用以下过程来验证是否在传感器与本地管理控制台之间建立了初始连接。

有两个选项可用于将 Azure Defender for IoT 传感器连接到本地管理控制台：

- 从传感器控制台进行连接

- 使用隧道进行连接

连接之后，必须使用这些传感器设置站点。

### <a name="connect-sensors-to-the-on-premises-management-console-from-the-sensor-console"></a>将传感器从传感器控制台连接到本地管理控制台

若要将传感器从传感器控制台连接到本地管理控制台，请执行以下操作：

1. 在本地管理控制台上，选择“系统设置”。

1. 复制“复制连接字符串”。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-string.png" alt-text="复制传感器的连接字符串。":::

1. 在传感器上，导航到“系统设置”，然后选择“与管理控制台的连接”:::image type="icon" source="media/how-to-manage-sensors-from-the-on-premises-management-console/connection-to-management-console.png" border="false"::: 

1. 将从本地管理控制台复制的连接字符串粘贴到“连接字符串”字段中。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/paste-connection-string.png" alt-text="将复制的连接字符串粘贴到连接字符串字段中。":::

1. 选择“连接”。

### <a name="connect-sensors-by-using-tunneling"></a>使用隧道连接传感器

在组织传感器与本地管理控制台之间启用安全隧道连接。 此设置可避免与组织防火墙交互，因此会减少攻击面。

通过使用隧道，可以从本地管理控制台的 IP 地址和单一端口（即 9000）将其连接到到任何传感器。

**若要在本地管理控制台上设置隧道，请执行以下操作：**

- 登录到本地管理控制台并运行以下命令：

  ```bash
  cyberx-management-tunnel-enable
  service apache2 reload
  sudo cyberx-management-tunnel-add-xsense --xsenseuid <sensorIPAddress> --xsenseport 9000
  service apache2 reload
  ```

**若要在传感器上设置隧道，请执行以下操作：**

1. 在传感器上手动打开 TCP 端口 9000（网络属性）。 如果端口未打开，则传感器将拒绝本地管理控制台的连接。

2. 登录到每个传感器，并运行以下命令：

   ```bash
   sudo cyberx-xsense-management-connect -ip <on-premises management console IP Address> -token < Copy the string that appears after the IP colon (:) from the Connection String field, Management Console Connection dialog box>
   sudo cyberx-xsense-management-tunnel
   sudo vi /var/cyberx/properties/network.properties
   opened_tcp_incoming_ports=22,80,443,9000
   sudo cyberx-xsense-network-validation
   sudo /etc/network/if-up.d/iptables-recover
   sudo iptables -nvL
   ```

## <a name="set-up-a-site"></a>设置站点

默认的企业地图根据多个地理位置级别提供设备的总体视图。

组织结构和用户权限非常复杂时，可能需要设备的视图。 在这些情况下，除了标准站点或区域结构之外，站点设置可能由全局组织结构确定。

若要支持此环境，需要创建基于组织的业务部门、地区、站点和区域的全局业务拓扑。 还需要使用访问组来定义针对这些实体的用户访问权限。

访问组可以更好地控制用户在 Defender for IoT 中管理和分析设备的位置。

### <a name="how-it-works"></a>工作原理

你可以为组织中的每个站点定义一个业务部门和一个地区。 然后，可以添加区域，这些区域是网络中存在的逻辑实体。 

为每个区域至少分配一个传感器。 五级模型提供交付反映组织结构的保护系统所需的灵活性和粒度。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/diagram-of-sensor-showing-relationships.png" alt-text="显示传感器和地区关系的图表。":::

使用企业视图，可以直接编辑站点。 从企业视图中选择站点时，每个区域旁边会出现打开的警报数。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/console-map-with-data-overlay-v2.png" alt-text="使用柏林数据覆盖的本地管理控制台地图的屏幕截图。":::

**若要设置站点，请执行以下操作：**

1. 添加新的业务部门以反映组织的逻辑结构。

   1. 在“企业”视图中，选择“所有网站” > “管理业务部门”。

      :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-business-unit.png" alt-text="从“企业视图”屏幕上的“所有站点”下拉菜单中选择“管理业务部门”。":::

   1. 输入新的业务部门名称，然后选择“添加”。

1. 添加新地区以反映组织的地区。

   1. 在“企业”视图中，选择“所有地区” > “管理地区” 。

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/manage-regions.png" alt-text="选择所有地区，然后管理地区，通过这种方式在企业中管理地区。":::

   1. 输入新的地区名称，然后选择“添加”。

1. 添加站点。

   1. 在“企业”视图的顶部栏中选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/new-site-icon.png" border="false":::。 此时光标会显示为加号 ( **+** )。

   1. 将 **+** 放置到新站点的位置并选中。 此时将打开“创建新站点”对话框。

      :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-site-screen.png" alt-text="创建新站点视图的屏幕截图。":::

   1. 为新站点定义名称和物理地址，然后选择“保存”。 新站点将显示在站点地图上。

4. [将区域添加到站点](#create-enterprise-zones)。

5. [连接传感器](how-to-manage-individual-sensors.md#connect-a-sensor-to-the-management-console)。

6. [将传感器分配到站点区域](#assign-sensors-to-zones)。

### <a name="delete-a-site"></a>删除站点

如果不再需要某个站点，可以从本地管理控制台将其删除。

**若要删除站点，请执行以下操作：**

1. 在“站点管理”窗口中，从包含站点名称的栏中选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false":::，然后选择“删除站点”。 此时将显示确认框，验证你是否要删除此站点。

2. 在确认框中，选择“确认”。

## <a name="create-enterprise-zones"></a>创建企业区域

区域是逻辑实体，使你能够根据各种特征将站点中的设备划分为组。 例如，可以为生产线、变电站、站点区域或设备类型创建组。 可以根据适用于组织的任何特性来定义区域。

将区域配置作为站点配置过程的一部分。

:::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/site-management-zones-screen-v2.png" alt-text="站点管理区域视图的屏幕截图。":::

下表描述了“站点管理”窗口中的参数。

| 参数 | 描述 |
|--|--|
| 名称 | 传感器名称。 只能从传感器更改此名称。 有关详细信息，请参阅 Defender for IoT 用户指南。 |
| IP | 传感器 IP 地址。 |
| 版本 | 传感器版本。 |
| 连接 | 传感器连接状态。 状态可以为“已连接”或“已断开连接” 。 |
| 上次升级 | 上次升级的日期。 |
| 升级进度 | 进度栏指示升级过程的状态，如下所示：<br />- 正在上传包<br />- 正在准备安装<br />- 正在停止进程<br />- 正在备份数据<br />- 正在创建快照<br />- 正在更新配置<br />- 正在更新依赖项<br />- 正在更新库<br />- 正在修补数据库<br />- 正在启动进程<br />- 正在验证系统健全性<br />- 验证已成功<br />- 成功<br />- 失败<br />- 已启动升级<br />- 正在启动安装ogress bar shows the status of the upgrade process, as follows:<br />- Uploading package<br />- Preparing to install<br />- Stopping processes<br />- Backing up data<br />- Taking snapshot<br />- Updating configuration<br />- Updating dependencies<br />- Updating libraries<br />- Patching databases<br />- Starting processes<br />- Validating system sanity<br />- Validation succeeded<br />- Success<br />- Failure<br />- Upgrade started<br />- Starting installation<br /></br >有关升级的详细信息，请联系[Microsoft 支持部门](https://support.microsoft.com/)获取帮助。 |
| 设备 | 传感器监视的 OT 设备的数量。 |
| 警报 | 传感器上的警报数。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false"::: | 允许将传感器分配给区域。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::| 允许从站点中删除断开连接的传感器。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/sensor-icon.png" border="false"::: | 指示当前连接到区域的传感器的数量。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/ot-assets-icon.png" border="false"::: | 指示当前连接到区域的 OT 资产的数量。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/number-of-alerts-icon.png" border="false"::: | 指示分配给区域的传感器发送的警报数。 |
| :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false"::: | 从区域取消分配传感器。 |

**若要向站点中添加区域，请执行以下操作：**

1. 在“站点管理”窗口中，从包含站点名称的栏中选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false":::，然后选择“添加区域”。 此时将显示“创建新区域”对话框。

    :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/create-new-zone-screen.png" alt-text="创建新区域视图的屏幕截图。":::

1. 输入区域名称。

1. 输入新区域说明，该说明能清楚指出将站点划分为区域所用的特征。

1. 选择“保存”。 新区域将显示在此区域所属站点下的“站点管理”窗口中。

**若要编辑区域，请执行以下操作：**

1. 在“站点管理”窗口中，从包含站点名称的栏中选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false":::，然后选择“编辑区域”。 此时将显示“编辑区域”对话框。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/zone-edit-screen.png" alt-text="显示“编辑区域”对话框的屏幕截图。":::

1. 编辑区域参数并选择“保存”。

**若要删除区域，请执行以下操作：**

1. 在“站点管理”窗口中，从包含站点名称的栏中选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/expand-view-icon.png" border="false":::，然后选择“删除区域”。

1. 在确认框中，选择“是”。

**若要根据连接状态进行筛选，请执行以下操作：**

- 在左上角选择“连接”旁的 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false":::，然后选择下列选项之一：

  - “全部”：显示向此本地管理控制台报告的所有传感器。

  - “已连接”：仅显示已连接的传感器。

  - “已断开连接”：仅显示已断开连接的传感器。

**若要根据升级状态进行筛选，请执行以下操作：**

- 在左上角选择“升级状态”旁的 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/down-pointing-icon.png" border="false":::，然后选择下列选项之一：

  - “全部”：显示向此本地管理控制台报告的所有传感器。

  - “有效”：显示处于有效升级状态的传感器。

  - “正在进行”：显示正在升级的传感器。

  - “失败”：显示升级过程失败的传感器。

## <a name="assign-sensors-to-zones"></a>将传感器分配到区域

需要给每个区域分配执行本地流量分析和警报的传感器。 只能分配连接到本地管理控制台的传感器。

**若要分配传感器，请执行以下操作：**

1. 选择“站点管理”。 未分配的传感器将显示在对话框的左上角。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassigned-sensors-view.png" alt-text="未分配传感器视图的屏幕截图。":::

1. 验证“连接” 状态是否为已连接。 如果未连接，请参阅[将传感器连接到本地管理控制台](#connect-sensors-to-the-on-premises-management-console)以获取有关连接的详细信息。 

1. 为要分配的传感器选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-icon.png" border="false":::。

1. 在“分配传感器”对话框中，选择要分配的业务部门、地区、站点和区域。

   :::image type="content" source="media/how-to-activate-and-set-up-your-on-premises-management-console/assign-sensor-screen.png" alt-text="分配传感器视图的屏幕截图。":::

1. 选择“分配”。

**若要取消分配和删除传感器，请执行以下操作：**

1. 断开传感器与本地管理控制台的连接。 请参阅[将传感器连接到本地管理控制台](#connect-sensors-to-the-on-premises-management-console)获取详细信息。

1. 在“站点管理”窗口中，选择传感器，然后选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/unassign-sensor-icon.png" border="false":::。 稍后，传感器将显示在未分配传感器的列表中。

1. 若要从站点中删除未分配的传感器，请从未分配传感器列表中选择该传感器，然后选择 :::image type="icon" source="media/how-to-activate-and-set-up-your-on-premises-management-console/delete-icon.png" border="false":::。

## <a name="see-also"></a>请参阅

[排查传感器和本地管理控制台问题](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
