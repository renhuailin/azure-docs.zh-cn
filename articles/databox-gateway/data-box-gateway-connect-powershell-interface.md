---
title: 使用 Windows PowerShell 连接并管理 Azure Data Box Gateway 设备
description: 介绍如何通过 Windows PowerShell 界面连接并管理 Data Box Gateway。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: f3b93bfc9af9bce50c301c10bd372567360d7223
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96581606"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>通过 Windows PowerShell 管理 Azure Data Box Gateway 设备

Azure Data Box Gateway 解决方案使你能够通过网络将数据发送到 Azure。 本文介绍了 Data Box Gateway 设备的一些配置和管理任务。 可以使用 Azure 门户、本地 Web UI 或 Windows PowerShell 界面来管理设备。

本文重点介绍使用 PowerShell 界面执行的任务。

本文包括以下过程：

- 连接到 PowerShell 接口
- 创建支持包
- 上传证书
- 在非 DHCP 环境中启动
- 查看设备信息

## <a name="connect-to-the-powershell-interface"></a>连接到 PowerShell 接口

[!INCLUDE [Connect to admin runspace](../../includes/data-box-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>创建支持包

[!INCLUDE [Create a support package](../../includes/data-box-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>上传证书

[!INCLUDE [Upload certificate](../../includes/data-box-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>在非 DHCP 环境中启动

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>查看设备信息

[!INCLUDE [View device information](../../includes/data-box-gateway-view-device-info.md)]

## <a name="next-steps"></a>后续步骤

- 在 Azure 门户中部署 [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。
