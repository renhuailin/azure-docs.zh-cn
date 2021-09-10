---
title: 使用本地 Web UI 管理 Azure Data Box/Azure Data Box Heavy
description: 介绍如何使用本地 Web UI 管理 Data Box 和 Data Box Heavy 设备
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/25/2021
ms.author: alkohli
ms.openlocfilehash: fa37d5dc9957e39d7fe4f47404bc9aaf249f192f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226404"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box-and-data-box-heavy"></a>使用本地 Web UI 管理 Data Box 和 Data Box Heavy

本文介绍可在 Data Box 和 Data Box Heavy 设备上执行的一些配置和管理任务。 可以通过 Azure 门户 UI 和设备的本地 Web UI 管理 Data Box 和 Data Box Heavy 设备。 本文重点介绍可使用本地 Web UI 执行的任务。

Data Box 和 Data Box Heavy 的本地 Web UI 用于设备的初始配置。 还可以使用本地 Web UI 关闭或重启设备、运行诊断测试、更新软件、查看复制日志、擦除设备中的本地数据以及生成供 Microsoft 支持部门使用的支持包。 在具有两个独立节点的 Data Box Heavy 设备上，可以访问对应于每个设备节点的两个独立本地 Web UI。

## <a name="generate-support-package"></a>生成支持包

如果遇到任何设备问题，可通过系统日志创建支持包。 Microsoft 支持部门使用此包来解决此问题。

要生成支持包，请执行以下步骤：

