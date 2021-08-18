---
title: Azure AD Connect：为 Microsoft 365 资源配置首选数据位置
description: 介绍如何使用 Azure Active Directory Connect 同步将 Microsoft 365 用户资源放在靠近用户的位置。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/09/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: edbc8c0151be2bc65dd653e996413d4c7877b1ef
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229753"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-microsoft-365-resources"></a>Azure Active Directory Connect 同步：为 Microsoft 365 资源配置首选数据位置
本主题的目的是介绍如何在 Azure Active Directory (Azure AD) Connect 同步中配置首选数据位置的属性。当某人使用了 Microsoft 365 中的多地区功能时，可使用此属性来指定用户的 Microsoft 365 数据的地理位置。 （术语 *区域* 和 *地域* 可以互换使用。）

## <a name="supported-multi-geo-locations"></a>支持的多地理位置
有关 Azure AD Connect 支持的所有地理位置的列表，请参阅 [Microsoft 365 多地理位置可用性](/microsoft-365/enterprise/microsoft-365-multi-geo?view=o365-worldwide#microsoft-365-multi-geo-availability)

## <a name="enable-synchronization-of-preferred-data-location"></a>启用首选数据位置的同步
默认情况下，用户使用的 Microsoft 365 资源位于你的 Azure AD 租户所在地区。 例如，如果你的租户位于北美，则用户的 Exchange 邮箱也位于北美。 对于跨国组织而言，这可能不是最佳情况。

通过设置属性 **preferredDataLocation**，可以定义用户的地域。 可以将用户的 Microsoft 365 资源（例如邮箱和 OneDrive）放在用户所在的同一地区，同时仍对整个组织使用一个租户。

> [!IMPORTANT]
> 多地区功能目前适用于拥有有效企业协议和至少 250 个 Microsoft 365 服务订阅的客户。 有关详细信息，请咨询 Microsoft 代表。
>
> 有关 Azure AD Connect 支持的所有地理位置的列表，请参阅 [Microsoft 365 多地理位置可用性](/microsoft-365/enterprise/microsoft-365-multi-geo?view=o365-worldwide#microsoft-365-multi-geo-availability)。




### <a name="azure-ad-connect-support-for-synchronization"></a>Azure AD Connect 对同步的支持

Azure AD Connect 在版本 1.1.524.0 及更高版本中支持对 **User** 对象的 **preferredDataLocation** 属性进行同步。 具体而言：

* Azure AD 连接器中对象类型 **User** 的架构已扩展，现在包含 **preferredDataLocation** 属性。 该属性为单值字符串类型。
* Metaverse 中对象类型 **Person** 的架构已扩展，现在包含 **preferredDataLocation** 属性。 该属性为单值字符串类型。

默认情况下，没有为同步启用 **preferredDataLocation**。 此功能适用于较大的组织。 Windows Server 2019 中的 Active Directory 架构的属性“msDS-preferredDataLocation”可用于实现此目的。 如果尚未更新 Active Directory 架构且无法更新，则必须标识一个属性，以便为用户保留 Microsoft 365 地区。 每个组织的此属性都不相同。

> [!IMPORTANT]
> Azure AD 允许使用 Azure AD PowerShell 直接配置 **云 User 对象** 上的 **preferredDataLocation** 属性。 若要配置 **已同步 User 对象** 上的此属性，必须使用 Azure AD Connect。

在启用同步之前：

* 如果尚未将 Active Directory 架构升级为 2019，则确定要用作源属性的本地 Active Directory 属性。 它的类型应当是 **单值字符串**。
* 如果之前使用 Azure AD PowerShell 在 Azure AD 中的现有 **已同步 User 对象** 上配置了 **preferredDataLocation** 属性，则必须将属性值向后移植到本地 Active Directory 中的对应 **User** 对象。

    > [!IMPORTANT]
    > 如果不向后移植这些值，则当启用了 **preferredDataLocation** 属性的同步时，Azure AD Connect 会删除 Azure AD 中的现有属性值。

* 现在，至少在几个本地 Active Directory User 对象上配置源属性。 以后可以使用此属性进行验证。

以下各部分提供了启用 **preferredDataLocation** 属性同步的步骤：

> [!NOTE]
> 这些步骤是在采用单林拓扑且没有自定义同步规则的 Azure AD 部署的上下文中介绍的。 如果你有多林拓扑、配置了自定义同步规则或者具有暂存服务器，则应当相应地调整步骤。

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>步骤 1：禁用同步计划程序，并验证是否没有正在进行的同步操作
为避免将意外的更改导出到 Azure AD，请确保不要在更新同步规则的中途进行同步。 若要禁用内置的同步计划程序，请执行以下操作：

1. 在 Azure AD Connect 服务器上启动 PowerShell 会话。
2. 通过运行以下 cmdlet 来禁用计划的同步：`Set-ADSyncScheduler -SyncCycleEnabled $false`。
3. 转到“开始” > “同步服务”，启动 Synchronization Service Manager。
4. 选择“操作”选项卡，确认已不存在状态为“正在进行”的操作。

![Synchronization Service Manager 的屏幕截图](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>步骤 2：刷新 Active Directory 的架构
如果已将 Active Directory 架构更新为 2019，并在架构扩展之前安装了 Connect，则 Connect 架构缓存没有已更新的架构。 必须从向导中刷新架构，使其显示在 UI 中。

1. 从桌面启动 Azure AD Connect 向导。
2. 选择选项“刷新目录架构”，然后单击“下一步”。
3. 输入 Azure AD 凭据，然后单击“下一步”。
4. 在“刷新目录架构”页上，确保选择所有林并单击“下一步”。
5. 完成后关闭向导。

![Connect 向导中“刷新目录架构”的屏幕截图](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>步骤 3：将源属性添加到本地 Active Directory 连接器架构
仅在运行 Connect 1.3.21 版或更早版本时才需要执行此步骤。如果运行的是 1.4.18 或更新版本，则跳至步骤 5。  
并非所有 Azure AD 属性都将导入到本地 Active Directory 连接器空间。 如果选择使用默认情况下不进行同步的属性，则需要将其导入。 要将源属性添加到导入属性的列表：

1. 在 Synchronization Service Manager 中选择“连接器”选项卡。
2. 右键单击本地 Active Directory 连接器，并选择“属性”。
3. 在弹出对话框中，转到“选择属性”选项卡。
4. 确保在属性列表中选中你选择使用的源属性。 如果未看到该属性，请选择“全部显示”复选框。
5. 若要保存，请选择“确定”。

![显示突出显示“属性”列表的“Synchronization Service Manager 和属性”对话框的屏幕截图。](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>步骤 4：将 preferredDataLocation 添加到 Azure AD 连接器架构
仅在运行 Connect 1.3.21 版或更早版本时才需要执行此步骤。如果运行的是 1.4.18 或更新版本，则跳至步骤 5。  
默认情况下，**preferredDataLocation** 属性不会导入到 Azure AD 连接器空间。 若要将其添加到已导入属性的列表，请执行以下操作：

1. 在 Synchronization Service Manager 中选择“连接器”选项卡。
2. 右键单击 Azure AD 连接器并选择“属性”。
3. 在弹出对话框中，转到“选择属性”选项卡。
4. 选择列表中的 **preferredDataLocation** 属性。
5. 若要保存，请选择“确定”。

![Synchronization Service Manager 和“属性”对话框的屏幕截图](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>步骤 5：创建入站同步规则
入站同步规则允许属性值从本地 Active Directory 中的源属性流到 metaverse。

1. 转到“开始” > “同步规则编辑器”，启动“同步规则编辑器”。
2. 将搜索筛选器的“方向”设置为“入站”。 
3. 若要创建新的入站规则，请选择“添加新规则”按钮。
4. 在“说明”选项卡下面提供以下配置：

    | 属性 | 值 | 详细信息 |
    | --- | --- | --- |
    | 名称 | *提供名称* | 例如“In from AD – User preferredDataLocation” |
    | 说明 | *提供自定义说明* |  |
    | 连接的系统 | *选取本地 Active Directory 连接器* |  |
    | 连接的系统对象类型 | **用户** |  |
    | Metaverse 对象类型 | **Person** |  |
    | 链接类型 | **Join** |  |
    | 优先级 | *选择介于 1 和 99 之间的数字* | 1-99 是为自定义同步规则保留的值。 请不要选择已被其他同步规则使用的值。 |

5. 将“范围筛选器”留空以包括所有对象。 可能需要根据 Azure AD Connect 部署调整范围筛选器。
6. 转到“转换”选项卡并实现以下转换规则：

    | 流类型 | 目标属性 | 源 | 应用一次 | 合并类型 |
    | --- | --- | --- | --- | --- |
    |直接 | preferredDataLocation | 选择源属性 | 未选中 | 更新 |

7. 若要创建入站规则，请选择“添加”。

![“创建入站同步规则”的屏幕截图](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>步骤 6：创建出站同步规则
出站同步规则允许属性值从 metaverse 流到 Azure AD 中的 **preferredDataLocation** 属性：

1. 转到“同步规则编辑器”。
2. 将搜索筛选器的“方向”设置为“出站”。 
3. 选择“添加新规则”。
4. 在“说明”选项卡下面提供以下配置：

    | 属性 | 值 | 详细信息 |
    | ----- | ------ | --- |
    | 名称 | *提供名称* | 例如，“Out to Azure AD – User preferredDataLocation” |
    | 说明 | *提供说明* ||
    | 连接的系统 | *选择 Azure AD 连接器* ||
    | 连接的系统对象类型 | **用户** ||
    | Metaverse 对象类型 | **Person** ||
    | 链接类型 | **Join** ||
    | 优先级 | *选择介于 1 和 99 之间的数字* | 1-99 是为自定义同步规则保留的值。 请不要选择已被其他同步规则使用的值。 |

5. 转到“范围筛选器”选项卡，并添加包含两个子句的单个范围筛选器组：

    | 属性 | 运算符 | 值 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | 用户 |
    | cloudMastered | NOTEQUAL | True |

    范围筛选器确定要将此出站同步规则应用到哪些 Azure AD 对象。 在本示例中，我们将使用与“Out to Azure AD – User Identity”OOB（现成）同步规则中相同的范围筛选器。 它可以防止将同步规则应用到未从本地 Active Directory 同步的 User 对象。 可能需要根据 Azure AD Connect 部署调整范围筛选器。

6. 转到“转换”选项卡并实现以下转换规则：

    | 流类型 | 目标属性 | 源 | 应用一次 | 合并类型 |
    | --- | --- | --- | --- | --- |
    | 直接 | preferredDataLocation | preferredDataLocation | 未选中 | 更新 |

7. 关闭“添加”创建出站规则。

![“创建入站同步规则”的屏幕截图](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>步骤 7：运行完全同步周期
一般情况下，完全同步周期是必需的。 这是因为你已向 Active Directory 和 Azure AD 连接器架构添加了新属性并引入了自定义同步规则。 在将更改导出到 Azure AD 之前验证更改。 在手动运行构成完全同步周期的步骤时，可以使用以下步骤来验证更改。

1. 在本地 Active Directory 连接器上运行 **完全导入**：

   1. 在 Synchronization Service Manager 中转到“连接器”选项卡。
   2. 右键单击 **本地 Active Directory 连接器**，并选择“运行”。
   3. 在对话框中，选择“完全导入”，并单击“确定”。
   4. 等待操作完成。

      > [!NOTE]
      > 如果源属性已包含在导入的属性的列表中，则可以在本地 Active Directory 连接器上跳过“完全导入”。 换而言之，在本文前面的步骤 2 中不需要进行任何更改。

2. 在 Azure AD 连接器上运行 **完全导入**：

   1. 右键单击“Azure AD 连接器”，并选择“运行”。 
   2. 在对话框中，选择“完全导入”，并单击“确定”。
   3. 等待操作完成。

3. 验证现有 **User** 对象上的同步规则更改。

   本地 Active Directory 中的源属性和 Azure AD 中的 **preferredDataLocation** 已导入到每个相应的连接器空间。 在继续执行完全同步步骤之前，在本地 Active Directory 连接器空间中的现有 **User** 对象上执行预览。 选择的对象应具有填充的源属性。 能够成功预览 Metaverse 中填充的 **preferredDataLocation** 是一个很好的指标，表明已正确配置了同步规则。 有关如何执行预览的信息，请参阅[验证更改](how-to-connect-sync-change-the-configuration.md#verify-the-change)部分。

4. 在本地 Active Directory 连接器上运行 **完全同步**：

   1. 右键单击 **本地 Active Directory 连接器**，并选择“运行”。
   2. 在对话框中，选择“完全同步”，然后选择“确定”。
   3. 等待操作完成。

5. 验证 Azure AD 的 **挂起的导出**：

   1. 右键单击“Azure AD 连接器”，并选择“搜索连接器空间”。 
   2. 在“搜索连接器空间”对话框中：

        a. 将“范围”设置为“挂起的导出”。 <br>
        b. 选择所有三个复选框，包括“添加”、“修改”和“删除”。<br>
        c. 若要查看包含要导出的更改的对象列表，请选择“搜索”。 若要检查给定对象的更改，请双击该对象。<br>
        d. 验证更改是否符合需要。

6. 在 **Azure AD 连接器** 上运行 **导出**

   1. 右键单击“Azure AD 连接器”，并选择“运行”。 
   2. 在“运行连接器”对话框中，选择“导出”，然后选择“确定”。
   3. 等待操作完成。

> [!NOTE]
> 你可能会注意到，这些步骤未包括 Azure AD 连接器上的完全同步步骤和 Active Directory 连接器上的导出步骤。 由于属性值仅从本地 Active Directory 流向 Azure AD，因此不需要执行这些步骤。

## <a name="step-8-re-enable-sync-scheduler"></a>步骤 8：重新启用同步计划程序
重新启用内置的同步计划程序：

1. 启动 PowerShell 会话。
2. 通过运行以下 cmdlet 来重新启用计划的同步：`Set-ADSyncScheduler -SyncCycleEnabled $true`。

## <a name="step-9-verify-the-result"></a>步骤 9：验证结果
现在，可以验证配置并为用户启用该配置。

1. 将区域添加到用户的选定属性。 可以在此表中找到可用地域的列表。  
![添加到用户的 AD 属性的屏幕截图](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. 等待该属性同步到 Azure AD。
3. 使用 Exchange Online PowerShell 验证是否正确设置了邮箱区域。  
![Exchange Online PowerShell 的屏幕截图](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
假设租户已标记为能够使用此功能，则邮箱会移到正确的区域。 可以通过查看邮箱所在的服务器名称进行此项验证。

## <a name="next-steps"></a>后续步骤

详细了解 Microsoft 365 中的多地区：

* [Ignite 上的多地域会话](https://aka.ms/MultiGeoIgnite)
* [OneDrive 中的多地域](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)
* [SharePoint Online 中的多地域](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)

详细了解同步引擎中的配置模型：

* 在[了解声明性预配](concept-azure-ad-connect-sync-declarative-provisioning.md)中阅读有关配置模型的详细信息。
* 在 [Understanding Declarative Provisioning Expressions](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)（了解声明性预配表达式）中了解有关表达式语言的详细信息。

概述主题：

* [Azure AD Connect 同步：理解和自定义同步](how-to-connect-sync-whatis.md)
* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)