---
title: Azure Stack Edge Pro R 安全性 | Microsoft Docs
description: 介绍保护 Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 设备、服务以及本地数据和云中数据的安全和隐私功能。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: bd90a16c09dce65115cea2f097d18f2e0ced931a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632027"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 的安全性和数据保护

[!INCLUDE [applies-to-pro-r-and-mini-r--skus](../../includes/azure-stack-edge-applies-to-pro-r-mini-r-sku.md)]

安全性是采用新技术（尤其是在该技术用于处理机密或专有数据时）时关心的主要问题。 Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 有助于确保只有授权的实体可以查看、修改或删除数据。

本文介绍了帮助保护每个解决方案组件及其存储的数据的 Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 安全功能。

解决方案包括四个主要的交互组件：

- **Azure Stack Edge 服务，托管在 Azure 公有云或 Azure 政府云中**。 用于创建设备订单、配置设备，然后跟踪订单完成状态的管理资源。
- **Azure Stack Edge Rugged 设备**。 附带的加固型物理设备使你可以将本地数据导入到 Azure 公有云或 Azure 政府云中。 设备可为 Azure Stack Edge Pro R 或 Azure Stack Edge Mini R。
- **连接到设备的客户端/主机**。 基础结构中的客户端，它们连接到设备，并包含需要保护的数据。
- **云存储**。 Azure 云平台中存储数据的位置。 该位置通常是链接到所创建的 Azure Stack Edge 资源的存储帐户。

## <a name="service-protection"></a>服务保护

Azure Stack Edge 服务是 Azure 中托管的一项管理服务。 该服务用于配置和管理设备。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>设备保护

该加固型设备是一种本地设备，它在本地处理数据，然后将其发送到 Azure，从而帮助你转换数据。 你的设备：

- 需要使用激活密钥来访问 Azure Stack Edge 服务。
- 始终受设备密码保护。
- 是一个锁定的设备。 设备基板管理控制器 (BMC) 和 BIOS 受密码保护。 BMC 受保护，仅可供有限的用户访问。
- 启用安全启动，确保仅使用 Microsoft 提供的受信任软件启动设备。
- 运行 Windows Defender 应用程序控制 (WDAC)。 使用 WDAC，你可以仅运行你在代码完整性策略中定义的受信任应用程序。
- 具有一个受信任的平台模块 (TPM)，用于执行基于硬件的安全相关功能。 具体而言，TPM 管理和保护需要保留在设备上的机密和数据。
- 仅在设备上打开所需的端口，并阻止所有其他端口。 有关详细信息，请参阅[设备端口要求](azure-stack-edge-pro-r-system-requirements.md)列表。
- 将记录对设备硬件和软件的所有访问。 
    - 对于设备软件，会针对来自设备的入站和出站流量收集默认防火墙日志。 这些日志捆绑在支持包中。
    - 对于设备硬件，所有设备机箱事件（例如打开和关闭设备机箱）都记录在设备中。

    有关包含硬件和软件入侵事件的特定日志以及如何获取这些日志的详细信息，请转到[收集高级安全日志](azure-stack-edge-gpu-troubleshoot.md)。


### <a name="protect-the-device-via-activation-key"></a>通过激活密钥保护设备

仅允许经授权的 Azure Stack Edge Pro R 或 Azure Stack Edge Mini R 设备加入你在 Azure 订阅中创建的 Azure Stack Edge 服务。 若要授权设备，需要使用通过 Azure Stack Edge 服务激活设备的激活密钥。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

