---
title: 删除 Azure 自动化运行方式帐户
description: 本文介绍如何使用 PowerShell 或 Azure 门户删除运行方式帐户。
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055682"
---
# <a name="delete-an-azure-automation-run-as-account"></a>删除 Azure 自动化运行方式帐户

Azure 自动化中的运行方式帐户提供身份验证，以使用自动化 runbook 和其他自动化功能管理 Azure 资源管理器或 Azure 经典部署模型上的资源。 本文介绍如何删除运行方式帐户或经典运行方式帐户。 执行此操作时，将保留自动化帐户。 删除运行方式帐户后，可以在 Azure 门户中或者通过提供的 PowerShell 脚本重新创建运行方式帐户。

## <a name="delete-a-run-as-or-classic-run-as-account"></a>删除运行方式帐户或经典运行方式帐户

1. 在 Azure 门户中，打开自动化帐户。

2. 在左侧窗格中，选择帐户设置部分中的“运行方式帐户”。

3. 在“运行方式帐户”属性页上，选择要删除的运行方式帐户或经典运行方式帐户。

4. 在所选帐户的“属性”窗格中单击“删除”。

   ![删除运行方式帐户](media/delete-run-as-account/automation-account-delete-run-as.png)

5. 帐户删除过程中，可以在菜单的“通知”下面跟踪进度。

## <a name="next-steps"></a>后续步骤

若要重新创建运行方式帐户或经典运行方式帐户，请参阅 [创建运行方式](create-run-as-account.md)帐户。