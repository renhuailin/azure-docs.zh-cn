---
title: 使用混合模式时的已知问题/迁移限制
description: 了解在混合模式下使用 Azure 数据库迁移服务时的已知问题/迁移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 819fe5ced6c91819c817065305a31fca456ea5c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91291837"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>使用混合模式时的已知问题/迁移限制

以下各部分将介绍与在混合模式下使用 Azure 数据库迁移服务相关的已知问题和限制。

## <a name="installer-fails-to-authenticate"></a>安装程序无法进行身份验证

证书上传到 AdApp 后，需要等待长达几分钟的时间才能向 Azure 进行身份验证。 安装程序会在延迟一段时间后尝试重试，但传播延迟可能比重试时间要长，并且你会看到一条“FailedToGetAccessTokenException”消息。 如果已将证书上传到正确的 AdApp 并在 dmsSettings.json 中提供了正确的 AppId，请尝试再次运行安装命令。

## <a name="service-offline-after-successful-installation"></a>成功安装后，服务“脱机”

如果在安装过程成功完成后服务显示为脱机，请尝试执行以下步骤。

1. 在 Azure 门户的 Azure 数据库迁移服务实例中，导航到“混合”设置选项卡，然后通过检查已注册辅助角色的网格来验证是否已注册了该辅助角色。

    此辅助角色的状态应当为“联机”，但如果存在问题，则它可能显示为“脱机”。

2. 在辅助角色计算机上，通过运行以下 PowerShell 命令来检查服务的状态：

    ```
    Get-Service Scenario*
    ```

    此命令提供运行辅助角色的 Windows 服务的状态。 应当只有一个结果。 如果辅助角色已停止，则可以尝试使用以下 PowerShell 命令将其重启：

    ```
    Start-Service Scenario*
    ```

    还可以在 Windows 服务 UI 中检查该服务。

3. 如果该 Windows 服务在“正在运行”与“已停止”之间循环，则表明辅助角色在启动时遇到问题。 请检查 Azure 数据库迁移服务混合辅助角色日志来确定问题。

    - 安装过程日志存储在“logs”文件夹中，该文件夹所在的文件夹正是从中运行了安装程序可执行文件的文件夹。
    - Azure 数据库迁移服务混合辅助角色日志存储在“WorkerLogs”文件夹中，该文件夹所在的文件夹正是安装了辅助角色的文件夹。 混合辅助角色日志文件的默认位置为“C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs”。

## <a name="using-your-own-signed-certificate"></a>使用自己的已签名证书

操作 GenerateCert 生成的证书是一个自签名证书。根据你的内部安全策略，此证书可能不会被接受。 你可以不使用此证书，而是提供自己的证书，并在 dmsSettings.json 中提供指纹。 需要将此证书上传到 AdApp，并将其安装在要安装 Azure 数据库迁移服务混合辅助角色的计算机上。 然后，将包含私钥的此证书安装到本地计算机证书存储中。

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>以低特权帐户身份运行辅助角色服务

默认情况下，Azure 数据库迁移服务混合辅助角色服务以“本地系统”帐户身份运行。 只要你使用的帐户具有网络权限，你就可以更改用于此服务的帐户。 若要更改服务“运行方式”帐户，请使用以下过程。

1. 通过 Windows 服务或通过 PowerShell 中的 Stop-Service 命令停止该服务。

2. 更新服务以使用不同的登录帐户。

3. 在本地计算机证书的 certmgr 中，为新帐户授予对“DMS 混合应用密钥”和“DMS 方案引擎密钥对”证书的私钥权限。

    a. 打开 certmgr 以查看以下密钥：

    - DMS 混合应用密钥
    - DMS 混合辅助角色安装密钥
    - DMS 方案引擎密钥对

    b. 右键单击“DMS 混合应用密钥”条目，指向“所有任务”，然后选择“管理私钥”。

    c. 在“安全性”选项卡上，选择“添加”，然后输入帐户名称。

    d. 使用相同的步骤为新帐户授予对“DMS 方案引擎密钥对”证书的私钥权限。

## <a name="unregistering-the-worker-manually"></a>手动注销辅助角色

如果你不再有权访问该辅助角色计算机，可通过执行以下步骤，注销该辅助角色并重复使用 Azure 数据库迁移服务实例：

1. 在 Azure 门户中，转到你的 Azure 数据库迁移服务实例，然后导航到“混合”设置页。

   你的辅助角色条目将显示在列表中，其状态显示为“脱机”。

2. 在辅助角色条目列表的最右侧，选择省略号，然后选择“注销”。

## <a name="addressing-issues-for-specific-migration-scenarios"></a>解决特定迁移方案的问题

以下各部分介绍了与使用 Azure 数据库迁移服务混合模式执行联机迁移相关的特定于方案的问题。

### <a name="online-migrations-to-azure-sql-managed-instance"></a>联机迁移到 Azure SQL 托管实例

**CPU 使用率高**

问题：联机迁移到 SQL 托管实例时，如果有太多备份或备份太大，则运行混合辅助角色的计算机会遇到 CPU 使用率过高的问题。

缓解：若要缓解此问题，可以使用压缩的备份，可以拆分迁移以便迁移使用多个共享，还可以纵向扩展运行混合辅助角色的计算机。
