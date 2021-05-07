---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 5bc00f4de95d22eec71f9b1b2504b00f506232dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99213579"
---
### <a name="to-sign-up-for-a-sendgrid-account"></a>注册 SendGrid 帐户
1. 登录到 [Azure 门户][Azure portal]。
2. 在 Azure 门户菜单上或在门户主页中，选择“创建资源”。

    ![该屏幕截图显示了在 Azure 门户菜单中选择了“创建资源”选项。][command-bar-new]
3. 搜索并选择“SendGrid”。

    ![Azure 门户“市场”屏幕的屏幕截图显示了搜索框中的“SendGr”，并在搜索结果中选择 SendGrid。][sendgrid-store]
4. 完成注册表单，并选择“创建”。

    ![该屏幕截图显示了在“创建新的 SendGrid 帐户”对话框中填写了“名称”、“密码”、“订阅”和“资源组”字段。][sendgrid-create]
5. 输入“名称”标识 Azure 设置中的 SendGrid 服务。 名称的长度必须介于 1 到 100 个字符之间，并只能包含字母字符、短划线、句点和下划线。 名称在订阅的 Azure 应用商店项目的列表中必须是唯一的。
6. 输入并确认“密码”。
7. 选择“订阅”。
8. 新建“资源组”或使用现有资源组。
9. 在“定价层”对话框中，选择要注册的 SendGrid 计划。

    ![该屏幕截图显示了“创建新的 SendGrid 帐户”对话框中打开了“选择定价层”部分][sendgrid-pricing]
10. 如果有“促销代码”，请输入。
11. 输入“联系信息”。
12. 查看并接受“法律条款”。
13. 确认购买后，将出现“部署成功”弹出窗口，并将看到列出的帐户。

    ![“SendGrid 帐户”页屏幕截图显示了列出的新帐户 ContosoSendGrid。][all-resources]

    完成购买并单击“管理”按钮启动电子邮件验证过程后，用户将收到一封来自 SendGrid 的电子邮件，要求验证其帐户。 如果没有收到此电子邮件，或验证帐户时出错，请参阅此常见问题解答。

    ![该屏幕截图显示了“ContosoSendGrid 帐户”页上突出显示“管理”按钮。][manage]

    **验证帐户之前，用户每天最多只能发送 100 封电子邮件。**

    若要修改订阅计划或查看 SendGrid 联系人设置，请单击 SendGrid 服务的名称以打开 SendGrid Marketplace 仪表板。

    ![该屏幕截图显示了通过选择 ContosoSendGrid 帐户页面的“所有设置”打开“设置”页面。][settings]

    若要使用 SendGrid 发送电子邮件，必须提供 API 密钥。

### <a name="to-find-your-sendgrid-api-key"></a>查找 SendGrid API 密钥
1. 单击“管理”。 

    ![该屏幕截图显示了“ContosoSendGrid 帐户”页上突出显示“管理”按钮。][manage]
2. 在 SendGrid 仪表板中，选择“设置”，并选择左侧菜单中的“API 密钥”。

    ![该屏幕截图显示了 SendGrid 仪表板的“设置”下拉列表已打开并在其中选择了“API 密钥”。][api-keys]

3. 单击“创建 API 密钥”。

    ![该屏幕截图显示了在 API 密钥屏幕中选择了“创建 API 密钥”按钮。][general-api-key]
4. 至少提供“此密钥名称”和对“邮件发送”的完全访问权限，并选择“保存”。

    ![该屏幕截图显示了“添加新的常规 API密钥”屏幕上，“邮件发送”设为“完全访问”、“计划发送”设为“无访问权限”和高亮显示“保存”按钮。][access]
5. 此时会显示一次 API。 请务必安全存储该 API。

### <a name="to-find-your-sendgrid-credentials"></a>查找 SendGrid 凭据
1. 单击密钥图标，查找“用户名”。

    ![该屏幕截图显示了“ContosoSendGrid 帐户”页上突出显示“密钥”按钮。][key]
2. 密码是用户设置时选择的密码。 若需进行任何更改，可选择“更改密码”或“重置密码”。

若要管理电子邮件传递设置，请单击“管理”按钮。 此操作将重定向到 SendGrid 仪表板。

![该屏幕截图显示了“ContosoSendGrid 帐户”页上突出显示“管理”按钮。][manage]

有关通过 SendGrid 发送电子邮件的详细信息，请访问[电子邮件 API 概述][Email API Overview]。

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
