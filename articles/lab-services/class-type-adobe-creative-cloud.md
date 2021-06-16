---
title: 借助 Azure 实验室服务设置使用 Adobe Creative Cloud 的实验室 | Microsoft Docs
description: 了解如何设置使用 Adobe Creative Cloud 的数字艺术与媒体课程实验室。
author: nicolela
ms.topic: article
ms.date: 04/21/2021
ms.author: nicolela
ms.openlocfilehash: 210fa647f12a786e357902bef3bbb3b20cca8077
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962655"
---
# <a name="set-up-a-lab-for-adobe-creative-cloud"></a>设置 Adobe Creative Cloud 实验室
[Adobe Creative Cloud](https://www.adobe.com/creativecloud.html) 是桌面应用与 Web 服务的集合，用于摄影、设计、视频、Web、用户体验 (UX) 等。  大学和 K-12 学校都会在数字艺术与媒体课程中使用 Creative Cloud。  与典型平板电脑、笔记本电脑或工作站支持相比，某些 Creative Cloud 的媒体进程可能需要更强大的计算和可视化 (GPU) 能力。  可以借助 Azure 实验室服务灵活选择各种虚拟机 (VM) 大小（包括 GPU 大小）。

本文将介绍如何设置使用 Creative Cloud 的课程。

## <a name="licensing"></a>许可
若要在实验室 VM 上使用 Creative Cloud，必须使用[命名用户许可](https://helpx.adobe.com/enterprise/kb/technical-support-boundaries-virtualized-server-based.html#main_Licensing_considerations)，该许可是支持在虚拟机上进行部署的唯一许可类型。  每台实验室 VM 均可访问 Internet，如此一来，学生便可通过登录软件激活 Creative Cloud 应用。  学生登录后，其身份验证令牌将被缓存到用户配置文件中，以便他们无需在其 VM 上再次登录。  有关详细信息，请阅读 [Adobe 有关许可的文章](https://helpx.adobe.com/enterprise/using/licensing.html)。

## <a name="lab-configuration"></a>实验室配置
若要设置此实验室，需要有 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，便可在 Azure 实验室服务中创建新的实验室帐户。 若要详细了解如何新建实验室帐户，请参阅有关[如何设置实验室帐户](./tutorial-setup-lab-account.md)的教程。 也可以使用现有实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

为实验室帐户启用下表中所述的设置。 若要详细了解如何启用市场映像，请参阅有关[如何指定可供实验室创建者使用的市场映像](./specify-marketplace-images.md)的文章。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
|市场映像| 启用 Windows 10 映像，以便在实验室帐户中使用。|

### <a name="lab-settings"></a>实验室设置

需要用于实验室的 VM 大小取决于你的学生将会创建的项目类型。  大多数 [Creative Cloud 应用](https://helpx.adobe.com/creative-cloud/system-requirements.html)支持基于 GPU 的加速，并且需要 GPU 才能让各项功能正常使用。  为了确保选择适当的 VM 大小，建议测试你的学生要创建的项目，以确保性能足够出众。  下表显示了推荐用于 Creative Cloud 的 [VM 大小](./administrator-guide.md#vm-sizing)。  

| 实验室设置 | 值/说明 |
| ------------ | ------------------ |
|虚拟机大小| 小型 GPU（可视化）。  此 VM 最适合使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏和编码。|  
|虚拟机映像| Windows 10 |

> [!NOTE]
> 配置“小型 GPU（可视化）”虚拟机大小，可带来高性能图形体验，并满足 [Adobe 对每个应用的系统要求](https://helpx.adobe.com/creative-cloud/system-requirements.html)。  请务必选择“小型 GPU（可视化）”，而非“小型 GPU（计算）”。  若要详细了解此虚拟机大小，请参阅有关[如何为实验室设置 GPU](./how-to-setup-lab-gpu.md) 的文章。

## <a name="template-virtual-machine-configuration"></a>模板虚拟机配置

### <a name="creative-cloud-deployment-package"></a>Creative Cloud 部署包
安装 Creative Cloud 需要使用部署包。 通常情况下，部署包是由 IT 部门使用 Adobe 的管理控制台创建的。  在 IT 部门创建部署包时，他们还可以选择启用自助服务。  可以采用多种方法为部署包启用自助服务：
-    创建自助服务包。
-    借助已开启的自助服务提升权限创建托管包。

启用自助服务后，不会安装整个 Creative Cloud 的应用集合。  相反，学生可以使用 Creative Cloud 桌面应用自行安装应用。  下面是此方法的部分主要优势：
- 整个 Creative Cloud 安装包约 25 GB。  如果学生只安装需要的应用，这样做有助于优化磁盘空间。 实验室 VM 的磁盘大小为 128 GB。
- 可以在发布之前，选择在模板 VM 上安装一部分应用。  如此一来，学生 VM 将默认安装部分应用，并且学生可以根据需要添加更多应用。
- 你可以避免重新发布模板 VM，因为学生可以在实验室生存期内的任何时间点在其 VM 上安装其他应用。  否则，IT 人员或教师将需要在模板 VM 上安装其他应用，然后重新发布。  重新发布会导致学生的 VM 重置，并且未在外部装置上保存的各项工作将会丢失。

如果在禁用自助服务的情况下使用托管部署包，学生将无法安装其自己的应用。  在这种情况下，IT 人员必须指定要安装 Creative Cloud 应用。

有关详细信息，请阅读 [Adobe 有关创建包的步骤](https://helpx.adobe.com/enterprise/admin-guide.html/enterprise/using/create-nul-packages.ug.html)。

### <a name="install-creative-cloud"></a>安装 Creative Cloud
创建模板虚拟机后，请按照以下步骤借助 Creative Cloud 设置实验室模板虚拟机 (VM)。
1. 启动模板 VM，并使用 RDP 进行连接。
1. 如需安装 Creative Cloud，请下载 IT 部门提供的或直接从 [Adobe 的管理控制台](https://adminconsole.adobe.com/)下载部署包。
1. 运行部署包文件。  此操作将会根据是否启用或禁用了自助服务来安装 Creative Cloud 桌面应用和/或指定的 Creative Cloud 应用。
有关详细信息，请参阅 [Adobe 的部署步骤](https://helpx.adobe.com/enterprise/admin-guide.html/enterprise/using/deploy-packages.ug.html)。
1. 设置模板 VM 后，请发布用于在实验室中创建所有学生 VM 的[模板 VM 映像](how-to-create-manage-template.md)。

### <a name="storage"></a>存储
如前所述，Azure 实验室 VM 的磁盘大小为 128 GB。  如果你的学生需要额外存储空间来保存大型课程资产，或者他们需要访问共享课程资产，则应考虑使用外部文件存储装置。  有关详细信息，请阅读以下文章：
-    [在实验室服务中使用外部文件存储](how-to-attach-external-storage.md)
-    [安装和配置 OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive)

### <a name="save-template-vm-image"></a>保存模板 VM 映像
请考虑保存模板 VM，以供将来使用。  如需保存模板 VM，请参阅[将图像保存到共享映像库](./how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)。
- 启用自助服务后，模板 VM 的映像将安装 Creative Cloud 桌面应用。  之后，教师可以重复使用此映像创建实验室，并选择要安装的 Creative Cloud 应用。  这样做有助于降低 IT 开销，这是因为教师可以独立设置实验室，并且可以完全控制其课程所需的 Creative Cloud 应用。
- 禁用自助服务后，模板 VM 的映像将已安装指定的 Creative Cloud 应用。  教师可以重复使用此映像创建实验室；但是，他们无法安装其他 Creative Cloud 应用。

## <a name="cost"></a>成本

我们将在本部分探讨此课程可能的成本估算。  我们将采用一个包含 25 名学生的班级，计划课程时间为 20 小时。  此外，每位学生会在计划课程时间之外获得 10 小时配额，用于完成家庭作业或课外作业。 我们选择的虚拟机大小是“小型 GPU (可视化)”，即 160 个实验室单位。

25 名学生（计划的 20 小时 + 配额的 10 小时）160 个实验室单位 * 每小时 0.01 美元 = 1200.00 美元\*\*

>[!IMPORTANT]
> 成本估算仅用于示例目的。  有关定价的最新详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。  

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室时通用的。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)