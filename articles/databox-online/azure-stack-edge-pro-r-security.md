---
title: Azure Stack Edge Pro R security |Microsoft Docs
description: 描述安全和隐私功能，这些功能可在本地和云中保护 Azure Stack Edge Pro R 和 Azure Stack 边缘迷你 R 设备、服务和数据。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 891885dce52ee57fa163bb71b427591156091651
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362841"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Azure Stack Edge Pro R 和 Azure Stack 边缘迷你 R 的安全性和数据保护

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

在采用新技术时，安全性是一个主要考虑因素，尤其是在该技术用于机密或专有数据时。 Azure Stack Edge Pro R 和 Azure Stack 边缘迷你 R 可帮助确保只有授权的实体可以查看、修改或删除数据。

本文介绍 Azure Stack Edge Pro R 和 Azure Stack 边缘迷你 R 安全功能，这些功能可帮助保护每个解决方案组件和其中存储的数据。

解决方案由四个主要组件进行交互：

- **在 azure 公共或 Azure 政府云中托管的 Azure Stack Edge 服务**。 用于创建设备顺序、配置设备，然后跟踪订单完成的管理资源。
- **Azure Stack 边缘可靠设备**。 提供给你的耐用的物理设备，你可以将本地数据导入 Azure 公共或 Azure 政府版云。 该设备可以 Azure Stack Edge Pro R 或 Azure Stack 边缘迷你 R。
- **连接到设备的客户端/主机**。 基础结构中连接到设备并包含需要保护的数据的客户端。
- **云存储**。 Azure 云平台中存储数据的位置。 此位置通常是链接到所创建的 Azure Stack Edge 资源的存储帐户。

## <a name="service-protection"></a>服务保护

Azure Stack Edge 服务是 Azure 中托管的一项管理服务。 服务用于配置和管理设备。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>设备保护

坚固的设备是一种本地设备，可帮助你在本地处理数据，然后将数据发送到 Azure。 你的设备：

- 需要激活密钥才能访问 Azure Stack Edge 服务。
- 由设备密码始终受保护。
- 是锁定的设备。 设备基板管理控制器 (BMC) 和 BIOS 受密码保护。 BMC 受受限的用户访问保护。
- 启用安全启动，确保仅使用 Microsoft 提供的受信任软件启动设备。
-  (WDAC) 运行 Windows Defender 应用程序控制。 通过 WDAC，只能运行在代码完整性策略中定义的受信任的应用程序。
- 具有受信任的平台模块 (TPM) ，用于执行基于硬件的安全相关的功能。 具体而言，TPM 管理并保护需要在设备上保留的机密和数据。
- 仅在设备上打开所需的端口，并阻止所有其他端口。 有关详细信息，请参阅设备的 [端口需求](azure-stack-edge-pro-r-system-requirements.md) 列表。
- 将记录对设备硬件和软件的所有访问。 
    - 对于设备软件，将收集来自设备的入站和出站流量的默认防火墙日志。 这些日志捆绑在支持包中。
    - 对于设备硬件，所有设备底盘事件（例如打开和关闭设备机箱）都记录在设备中。

    有关包含硬件和软件入侵事件的特定日志以及如何获取日志的详细信息，请转到 [收集高级安全日志](azure-stack-edge-gpu-troubleshoot.md)。


### <a name="protect-the-device-via-activation-key"></a>通过激活密钥保护设备

仅允许授权的 Azure Stack Edge Pro R 或 Azure Stack 边缘迷你 R 设备加入你在 Azure 订阅中创建的 Azure Stack Edge 服务。 若要授权设备，你需要使用激活密钥来激活设备与 Azure Stack Edge 服务。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