有关详细信息，请参阅[获取激活密钥](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>通过密码保护设备

密码可确保只有授权用户才能访问数据。 Azure Stack Edge Pro R 设备将在锁定状态下启动。

你可以：

- 通过浏览器连接到设备的本地 Web UI，然后提供用于登录到设备的密码。
- 通过 HTTP 远程连接到设备 PowerShell 界面。 默认情况下启用远程管理。 远程管理还配置为使用 Just Enough Administration (JEA) 以限制用户可以执行的操作。 然后，你可以提供用于登录设备的设备密码。 有关详细信息，请参阅[远程连接到设备](azure-stack-edge-gpu-connect-powershell-interface.md)。
- 设备上的本地 Edge 用户对设备的访问权限有限，无法进行初始配置和故障排除。 可以从 Azure 公共或政府门户访问设备上运行的计算工作负载、数据传输和存储，以获取云中的资源。

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- 使用本地 Web UI [更改密码](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password)。 如果更改密码，请务必通知所有远程访问用户，以便用户在登录时不会出现问题。

### <a name="establish-trust-with-the-device-via-certificates"></a>通过证书与设备建立信任关系

通过 Azure Stack Edge Rugged 设备，你可以自带证书，并安装这些证书以用于所有公共终结点。 有关详细信息，请转到[上传证书](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)。 若要获取可在设备上安装的所有证书的列表，请参阅[管理设备上的证书](azure-stack-edge-gpu-manage-certificates.md)。

- 在设备中配置计算时，会创建 IoT 设备和 IoT Edge 设备。 系统会自动为这些设备分配对称访问密钥。 最佳安全做法是通过 IoT 中心服务定期轮换这些密钥。

## <a name="protect-your-data"></a>保护数据

本节介绍保护传输中数据和存储数据的安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

设备上所有静止的数据已双重加密，已控制对数据的访问，并在设备停用后从数据磁盘安全地清除数据。

#### <a name="double-encryption-of-data"></a>数据的双重加密

磁盘上的数据受两层加密保护：

- 第一层加密是数据卷上的 BitLocker XTS-AES 256 位加密。
- 第二层是具有内置加密的硬盘。
- OS 卷具有 BitLocker 作为单层加密。

> [!NOTE]
> OS 磁盘具有单层 BitLocker XTS-AES-256 软件加密。

激活设备后，系统会提示你保存一个密钥文件，该文件包含恢复密钥，如果设备无法启动，此密钥可帮助恢复设备上的数据。 此文件中有两个密钥：

- 一个密钥可恢复 OS 卷上的设备配置。
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- 第二个密钥可解锁数据磁盘中的硬件加密。

> [!IMPORTANT]
> 请将密钥文件保存在设备本身之外的安全位置。 如果设备无法启动，而你又没有密钥，则可能会导致数据丢失。

- 某些恢复方案将提示你输入已保存的密钥文件。 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>限制对数据的访问

对共享和存储帐户中存储的数据的访问受到限制。

- 访问共享数据的 SMB 客户端需要与共享关联的用户凭据。 这些凭据是在创建共享时定义的。
- 访问共享的 NFS 客户端需要在创建共享时显式添加其 IP 地址。
- 在设备上创建的 Edge 存储帐户是本地帐户，受数据磁盘上的加密保护。 这些 Edge 存储帐户映射到的 Azure 存储帐户受订阅和与 Edge 存储帐户关联的两个 512 位存储访问密钥保护（这些密钥不同于与 Azure 存储帐户关联的密钥）。 有关详细信息，请参阅[保护存储帐户中的数据](#protect-data-in-storage-accounts)。
- BitLocker XTS-AES 256 位加密用于保护本地数据。

#### <a name="secure-data-erasure"></a>安全数据擦除

当设备进行硬重置时，将对设备执行安全擦除。 安全擦除使用 NIST SP 800-88r1 清除对磁盘执行数据擦除。

### <a name="protect-data-in-flight"></a>保护使用中的数据

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>保护存储帐户中的数据

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- 定期轮换并[同步你的存储帐户密钥](azure-stack-edge-gpu-manage-storage-accounts.md)，以帮助保护你的存储帐户，使未经授权的用户无法访问它。

## <a name="manage-personal-information"></a>管理个人信息

Azure Stack Edge 服务在以下场景收集个人信息：

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

要查看可访问共享的用户列表或删除某个共享，请按照[管理 Azure Stack Edge 上的共享](azure-stack-edge-gpu-manage-shares.md)中的步骤操作。

有关详细信息，请在[信任中心](https://www.microsoft.com/trustcenter)查看 Microsoft 隐私策略。

## <a name="next-steps"></a>后续步骤

[部署 Azure Stack Edge Pro R 设备](azure-stack-edge-gpu-deploy-prep.md)