1. 在本地 Web UI 中，转到“联系支持部门”。 也可以选择“包括内存转储”。 然后选择“创建支持包”。

    内存转储是系统出现故障后保存的设备内存内容。

    除非支持人员要求提供内存转储，否则不应选择“包括内存转储”选项。 收集包括内存转储的支持包（包括敏感数据）需要很长时间。

    ![创建支持包 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

    将收集到一个支持包。 如果只包括系统日志，则只需几分钟就可完成此操作。 如果包括内存转储，则需要很长时间。

    ![创建支持包 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

2. 支持包创建完成后，选择“下载支持包”。

    ![创建支持包 3](media/data-box-local-web-ui-admin/create-support-package-3.png)

3. 浏览并选择下载位置。 打开文件夹以查看内容。

    ![创建支持包 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

## <a name="erase-local-data-from-your-device"></a>擦除设备中的本地数据

在将设备中的本地数据返回到 Azure 数据中心之前，可以使用本地 Web UI 将其擦除。

> [!IMPORTANT]
> 数据擦除不可逆。 擦除设备中的本地数据之前，请务必备份这些文件。

若要擦除设备中的本地数据，请执行以下步骤：

1. 在本地 Web UI 中，转到“数据擦除”。
2. 输入设备密码，并选择“擦除数据”。

    ![设备的数据擦除选项](media/data-box-local-web-ui-admin/erase-local-data-1.png)

3. 在出现确认提示时选择“是”以继续操作。 数据擦除可能需要长达 50 分钟的时间。

   在擦除设备中的本地数据之前，请务必备份这些数据。 数据擦除不可逆。

    ![数据擦除确认提示](media/data-box-local-web-ui-admin/erase-local-data-2.png)

## <a name="shut-down-or-restart-your-device"></a>关闭或重启设备

可使用本地 Web UI 关闭或重启设备。 在重启之前，建议使共享依次在主机和设备上脱机。 这样做能在最大程度上减少发生数据损坏的可能性。 关闭设备时，请确保当前未执行数据复制。

若要关闭设备，请执行以下步骤。

1. 在本地 Web UI 中，转到“关闭或重启”。

2. 选择“关闭”。

    ![关闭 Data Box 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. 出现确认提示时，请选择“确定”以继续。

    ![关闭 Data Box 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

关闭设备后，使用前面板上的电源按钮打开设备。

要重启 Data Box，请执行以下步骤。

1. 在本地 Web UI 中，转到“关闭或重启”。
2. 选择“重启”。 

    ![重启 Data Box 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. 出现确认提示时，请选择“确定”以继续。

   设备将关闭并重启。

## <a name="get-share-credentials"></a>获取共享凭据 

如果需要找出用来连接设备上某个共享的用户名和密码，可以在本地 Web UI 中的“连接和复制”中找到共享凭据。

订购设备时，可以为设备上的共享使用系统生成的密码或自己的密码。 无论哪种方式，共享密码都是出厂设置的，不能进行更改。 

获取共享凭据：

1. 在本地 Web UI 中，转到“连接和复制”。 选择“SMB”以获取与存储帐户关联的共享的访问凭据。

   ![屏幕截图显示了 Data Box 本地 Web UI 中的“连接和复制”页。 突出显示了“连接和复制”菜单项和“SMB”选项。](media/data-box-local-web-ui-admin/get-share-credentials-01.png)

1. 在“访问共享和复制数据”对话框中，使用复制图标复制对应于该共享的“用户名”和“密码”。   要关闭该对话框，请选择“确定”。

   ![屏幕截图显示了 Data Box 本地 Web UI 中用于 SMB 共享的“访问共享和复制数据”对话框。 突出显示了“存储帐户和密码”选项的“复制”图标以及“确定”按钮。](media/data-box-local-web-ui-admin/get-share-credentials-02.png)

> [!NOTE]
> 在多次尝试使用错误密码，共享连接失败后，用户帐户被锁定，无法共享。 几分钟后帐户锁定会被解除，便可以重新连接共享。  
> - Data Box 4.1 及更高版本：在 5 次登录尝试失败后，帐户会被锁定 15 分钟。 
> - Data Box 4.0 及更低版本：在 3 次登录尝试失败后，帐户会被锁定 30 分钟。

## <a name="download-bom-or-manifest-files"></a>下载 BOM 或清单文件

BOM 或清单文件包含要复制到 Data Box 或 Data Box Heavy 的文件列表。 这些文件是在准备要交付的设备时针对导入订单生成的。

在开始之前，请按照以下步骤为导入订单下载 BOM 或清单文件：

1. 转到设备的本地 Web UI。 验证设备是否已完成“准备交付”步骤。 设备准备完成后，设备状态将显示为“已准备好寄送”。

    ![设备已准备好寄送](media/data-box-local-web-ui-admin/prepare-to-ship-3.png)

2. 选择“下载文件列表”，以下载在 Data Box 上复制的文件列表。

    <!-- ![Select Download list of files](media/data-box-portal-admin/download-list-of-files.png) -->

3. 在文件资源管理器中，已根据用于连接到设备的协议和使用的 Azure 存储类型，生成了单独的文件列表。

    <!-- ![Files for storage type and connection protocol](media/data-box-portal-admin/files-storage-connection-type.png) -->
    ![根据存储类型和连接协议生成的文件](media/data-box-local-web-ui-admin/prepare-to-ship-5.png)

   下表将文件名映射到 Azure 存储类型和使用的连接协议。

    |文件名  |Azure 存储类型  |使用的连接协议 |
    |---------|---------|---------|
    |utSAC1_202006051000_BlockBlob-BOM.txt     |块 Blob         |SMB/NFS         |
    |utSAC1_202006051000_PageBlob-BOM.txt     |页 Blob         |SMB/NFS         |
    |utSAC1_202006051000_AzFile-BOM.txt    |Azure 文件         |SMB/NFS         |
    |utsac1_PageBlock_Rest-BOM.txt     |页 Blob         |REST        |
    |utsac1_BlockBlock_Rest-BOM.txt    |块 Blob         |REST         |

将 Data Box 寄回到 Azure 数据中心后，使用此列表来验证已上传到 Azure 存储帐户的文件。 示例清单文件如下所示。

> [!NOTE]
> 在 Data Box Heavy 上，提供了对应于两个设备节点的两组文件列表（BOM 文件）。

```xml
<file size="52689" crc64="0x95a62e3f2095181e">\databox\media\data-box-deploy-copy-data\prepare-to-ship2.png</file>
<file size="22117" crc64="0x9b160c2c43ab6869">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer2.png</file>
<file size="57159" crc64="0x1caa82004e0053a4">\databox\media\data-box-deploy-copy-data\verify-used-space-dashboard.png</file>
<file size="24777" crc64="0x3e0db0cd1ad438e0">\databox\media\data-box-deploy-copy-data\prepare-to-ship5.png</file>
<file size="162006" crc64="0x9ceacb612ecb59d6">\databox\media\data-box-cable-options\cabling-dhcp-data-only.png</file>
<file size="155066" crc64="0x051a08d36980f5bc">\databox\media\data-box-cable-options\cabling-2-port-setup.png</file>
<file size="150399" crc64="0x66c5894ff328c0b1">\databox\media\data-box-cable-options\cabling-with-switch-static-ip.png</file>
<file size="158082" crc64="0xbd4b4c5103a783ea">\databox\media\data-box-cable-options\cabling-mgmt-only.png</file>
<file size="148456" crc64="0xa461ad24c8e4344a">\databox\media\data-box-cable-options\cabling-with-static-ip.png</file>
<file size="40417" crc64="0x637f59dd10d032b3">\databox\media\data-box-portal-admin\delete-order1.png</file>
<file size="33704" crc64="0x388546569ea9a29f">\databox\media\data-box-portal-admin\clone-order1.png</file>
<file size="5757" crc64="0x9979df75ee9be91e">\databox\media\data-box-safety\japan.png</file>
<file size="998" crc64="0xc10c5a1863c5f88f">\databox\media\data-box-safety\overload_tip_hazard_icon.png</file>
<file size="5870" crc64="0x4aec2377bb16136d">\databox\media\data-box-safety\south-korea.png</file>
<file size="16572" crc64="0x05b13500a1385a87">\databox\media\data-box-safety\taiwan.png</file>
<file size="999" crc64="0x3f3f1c5c596a4920">\databox\media\data-box-safety\warning_icon.png</file>
<file size="1054" crc64="0x24911140d7487311">\databox\media\data-box-safety\read_safety_and_health_information_icon.png</file>
<file size="1258" crc64="0xc00a2d5480f4fcec">\databox\media\data-box-safety\heavy_weight_hazard_icon.png</file>
<file size="1672" crc64="0x4ae5cfa67c0e895a">\databox\media\data-box-safety\no_user_serviceable_parts_icon.png</file>
<file size="3577" crc64="0x99e3d9df341b62eb">\databox\media\data-box-safety\battery_disposal_icon.png</file>
<file size="993" crc64="0x5a1a78a399840a17">\databox\media\data-box-safety\tip_hazard_icon.png</file>
<file size="1028" crc64="0xffe332400278f013">\databox\media\data-box-safety\electrical_shock_hazard_icon.png</file>
<file size="58699" crc64="0x2c411d5202c78a95">\databox\media\data-box-deploy-ordered\data-box-ordered.png</file>
<file size="46816" crc64="0x31e48aa9ca76bd05">\databox\media\data-box-deploy-ordered\search-azure-data-box1.png</file>
<file size="24160" crc64="0x978fc0c6e0c4c16d">\databox\media\data-box-deploy-ordered\select-data-box-option1.png</file>
<file size="115954" crc64="0x0b42449312086227">\databox\media\data-box-disk-deploy-copy-data\data-box-disk-validation-tool-output.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
<file size="2823" crc64="0x63db1ada6fcdc672">\databox\index.yml</file>
<file size="4364" crc64="0x62b5710f58f00b8b">\databox\data-box-local-web-ui-admin.md</file>
<file size="3603" crc64="0x7e34c25d5606693f">\databox\TOC.yml</file>
```

此文件包含已在 Data Box 或 Data Box Heavy 上复制的所有文件的列表。 在此文件中，*crc64* 值与针对相应文件生成的校验和相关。

## <a name="view-available-capacity-of-the-device"></a>查看设备的可用容量

可使用设备仪表板查看设备的可用和已用容量。

1. 在本地 web UI 中，转到“查看仪表板”。
2. “连接和复制”下显示设备上的可用和已用空间。

    ![查看可用容量](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)

## <a name="skip-checksum-validation"></a>跳过校验和验证

准备寄送时，默认会生成数据的校验和。 在某些罕见的情况下，根据所用的数据类型（小型文件），性能可能较慢。 在这种情况，则可以跳过校验和。

准备交付期间的校验和计算仅适用于导入订单，不适用于导出订单。

我们强烈建议仅在性能受到严重影响的情况下禁用校验和。

1. 在设备本地 Web UI 的右上角，转到“设置”。

    ![禁用校验和](media/data-box-local-web-ui-admin/disable-checksum.png)

2. 禁用校验和验证
3. 选择“应用”。

> [!NOTE]
> “跳过校验和计算”选项仅在 Azure Data Box 处于解锁状态时才可用。 锁定设备时，不会显示此选项。

## <a name="enable-smb-signing"></a>启用 SMB 签名

服务器消息块 (SMB) 签名是一项功能，通过此功能可以在数据包级别对使用 SMB 的通信进行数字签名。 此签名可防范修改传输中 SMB 数据包的攻击。

有关 SMB 签名的详细信息，请参阅[服务器消息块签名概述](https://support.microsoft.com/help/887429/overview-of-server-message-block-signing)。

若要在 Azure 设备中启用 SMB 签名：

1. 在设备本地 Web UI 的右上角，选择“设置”。

    ![打开“设置”](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. “启用”SMB 签名。

    ![启用 SMB 签名](media/data-box-local-web-ui-admin/data-box-smb-signing-1.png)

3. 选择“应用”。
4. 在本地 Web UI 中，转到“关闭或重启”。
5. 选择“重启”。 

## <a name="enable-backup-operator-privileges"></a>启用备份操作员特权

默认情况下，Web UI 用户对 SMB 共享具有备份操作员特权。 如果不想有此特权，请使用“启用备份操作员特权”以禁用或启用特权。

有关信息，请参阅 [Active Directory 安全组](/windows/security/identity-protection/access-control/active-directory-security-groups#backup-operators)中的“备份操作员”。

若要在 Azure 设备中启用备份操作员特权：

1. 在设备本地 Web UI 的右上角，选择“设置”。

   ![打开 Data Box 设置 - 1](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. “启用”备份操作员特权。

   ![启用备份操作员特权](media/data-box-local-web-ui-admin/data-box-backup-operator-privileges-1.png)

3. **选择“应用”** 。
4. 在本地 Web UI 中，转到“关闭或重启”。
5. 选择“重启”。 

## <a name="enable-acls-for-azure-files"></a>启用 Azure 文件存储的 ACL

用户通过 SMB 将数据上传到 Data Box 时，默认传输文件中的元数据。 该元数据包括访问控制列表 (ACL)、文件属性和时间戳。 如果不想这样做，请使用“Azure 文件存储的 ACL”以禁用或启用此功能。

<!--For more information about metadata that is transferred, see [Preserving the ACLs and metadata with Azure Data Box](./data-box-local-web-ui-admin.md#enable-backup-operator-privileges) - IN DEVELOPMENT-->

> [!Note]
> 若要将元数据与文件一起传输，你必须是备份操作员。 使用此功能时，请确保 Web UI 的本地用户是备份操作员。 请参阅[启用备份操作员特权](#enable-backup-operator-privileges)。

若要启用 Azure 文件存储的 ACL 传输：

1. 在设备本地 Web UI 的右上角，选择“设置”。

    ![打开 Data Box 设置 - 2](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. “启用”Azure 文件存储的 ACL。

     ![启用 Azure 文件存储的 ACL](media/data-box-local-web-ui-admin/data-box-acls-for-azure-files-1.png)
  
3. 选择“应用”。
4. 在本地 Web UI 中，转到“关闭或重启”。
5. 选择“重启”。 

## <a name="enable-tls-11"></a>启用 TLS 1.1

默认情况下，Azure Data Box 使用传输层安全性 (TLS) 1.2 进行加密，因为它比 TSL 1.1 更安全。 但是，如果你或你的客户端使用不支持 TLS 1.2 的浏览器访问数据，则可以启用 TLS 1.1。

有关 TLS 的详细信息，请参阅 [Azure Data Box Gateway 安全性](../databox-gateway/data-box-gateway-security.md)。

若要在 Azure 设备中启用 TLS 1.1：

1. 在设备本地 Web UI 的右上角，选择“设置”。

    ![打开 Data Box 设置 - 3](media/data-box-local-web-ui-admin/data-box-settings-1.png)

2. “启用”TLS 1.1。

    ![启用 TLS 1.1](media/data-box-local-web-ui-admin/data-box-tls-1-1.png)

3. 选择“应用”。
4. 在本地 Web UI 中，转到“关闭或重启”。
5. 选择“重启”。 

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure 门户管理 Data Box 和 Azure Data Box Heavy](data-box-portal-admin.md)。