有关详细信息，请参阅 [获取激活密钥](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>通过密码保护设备

密码确保只有经过授权的用户才能访问数据。 Azure Stack 边缘 Pro R 设备在锁定状态启动。

方法：

- 通过浏览器连接到设备的本地 web UI，然后提供用于登录设备的密码。
- 通过 HTTP 远程连接到设备 PowerShell 接口。 默认情况下启用远程管理。 远程管理还配置为使用足够的管理 (JEA) ，以限制用户可执行的操作。 然后，你可以提供用于登录设备的设备密码。 有关详细信息，请参阅 [远程连接到你的设备](azure-stack-edge-gpu-connect-powershell-interface.md)。
- 设备上的本地边缘用户对设备的访问权限受到限制，无法进行初始配置和故障排除。 在设备上运行的计算工作负荷、数据传输和存储均可从 Azure 公共或政府门户访问云中资源。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- 使用本地 web UI [更改密码](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password)。 如果更改了密码，请务必通知所有远程访问用户，以便在登录时不会出现问题。

### <a name="establish-trust-with-the-device-via-certificates"></a>通过证书建立与设备的信任

Azure Stack 边缘坚固的设备使你可以自带证书，并将其安装到用于所有公共终结点。 有关详细信息，请参阅 " [上传证书](azure-stack-edge-j-series-manage-certificates.md#upload-certificates)"。 若要获取可在设备上安装的所有证书的列表，请参阅 [管理设备上的证书](azure-stack-edge-j-series-manage-certificates.md)。

- 在设备上配置计算时，会创建 IoT 设备和 IoT Edge 设备。 系统会自动为这些设备分配对称访问密钥。 最佳安全做法是通过 IoT 中心服务定期轮换这些密钥。

## <a name="protect-your-data"></a>保护数据

本节介绍保护传输中数据和存储数据的安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

设备上的所有静态数据都是双加密的，对数据的访问受到控制，设备停用后，数据将安全地从数据磁盘中删除。

#### <a name="double-encryption-of-data"></a>数据的双加密

磁盘上的数据受两层加密保护：

- 第一层加密是数据卷上的 BitLocker XTS-AES 256 位加密。
- 第二层是具有内置加密的硬盘。
- OS 卷将 BitLocker 作为加密的单个层。

> [!NOTE]
> OS 磁盘具有单层 BitLocker XTS-256 软件加密。

激活设备后，系统会提示你保存一个密钥文件，其中包含恢复密钥，可帮助在设备无法启动时恢复设备上的数据。 此文件中有两个密钥：

- 一个密钥恢复 OS 卷上的设备配置。
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- 第二个密钥解锁数据磁盘中的硬件加密。

> [!IMPORTANT]
> 将密钥文件保存在设备本身之外的安全位置。 如果设备未启动，并且没有密钥，则可能会导致数据丢失。

- 某些恢复方案将提示你输入已保存的密钥文件。 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>限制对数据的访问

对共享和存储帐户中存储的数据的访问受到限制。

- 访问共享数据的 SMB 客户端需要与共享关联的用户凭据。 这些凭据是在创建共享时定义的。
- 访问共享的 NFS 客户端需要在创建共享时显式添加其 IP 地址。
- 在设备上创建的边缘存储帐户是本地的，受数据磁盘上的加密保护。 与边缘存储帐户关联的订阅和 2 512 位存储访问密钥会保护这些边缘存储帐户所映射到的 Azure 存储帐户 (这些密钥不同于与你的 Azure 存储帐户) 相关联的密钥。 有关详细信息，请参阅 [保护存储帐户中的数据](#protect-data-in-storage-accounts)。
- BitLocker XTS-AES 256 位加密用于保护本地数据。

#### <a name="secure-data-erasure"></a>安全数据擦除

当设备进行硬重置时，将在设备上执行安全擦除。 安全擦除使用 NIST SP 800-88r1 清除对磁盘执行数据擦除。

### <a name="protect-data-in-flight"></a>保护传输中的数据

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>保护存储帐户中的数据

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- 定期轮换 [存储帐户密钥](azure-stack-edge-j-series-manage-storage-accounts.md) 并对其进行同步，以帮助保护你的存储帐户不受未经授权的用户的保护。

## <a name="manage-personal-information"></a>管理个人信息

Azure Stack Edge 服务在以下情况下收集个人信息：

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

若要查看可访问或删除共享的用户的列表，请按照 [管理 Azure Stack 边缘上的共享](azure-stack-edge-j-series-manage-shares.md)中的步骤操作。

有关详细信息，请在 [信任中心](https://www.microsoft.com/trustcenter)查看 Microsoft 隐私策略。

## <a name="next-steps"></a>后续步骤

[部署 Azure Stack Edge Pro R 设备](azure-stack-edge-gpu-deploy-prep.md)
