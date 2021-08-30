---
title: Azure Stack Edge Pro FPGA 安全性
description: 介绍了安全和隐私功能，这些功能用于保护 Azure Stack Edge Pro FPGA 设备、服务以及本地和云中的数据。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: b2c6ee6e1bc494f0bef4a04b07115c400c8ce1ec
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2021
ms.locfileid: "112914042"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Azure Stack Edge 安全性和数据保护

当采用新技术时（尤其是在将此技术用于机密或专有数据时），安全性是一个主要问题。 Azure Stack Edge 有助于确保只有授权实体才能查看、修改或删除数据。

本文介绍了有助于保护每个解决方案组件及其中存储的数据的 Azure Stack Edge 安全功能。

Azure Stack Edge 由四个相互交互的主要组件组成：

- 在 Azure 中托管的 Azure Stack Edge 服务。 用于创建设备订单、配置设备并跟踪订单直至完成的管理资源。
- Azure Stack Edge Pro FPGA 设备。 寄送给你的传输设备，这样你就可以将本地数据导入 Azure。
- 连接到设备的客户端/主机。 基础结构中连接到 Azure Stack Edge Pro FPGA 设备的客户端，它们包含需要保护的数据。
- 云存储。 Azure 云平台中存储数据的位置。 此位置通常是与你创建的 Azure Stack Edge 资源关联的存储帐户。

## <a name="azure-stack-edge-service-protection"></a>Azure Stack Edge 服务保护

Azure Stack Edge 服务是托管在 Azure 中的管理服务。 此服务用于配置和管理设备。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Azure Stack Edge 设备保护

Azure Stack Edge 设备是一种本地设备，它可以通过在本地处理数据，然后将数据发送到 Azure，从而帮助转换数据。 你的设备：

- 需要激活密钥来访问 Azure Stack Edge 服务。
- 始终受设备密码保护。
- 是锁定设备。 设备 BMC 和 BIOS 都受密码保护。 BIOS 受保护，仅可供有限的用户访问。
- 已启用了安全启动。
- 运行 Windows Defender Device Guard。 使用 Device Guard，你可以仅运行你在代码完整性策略中定义的受信任应用程序。

### <a name="protect-the-device-via-activation-key"></a>通过激活密钥保护设备

只有授权的 Azure Stack Edge 设备才被允许加入你在 Azure 订阅中创建的 Azure Stack Edge 服务。 若要授权设备，你需要使用激活密钥来激活设备访问 Azure Stack Edge 服务。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

有关详细信息，请参阅[获取激活密钥](azure-stack-edge-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>通过密码保护设备

密码确保只有授权用户才能访问你的数据。 Azure Stack Edge 设备在锁定状态下启动。

方法：

- 通过浏览器连接到设备的本地 Web UI，然后提供密码来登录设备。
- 通过 HTTP 远程连接到设备 PowerShell 界面。 默认情况下，远程管理处于启用状态。 然后，你可以提供设备密码来登录设备。 有关详细信息，请参阅[远程连接到 Azure Stack Edge Pro FPGA 设备](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本地 Web UI [更改密码](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access)。 如果更改密码，请务必通知所有远程访问用户，以便他们在登录时不会遇到问题。

## <a name="protect-your-data"></a>保护数据

此部分介绍了用于保护传输中数据和已存储数据的 Azure Stack Edge Pro FPGA 安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256 位加密用于保护本地数据。


### <a name="protect-data-in-flight"></a>保护传输中数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>通过存储帐户保护数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 定期轮换并[同步你的存储帐户密钥](azure-stack-edge-manage-shares.md#sync-storage-keys)，以帮助保护你的存储帐户免受未授权的用户侵害。

## <a name="manage-personal-information"></a>管理个人信息

Azure Stack Edge 服务在以下方案中收集个人信息：

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要查看可以访问或删除共享的用户的列表，请按照[管理 Azure Stack Edge Pro FPGA 上的共享](azure-stack-edge-manage-shares.md)中的步骤操作。

有关详细信息，请查阅[信任中心](https://www.microsoft.com/trustcenter)内的 Microsoft 隐私策略。

## <a name="next-steps"></a>后续步骤

[部署 Azure Stack Edge Pro FPGA 设备](azure-stack-edge-deploy-prep.md)
