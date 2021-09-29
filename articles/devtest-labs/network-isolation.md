---
title: 网络隔离
description: 了解 Azure 开发测试实验室中的网络隔离。
ms.topic: how-to
ms.date: 08/25/2020
ms.openlocfilehash: 7da1dbddee6d4807969cda41550972912fc69910
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661747"
---
# <a name="network-isolation-in-devtest-labs"></a>开发测试实验室中的网络隔离

[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)可起到安全边界的作用，将你的 Azure 资源与公共 Internet 隔离开来。 你还可将 Azure 虚拟网络加入本地网络，以便能够安全地连接到本地资源。 在开发测试实验室中，可选择将[所有实验室虚拟机](devtest-lab-configure-vnet.md)和[环境隔离到你的网络环境中](connect-environment-lab-virtual-network.md)，确保实验室资源遵循组织网络策略。 

作为实验室所有者，你还可选择完全隔离实验室，也就是说，除了将虚拟机和环境隔离到所选网络中外，还可隔离实验室存储帐户和订阅中创建的密钥保管库。 本文将逐步讲解如何创建网络隔离实验室。 

另请参阅以下文章：

- [开发测试实验室如何使用实验室存储帐户](encrypt-storage.md)
- [开发测试实验室如何使用密钥保管库](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> 目前只有新创建的实验室支持网络隔离。

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>在创建实验室期间启用网络隔离的步骤

1. 在创建实验室的过程中，请转到“网络”选项卡。
1. 你可以选择实验室将为你创建的默认网络，也可从下拉列表选择现有网络。 你将只能选择与实验室处于同一区域和订阅中的网络。 

    > [!div class="mx-imgBorder"]
    > ![创建实验室](./media/network-isolation/create-lab.png)
1. 选择子网。

    > [!div class="mx-imgBorder"]
    > ![创建子网](./media/network-isolation/create-lab-subnet.png)
1. 如果选择将实验室资源（实验室存储帐户和密钥保管库）隔离到默认网络中，则无需执行进一步的操作，实验室将处理后续的资源隔离事宜。
 
    > [!div class="mx-imgBorder"]
    > ![网络隔离](./media/network-isolation/isolate-lab-resources.png)
1. 如果选择将实验室资源（实验室存储帐户和密钥保管库）隔离到所选的现有网络中，则需要在创建实验室后完成以下步骤，确保实验室在隔离模式下能够继续正常工作。 
 
    > [!div class="mx-imgBorder"]
    > ![隔离资源](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > 实验室所有者需要先完成这些步骤，然后才能在实验室中配置和/或创建任何资源。

### <a name="steps-to-follow-post-lab-creation"></a>完成实验室创建后的步骤

1. 在实验室的“主页”上，选择概述页面中的“资源组” 。 你应会看到包含实验室的资源组的“资源组”页。 
 
   > [!div class="mx-imgBorder"]
   > ![Contoso 实验室](./media/network-isolation/contoso-lab.png)
1. 选择实验室的 Azure 存储帐户。 实验室存储帐户的命名约定为：a<labNameWithoutInvalidCharacters><4 位数> 。 例如，如果实验室名称为 contosolab，则存储帐户名称可以是 acontosolab1234。
 
   > [!div class="mx-imgBorder"]
   > ![Contoso - 测试](./media/network-isolation/contoso-test.png)
1. 在存储帐户上，转到“防火墙和虚拟网络”，确保选中“允许受信任的 Microsoft 服务访问此存储帐户”复选框。 由于[开发测试实验室是受信任的 Microsoft 服务](../storage/common/storage-network-security.md#trusted-microsoft-services)，因此选择此选项后实验室可在网络隔离模式下正常工作。 

   > [!div class="mx-imgBorder"]
   > ![Contoso - 实验室防火墙](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. 接下来，单击“+添加现有虚拟网络”，选择创建实验室时选取的虚拟网络和子网，然后单击“启用” 。 

   > [!div class="mx-imgBorder"]
   > ![Contoso - 我的 vnet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  成功为选定的虚拟网络启用服务终结点后，请单击“添加”。 

   > [!div class="mx-imgBorder"]
   > ![添加](./media/network-isolation/contoso-firewall-add.png)
 
这样，Azure 存储将允许来自添加的虚拟网络的入站连接，使实验室在网络隔离模式下能够成功运作。 

你还可选择自动执行这些步骤，为多个实验室配置此设置。 

[详细了解如何使用 PowerShell 和 CLI 管理 Azure 存储的默认网络访问规则](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="network-isolation-for-an-existing-lab"></a>现有实验室的网络隔离

作为实验室所有者，你可以选择隔离现有实验室的网络。 [此示例脚本](https://github.com/Azure/azure-devtestlab/blob/master/Tools/ConvertDtlLabToIsolatedNetwork/Convert-DtlLabToIsolatedNetwork.ps1)演示了如何将现有实验室和相关实验室资源转换为隔离网络模式。 

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>在网络隔离模式下使用实验室时的注意事项

### <a name="accessing-labs-storage-account-outside-the-lab"></a>从实验室外部访问实验室的存储帐户 

在网络隔离实验室中，对于将 VHD 上传到实验室的存储帐户来从中创建自定义映像这样的操作，实验室所有者需要显式启用从允许的终结点访问存储帐户。 为此，可以创建一个虚拟机，并从该虚拟机安全地访问实验室的存储帐户。 

[详细了解如何从虚拟机访问存储帐户](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>从实验室导出使用情况数据 

在网络隔离实验室中，若要[导出实验室的个人使用情况数据](personal-data-delete-export.md)，实验室所有者需要显式提供存储帐户，并在该帐户中生成一个 blob 来存储数据。 

如果未提供存储帐户，在网络隔离模式下此操作将失败，因为实验室无法访问其存储帐户，以在客户未提供存储帐户时使用。 

[详细了解如何导出指定存储帐户中的实验室使用情况数据](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>后续步骤

[使用 Azure 资源管理器模板和 PowerShell 自动创建或修改实验室](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
