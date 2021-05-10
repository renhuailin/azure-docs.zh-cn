---
title: Azure Data Box Gateway 安全性 | Microsoft Docs
description: 介绍保护 Azure Data Box Gateway 虚拟设备、服务以及本地数据和云中数据的安全和隐私功能。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 13d3809611714992f24a66a96c22074e69fba9bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786650"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box Gateway 安全性和数据保护

安全性是采用新技术（尤其是在该技术用于处理机密或专有数据时）时关心的主要问题。 Azure Data Box Gateway 可帮助确保只有授权实体才能查看、修改或删除数据。

本文介绍了帮助保护每个解决方案组件及其存储的数据的 Azure Data Box Gateway 安全功能。

Data Box Gateway 解决方案包括四个主要的交互组件：

- **Azure 中托管的 Data Box Gateway 服务**。 用于创建设备订单、配置设备，然后跟踪订单完成状态的管理资源。
- **Data Box Gateway 设备**。 在所提供系统的虚拟机监控程序中预配的虚拟设备。 此虚拟设备用于将本地数据导入 Azure 中。
- **连接到设备的客户端/主机**。 基础结构中的客户端，它们连接到 Data Box Gateway 设备，并包含需要保护的数据。
- **云存储**。 Azure 云平台中存储数据的位置。 该位置通常是链接到所创建的 Data Box Gateway 资源的存储帐户。

## <a name="data-box-gateway-service-protection"></a>Data Box Gateway 服务保护

Data Box Gateway 服务是 Azure 中托管的一项管理服务。 该服务用于配置和管理设备。

[!INCLUDE [data-box-gateway-service-protection](../../includes/data-box-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box Gateway 设备保护

Data Box Gateway 设备是在所提供的本地系统的虚拟机监控程序中预配的虚拟设备。 该设备可帮助将数据发送到 Azure。 你的设备：

- 需要使用激活密钥来访问 Azure Stack Edge Pro/Data Box Gateway 服务。
- 始终受设备密码保护。
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>通过激活密钥保护设备

仅允许授权 Data Box Gateway 设备加入在 Azure 订阅中创建的 Data Box Gateway 服务。 若要授权设备，需要使用通过 Data Box Gateway 服务激活设备的激活密钥。

[!INCLUDE [data-box-gateway-activation-key](../../includes/data-box-gateway-activation-key.md)]

有关详细信息，请参阅[获取激活密钥](data-box-gateway-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>通过密码保护设备

密码可确保只有授权用户才能访问数据。 Data Box Gateway 设备将在锁定状态下启动。

方法：

- 通过浏览器连接到设备的本地 Web UI，然后提供用于登录到设备的密码。
- 通过 HTTP 远程连接到设备 PowerShell 界面。 默认情况下启用远程管理。 然后，你可以提供用于登录设备的设备密码。 有关详细信息，请参阅[远程连接到 Data Box Gateway 设备](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-gateway-password-best-practices](../../includes/data-box-gateway-password-best-practices.md)]
- 使用本地 Web UI [更改密码](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 如果更改密码，请务必通知所有远程访问用户，以便用户在登录时不会出现问题。

## <a name="protect-your-data"></a>保护数据

本节介绍保护传输中数据和存储数据的 Data Box Gateway 安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

[!INCLUDE [data-box-gateway-data-rest](../../includes/data-box-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>保护使用中的数据

[!INCLUDE [data-box-gateway-data-flight](../../includes/data-box-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>使用存储帐户保护数据

[!INCLUDE [data-box-gateway-data-storage-accounts](../../includes/data-box-gateway-protect-data-storage-accounts.md)]

- 定期轮换并[同步你的存储帐户密钥](data-box-gateway-manage-shares.md#sync-storage-keys)，以帮助保护你的存储帐户，使未经授权的用户无法访问它。

### <a name="protect-the-device-data-using-bitlocker"></a>使用 BitLocker 保护设备数据

若要保护 Data Box Gateway 虚拟机上的虚拟磁盘，建议启用 BitLocker。 默认情况下，不启用 BitLocker。 有关详细信息，请参阅：

- [Hyper-V 管理器中的加密支持设置](/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [虚拟机中的 BitLocker 支持](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>管理个人信息

Data Box Gateway 服务在以下场景收集个人信息：

[!INCLUDE [data-box-gateway-manage-personal-data](../../includes/data-box-gateway-manage-personal-data.md)]

要查看可访问或删除共享的用户的列表，请按照[管理 Data Box Gateway 上的共享](data-box-gateway-manage-shares.md)中的步骤操作。

有关详细信息，请在[信任中心](https://www.microsoft.com/trustcenter)查看 Microsoft 隐私策略。

## <a name="next-steps"></a>后续步骤

[部署 Data Box Gateway 设备](data-box-gateway-deploy-prep.md)
