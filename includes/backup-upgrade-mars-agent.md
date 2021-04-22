---
title: 升级 Azure 备份代理
description: 此信息说明为什么应升级 Azure 备份代理，以及应在何处下载升级。
services: backup
cloud: ''
suite: ''
author: v-amallick
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: v-amallick
ms.openlocfilehash: bf77103db93652e1df837f6b1032b5e53bd41e1f
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294098"
---
## <a name="upgrade-the-mars-agent"></a>升级 MARS 代理

低于 2.0.9083.0 的 Microsoft Azure 恢复服务 (MARS) 代理版本依赖于 Azure 访问控制服务。 MARS 代理也称为 Azure 备份代理。

在 2018 年，Microsoft [弃用了 Azure 访问控制服务](../articles/active-directory/azuread-dev/active-directory-acs-migration.md)。 从 2018 年 3 月 19 日开始，低于 2.0.9083.0 的所有 MARS 代理版本会遇到备份失败。 若要避免或解决备份失败，请[将 MARS 代理升级到最新版本](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent)。 若要确定需要 MARS 代理升级的服务器，请按照[升级 Microsoft Azure 恢复服务 (MARS) 代理](../articles/backup/upgrade-mars-agent.md)中的步骤操作。

MARS 代理用于将文件、文件夹和系统状态数据备份到 Azure。 System Center DPM 和 Azure 备份服务器使用 MARS 代理将数据备份到 Azure。
