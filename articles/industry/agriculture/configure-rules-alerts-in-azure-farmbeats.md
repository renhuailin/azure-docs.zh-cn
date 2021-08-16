---
title: 配置规则并管理警报
description: 介绍如何在 FarmBeats 中配置规则和管理警报
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: a04f973cbfa3a68016065f50e9e2ff4f7566da94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182921"
---
# <a name="configure-rules-and-manage-alerts"></a>配置规则并管理警报

借助 Azure FarmBeats，除了来自在场中部署的传感器和设备的传感器数据之外，还可根据业务逻辑创建规则。 每当传感器值超过阈值时，规则都会在系统中触发警报。 通过查看和分析在超过阈值后创建的警报，可快速处理任何问题并获取所需的解决方案。

## <a name="create-rule"></a>创建规则

1. 在主页上，转到“规则”。
2. 选择“新建规则”。 这将显示“新建规则”窗口。

    ![突出显示“新建规则”按钮和“新建规则”部分的屏幕截图。](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. 输入规则名称和规则说明，然后从“选择场”下拉菜单中选择一个场  。
4. 键入场名称来选择对应的场，该窗口中会显示“条件”部分。  

    ![突出显示“条件”部分的屏幕截图。](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. 在“条件”中，为“度量值”、“运算符”和“值”输入相应的值   。
6. 在“度量值”下拉菜单中键入度量值名称。
7. 选择“+添加条件”，将更多条件添加到规则中。
8. 选择“严重性级别”。
9. 在“操作”中，打开“已启用电子邮件”切换按钮来启用电子邮件警报 。

    ![显示“已启用电子邮件”选项的屏幕截图。](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. 输入要向其发送电子邮件警报的电子邮件地址，还需填写电子邮件主题和其他说明  。  
11. 在“规则状态”中，打开“已启用”切换按钮来启用或禁用规则 。
    可查看将受规则影响的设备的数量。
12. 选择“应用”来创建规则。

## <a name="view-rule"></a>查看值

“场”页面显示了可用规则的列表。 选择规则名称。 窗口将显示适用于所选规则的以下详细信息：
 - 规则名称
 - 指向与规则关联的场的链接
 - 创建日期
 - 上次更新日期
 - 严重性级别
 - 规则状态
 - 条件列表  
 - 受规则影响的设备数

    ![显示“规则详细信息”屏幕的屏幕截图。](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>编辑规则

若要编辑规则，请执行以下步骤：

1. 在主页上，从左侧导航菜单中选择“规则”。
   这将显示规则窗口。
2. 选择你想要编辑的规则。

    ![显示所选规则的屏幕截图。](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. 从操作栏中选择“编辑”，这将显示“编辑规则”窗口 。

    ![显示“编辑规则”屏幕的屏幕截图。](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. 更改规则名称和规则说明，然后从“选择场”下拉菜单中选择一个场  。
5. 键入场名称来选择对应的场，该窗口中会显示“条件”。  
6. 在“条件”中，编辑“度量值”、“运算符”和“值”   。
7. 在“度量值”下拉菜单中键入度量值名称。
8. 选择“+添加条件”，在规则中添加/编辑条件。

    ![突出显示“添加条件”按钮的屏幕截图。](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  选择“严重性级别”。  
10. 在“操作”中，打开“已启用电子邮件”切换按钮来启用电子邮件警报 。
11. 编辑要向其发送电子邮件警报的电子邮件地址，还需填写电子邮件主题和其他说明  。  
12. 在“规则状态”中，打开“已启用”切换按钮来启用或禁用规则 。
可查看将受此规则影响的设备的数量。
13. 选择“应用”来编辑规则。

## <a name="change-rule-status"></a>更改规则状态

若要更改规则的状态，请执行以下步骤：

1. 在主页上，从左侧导航菜单中选择“规则”。 这将显示规则窗口。
2. 选择要更改其状态的规则。

    ![显示“更改状态”按钮的屏幕截图。](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. 从操作栏中选择“更改状态”。 这将显示“更改状态”窗口。

    ![显示“更改状态”屏幕的屏幕截图。](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. 使用“更改状态”切换按钮更改规则状态。
   可查看将受规则影响的设备的数量。
4. 选择“应用”来更改规则状态。

## <a name="delete-rule"></a>删除规则

若要删除规则，请执行以下步骤：

1. 在主页上，从左侧导航菜单中选择“规则”。 这将显示规则窗口。
2. 选择你想要删除的规则。

    ![突出显示“删除”按钮的屏幕截图。](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. 从操作栏中选择“删除”。

    ![FarmBeats 项目](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. 这将显示“删除规则”对话框。 选择“删除”。
