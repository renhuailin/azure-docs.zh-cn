---
author: v-dalc
ms.service: databox
ms.subservice: databox
ms.topic: include
ms.date: 09/08/2021
ms.author: alkohli
ms.custom: contperf-fy22q1
ms.openlocfilehash: 6f784c075d24fe26b7ec1ea9d5921dfb460e5298
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743324"
---
要订购设备，请在 Azure 门户中执行以下步骤：

1. 使用 Microsoft Azure 凭据在以下 URL 登录：[https://portal.azure.com](https://portal.azure.com)。
2. 选择“+ 创建资源”并搜索 *Azure Data Box*。 选择“Azure Data Box”。

   ![“新建”部分的屏幕截图，其中搜索字段中有“Azure Data Box”字样](media/data-box-order-portal/data-box-import-01.png)

3. 选择“创建”。  

   ![Azure Data Box 部分的屏幕截图，其中已标注“创建”选项](media/data-box-order-portal/data-box-import-02.png)

4. 检查你所在区域是否可使用 Data Box 服务。 输入或选择以下信息，然后选择“应用”。

    |设置  |值  |
    |---------|---------|
    |传输类型     | 选择“导入到 Azure”。        |
    |订阅     | 对于 Data Box 服务，选择一个 EA、CSP 或 Azure 赞助订阅。 <br> 该订阅将链接到计费帐户。       |
    |资源组 | 选择现有资源组。 资源组是可以统一管理或部署的资源的逻辑容器。 |
    |源国家/地区    |    选择数据当前所在的国家/地区。         |
    |目标 Azure 区域     |     选择要在其中传输数据的 Azure 区域。 <br> 有关详细信息，请参阅 [Data Box 的上市区域](../articles/databox/data-box-overview.md#region-availability)或 [Data Box Heavy 的上市区域](../articles/databox/data-box-heavy-overview.md#region-availability)。  |

    [ ![启动 Azure Data Box 导入订单](media/data-box-order-portal/data-box-import-03.png) ](media/data-box-order-portal/data-box-import-03.png#lightbox)

5. 选择要订购的 Data Box 产品，是 Data Box（如下所示）还是 Data Box Heavy。 

    [ ![屏幕截图显示用于选择 Azure Data Box 产品的屏幕。突出显示了 Data Box 的“选择”按钮](media/data-box-order-portal/data-box-import-04.png) ](media/data-box-order-portal/data-box-import-04.png#lightbox)

    对于 Data Box，单次最多可订购的容量是 80 TB。 对于 Data Box Heavy，单次最多可订购的容量是 770 TB。 可以创建多个订单，以增加数据大小。

    如果选择“Data Box Heavy”，则 Data Box 团队会检查你的区域中的设备可用性。 他们将通知你何时可以继续处理你的订单。

6. 在“订单”中，转到“基本信息”选项卡，输入或选择以下信息 。 然后，选择“下一步: 数据目标>”。

    |设置  |值  |
    |---------|---------|
    |订阅      | 系统会根据前面所做的选择自动填充此订阅。|
    |资源组    | 之前选择的资源组。 |
    |导入订单名称 | 提供友好名称用于跟踪订单。 <ul><li>名称可包含 3 到 24 个字符，可以是字母、数字或连字符。</li><li>名称必须以字母或数字开头和结尾。</li></ul>    |

    ![显示 Data Box 订单的“基本信息”屏幕的屏幕截图，其中显示了示例条目。 突出显示了“基本信息”选项卡和“下一步: 数据目标>”按钮。](media/data-box-order-portal/data-box-import-05.png)

7. 在“数据目标”屏幕上，选择“数据目标”（存储帐户或托管磁盘） 。

    如果使用“存储帐户”作为存储目标，则会看到以下屏幕：

    ![Data Box 订单的“数据目标”屏幕的屏幕截图，其中显示了存储帐户目标。 突出显示了“数据目标”选项卡、“存储帐户”和“下一步: 安全性>”按钮。](media/data-box-order-portal/data-box-import-06.png)

    根据指定的 Azure 区域，从现有存储帐户的筛选列表中选择一个或多个存储帐户。 Data Box 可以与最多 10 个存储帐户链接。 也可以创建新的 **常规用途 v1**、**常规用途 v2** 或 **Blob 存储帐户**。

   > [!NOTE]
   > - 如果选择 Azure Premium FileStorage 帐户，则存储帐户共享上的预配配额将增加到要复制到文件共享的数据的大小。 配额增加后，就不会再进行调整，例如，如果出于某种原因 Data Box 无法复制你的数据。
   > - 此配额用于计费。 将数据上传到数据中心后，应调整配额以满足需求。 有关详细信息，请参阅[了解计费](../articles/storage/files/understanding-billing.md)。

    支持使用虚拟网络的存储帐户。 若要允许 Data Box 服务使用受保护的存储帐户，请在存储帐户网络防火墙设置中启用受信任的服务。 有关详细信息，请了解如何[将 Azure Data Box 添加为受信任的服务](../articles/storage/common/storage-network-security.md#exceptions)。

    如果使用 Data Box 从本地虚拟硬盘 (VHD) 创建“托管磁盘”，则还需提供以下信息：

    |设置  |值  |
    |---------|---------|
    |资源组     | 若要从本地 VHD 创建托管磁盘，请创建新的资源组。 使用现有资源组的前提是，资源组是在以前由 Data Box 服务为托管磁盘创建 Data Box 订单时创建的。 <br> 指定多个用分号分隔的资源组。 最多支持 10 个资源组。|

    ![Data Box 订单的“数据目标”选项卡的屏幕截图，其中显示了托管磁盘目标。 突出显示了“数据目标”选项卡、“托管磁盘”和“下一步: 安全性>”按钮。](media/data-box-order-portal/data-box-import-07.png)

    为托管磁盘指定的存储帐户用作临时存储帐户。 Data Box 服务将 VHD 作为页 Blob 上传到临时存储帐户，然后将其转换为托管磁盘并移到资源组。 有关详细信息，请参阅[验证 Azure 中的数据上传](../articles/databox/data-box-deploy-picked-up.md#verify-data-upload-to-azure-8)。

   > [!NOTE]
   > 如果页 blob 未成功转换为托管磁盘，它将保留在存储帐户中，你需为存储付费。

8. 选择“下一步: 安全性>”继续操作。

    通过“安全性”屏幕，可使用自己的加密密钥和设备并共享密码，还可选择使用双重加密。

    “安全性”屏幕上的所有设置都是可选的。 如果不更改任何设置，则将应用默认设置。

    ![Data Box 导入订单的“安全性”选项卡屏幕截图。 突出显示了“安全性”选项卡。](media/data-box-order-portal/data-box-import-08.png)

9. 如果要使用自己的客户管理的密钥来保护新资源的解锁密钥，请展开“加密类型”。

    可选择性地为 Azure Data Box 配置客户管理的密钥。 默认情况下，Data Box 使用 Microsoft 管理的密钥来保护解锁密钥。

    客户管理的密钥不影响设备上数据的加密方式。 该密钥仅用于加密设备解锁密钥。

    如果不想使用客户管理的密钥，请跳到步骤 15。

   ![Data Box 订单向导中的“安全性”选项卡屏幕截图。 “加密类型”设置已展开并突出显示。](./media/data-box-order-portal/customer-managed-key-01.png)

10. 若要使用客户管理的密钥，请选择“客户管理的密钥”作为密钥类型。 然后，选中“选择密钥保管库和密钥”。
   
    ![Data Box 订单的“安全性”选项卡上“加密类型”设置的屏幕截图。 突出显示了“选择密钥保管库和密钥”链接。](./media/data-box-order-portal/customer-managed-key-02.png)

11. 在“从 Azure Key Vault 中选择密钥”边栏选项卡中：

    - 会自动填充“订阅”字段。

    - 对于“密钥保管库”，可以从下拉列表中选择现有的密钥保管库。

      ![Data Box 订单的“安全性”选项卡上“加密类型”设置的屏幕截图。 选中了“客户管理的密钥”选项和“选择密钥保管库和密钥”链接。](./media/data-box-order-portal/customer-managed-key-03.png)

      如果要创建新的密钥保管库，请选择“新建密钥保管库”。 
    
      ![Data Box 订单的“安全性”选项卡上“加密类型”设置的屏幕截图。 突出显示了“新建密钥保管库”链接。](./media/data-box-order-portal/customer-managed-key-04.png)      

      然后，在“创建密钥保管库”屏幕上，输入资源组和密钥保管库名称。 确保已启用软删除和清除保护 。 接受其他所有默认值，然后选择“查看 + 创建”。

      ![Data Box 订单的“创建密钥保管库”屏幕的屏幕截图。 突出显示了资源组和密钥保管库名称。 “软删除”和“清除保护”已启用。](./media/data-box-order-portal/customer-managed-key-05.png)

      查看密钥保管库的信息，然后选择“创建”。 等待几分钟，直到密钥保管库创建完成。

      ![Azure 的创建密钥保管库向导的“查看 + 创建”选项卡的屏幕截图。 其中突出显示了“创建”按钮。](./media/data-box-order-portal/customer-managed-key-06.png)

12. “选择密钥”边栏选项卡将显示所选的密钥保管库。

    ![Azure Key Vault 中“选择密钥”屏幕的屏幕截图。 突出显示了“密钥保管库”字段。](./media/data-box-order-portal/customer-managed-key-07.png)

    如果要创建新密钥，请选择“创建新密钥”。 必须使用 RSA 密钥。 大小可以是 2048 或更大。 输入新密钥的名称，接受其他默认值，然后选择“创建”。

      ![Azure Key Vault 中“创建密钥”屏幕的屏幕截图，其中输入了密钥名称。 突出显示了“名称”字段和“创建”按钮。](./media/data-box-order-portal/customer-managed-key-08.png)

      在密钥保管库中创建密钥后，你将收到通知。 新密钥将被选中并显示在“选择密钥”边栏选项卡上。

13. 选择要使用的密钥的版本，然后选中“选择” 。

      ![Azure Key Vault 中“创建密钥”的屏幕截图。 突出显示了“版本”字段，还显示了可用版本。](./media/data-box-order-portal/customer-managed-key-09.png)

    如果要创建新的密钥版本，请选择“创建新版本”。

    ![Azure Key Vault 中“创建密钥”的屏幕截图。 突出显示了“创建新版本”链接。](./media/data-box-order-portal/customer-managed-key-10.png)

    选择新密钥版本的设置，然后选择“创建”。

    ![Azure Key Vault 中“创建密钥”对话框的屏幕截图，其中显示了示例字段设置。 其中突出显示了“创建”按钮。](./media/data-box-order-portal/customer-managed-key-11.png)

    “安全性”屏幕上的“加密类型”设置将显示密钥保管库和密钥 。

    ![Data Box 导入订单的“安全性”选项卡屏幕截图。 密钥保管库和密钥在“加密类型”设置中突出显示。](./media/data-box-order-portal/customer-managed-key-12.png)

14. 选择将用于管理对此资源的访问权限的用户标识。 选中“选择用户标识”。 在右侧面板中，选择要使用的订阅和托管标识。 然后选取“选择”  。

    用户分配的托管标识是一个可用于管理多个资源的独立 Azure 资源。 有关详细信息，请参阅[托管标识类型](../articles/active-directory/managed-identities-azure-resources/overview.md)。  

    如果需要创建新的托管标识，请按照[使用 Azure 门户创建、列出和删除用户分配的托管标识以及如何为其分配角色](../articles/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)中的指南操作。
    
    ![Data Box 订单的“安全性”选项卡屏幕截图，其中显示了“选择用户分配的托管标识”面板。 突出显示了“订阅”和“所选标识”字段。](./media/data-box-order-portal/customer-managed-key-13.png)

    用户标识显示在“加密类型”设置中。

    ![Data Box 导入订单的“安全性”选项卡屏幕截图。 所选用户标识在“加密类型”设置中突出显示。](./media/data-box-order-portal/customer-managed-key-14.png)

15. 如果不想采用 Azure Data Box 默认使用的系统生成的密码，请在“安全性”屏幕上展开“创建自己的密码” 。

    系统生成的密码很安全，建议使用该密码，除非贵组织另有要求。

    ![Data Box 订单的“安全性”选项卡屏幕截图，其中“自带密码”已展开。 突出显示了“安全性”选项卡和密码选项。](media/data-box-order-portal/bring-your-own-password-01.png) 

   - 要对新设备使用自己的密码，请在“设置设备密码的首选项”中选择“使用自己的密码”，然后键入符合安全要求的密码 。
     
     密码必须是字母和数字，且包含 12 到 15 个字符，至少有一个大写字母、一个小写字母、一个特殊字符和一个数字。

     - 允许使用的特殊字符：@ # - $ % ^ ! + = ; : _ ( )
     - 不允许使用的字符：I i L o O 0
   
     ![Data Box 订单的“安全性”选项卡屏幕截图，其中显示了“自带密码”选项。 突出显示了“使用自己的密码”选项和“设备密码”选项。](media/data-box-order-portal/bring-your-own-password-02.png)

 - 使用自己的共享密码：

   1. 在“设置共享密码的首选项”中，依次选择“使用自己的密码”和“选择共享密码”  。
     
       ![Data Box 订单的“安全性”选项卡屏幕截图，其中显示了用于使用自己的共享密码的选项。 突出显示了两个选项：“使用自己的密码”和“选择共享密码”。](media/data-box-order-portal/bring-your-own-password-03.png)

    1. 为订单中的每个存储帐户键入一个密码。 该密码将用于存储帐户的所有共享。
    
       密码必须是字母和数字，且包含 12 到 64 个字符，至少有一个大写字母、一个小写字母、一个特殊字符和一个数字。

       - 允许使用的特殊字符：@ # - $ % ^ ! + = ; : _ ( )
       - 不允许使用的字符：I i L o O 0
     
    1. 要对所有存储帐户使用同一密码，请选择“复制到全部”。 

    1. 完成后，选择“保存”。
     
       ![Data Box 订单的“设置共享密码”屏幕的屏幕截图。 突出显示了“复制到全部”链接和“保存”按钮。](media/data-box-order-portal/bring-your-own-password-04.png)

    在“安全性”屏幕上，可以使用“查看或更改密码”来更改密码 。

16. 在“安全性”中，如果要启用基于软件的双重加密，请展开“双重加密(适用于高度安全的环境)”，然后选择“为订单启用双重加密”  。

    ![Data Box 订单的“安全性”选项卡屏幕截图，其中显示了“双重加密”选项。 突出显示了“为订单启用双重加密”选项和“下一步: 联系人详细信息>”按钮。](media/data-box-order-portal/double-encryption-01.png)

    除了对 Data Box 上的数据进行 AES-256 位加密，还可执行基于软件的加密。

    > [!NOTE]
    > 启用此选项可能会导致订单处理和数据复制耗时较长。 创建订单后，不能更改此选项。

    选择“下一步: 联系人详细信息>”来继续操作。

17. 在“联系人详细信息”中，选择“+ 添加地址” 。

    ![Data Box 订单的“联系人详细信息”选项卡屏幕截图。 突出显示了“联系人详细信息”选项卡和“+ 添加地址”选项。](media/data-box-order-portal/contact-details-01.png)

18. 在“添加地址”屏幕中，提供你的姓名、公司的名称和邮政地址，以及有效的电话号码。 选择“验证地址”。 服务会验证该地址是否可使用该服务，并将能否使用的结果通知你。

    ![Data Box 订单的“添加地址”屏幕的屏幕截图。 标注了“寄送方式”选项和“添加寄送地址”选项。](media/data-box-order-portal/contact-details-02.png)

    如果你选择了自我托管交付，则在成功下单后，你将收到一封电子邮件通知。 有关自托管寄送的详细信息，请参阅[使用自托管寄送](../articles/databox/data-box-portal-customer-managed-shipping.md)。

19. 成功验证送货详细信息后，选择“**添加送货地址**”。 你将返回“联系人详细信息”选项卡。

20. 在“电子邮件”旁边，添加一个或多个电子邮件地址。 服务会将有关任何订单状态更新的电子邮件通知发送到指定的电子邮件地址。

    我们建议使用组电子邮件，以便在组中的管理员离任后，可以持续收到通知。

    ![Data Box 订单的“联系人详细信息”选项卡中“电子邮件”部分的屏幕截图。 突出显示了用于键入电子邮件地址的区域和“查看 + 订购”按钮。](media/data-box-order-portal/contact-details-03.png)

21. 查看“查看 + 订购”中与订单、联系人、通知和隐私条款相关的信息。 选中对应于同意隐私条款的复选框。

22. 选择“订单”。 创建订单需要几分钟时间。

    ![Data Box 订单的“查看 + 订购”选项卡屏幕截图。 突出显示了“查看 + 订购”选项卡和“订购”按钮。](media/data-box-order-portal/data-box-import-09.png)