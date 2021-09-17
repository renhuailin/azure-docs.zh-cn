---
title: 通过 Azure 门户中的专用访问连接到 Azure Database for PostgreSQL 灵活服务器
description: 本文演示如何使用 Azure 门户，通过专用访问或虚拟网络创建并连接到 Azure Database for PostgreSQL 灵活服务器。
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 07/29/2021
ms.openlocfilehash: 772cd5c647c3e03773bd6c3e7f57616093b4c7ce
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694142"
---
# <a name="connect-azure-database-for-postgresql-flexible-server-with-the-private-access-connectivity-method"></a>使用专用访问连接方法连接 Azure Database for PostgreSQL 灵活服务器

Azure Database for PostgreSQL 灵活服务器是一种托管服务，可用于在云中运行、管理和缩放具有高可用性的 PostgreSQL 服务器。 本快速入门介绍如何使用 Azure 门户在虚拟网络中创建灵活服务器。

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器当前以公共预览版提供。

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

转到 [Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。

## <a name="create-an-azure-database-for-postgresql-flexible-server"></a>创建 Azure Database for PostgreSQL 灵活服务器

使用一组定义的[计算和存储资源](./concepts-compute-storage.md)创建灵活服务器。 请在 [Azure 资源组](../../azure-resource-manager/management/overview.md)中创建该服务器。

如需创建灵活服务器，请完成以下步骤：

1. 在门户中搜索并选择“Azure Database for PostgreSQL 服务器”：

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png" alt-text="显示对 Azure Database for PostgreSQL 服务器进行搜索的屏幕截图。" lightbox="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png":::

2. 选择 **添加** 。

3. 在“选择 Azure Database for PostgreSQL 部署选项”页面上，选择“灵活服务器”作为部署选项 ：

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-option.png" alt-text="显示“灵活服务器”选项的屏幕截图。" lightbox="./media/quickstart-create-connect-server-vnet/deployment-option.png":::

4. 在“基本信息”选项卡上，输入“订阅”、“资源组”、“区域”和“服务器名称”    。  使用默认值时，这会使用 2 个 vCore、8 GiB RAM 和 28 GiB 存储预配具有常规用途定价层的 12 版 PostgreSQL 服务器。 备份保留期为七天。 可以使用“开发”工作负载默认为较低成本定价层。

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/postgres-create-basics.png" alt-text="显示 postgres 灵活服务器页的“基本信息”选项卡的屏幕截图。" lightbox="./media/quickstart-create-connect-server-vnet/postgres-create-basics.png":::

5. 在“基本信息”选项卡，输入唯一的管理员用户名和管理员密码  。

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/db-administrator-account.png" alt-text="显示管理员用户信息页的屏幕截图。" lightbox="./media/quickstart-create-connect-server-vnet/db-administrator-account.png":::

6.  转到“网络”选项卡，然后选择“专用访问” 。 创建服务器后，不能更改连接方法。 选择“创建虚拟网络”，创建新的虚拟网络 vnetenvironment1 。 提供虚拟网络名称和子网信息后，选择“确定”。

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-postgres-server.png" alt-text="显示新 VNET 的“网络”选项卡的屏幕截图。" lightbox="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-postgres-server.png":::

7. 选择“查看 + 创建”，查看你的灵活服务器配置。

8. 选择“创建”以预配服务器。 预配可能需要几分钟时间。

9. 等待部署完成并成功。

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-success.png" alt-text="显示新 VNET 的“网络”设置的屏幕截图。" lightbox="./media/quickstart-create-connect-server-vnet/deployment-success.png":::

9.  选择“转到资源”，查看打开的服务器的“概述”页面 。

## <a name="create-an-azure-linux-virtual-machine"></a>创建一个 Azure Linux 虚拟机

由于服务器位于虚拟网络中，因此只能从与服务器位于同一虚拟网络中的其他 Azure 服务连接到服务器。 若要连接和管理服务器，请创建一个 Linux 虚拟机。 虚拟机必须在相同的区域和相同的订阅中创建 。 Linux 虚拟机可充当用于管理数据库服务器的 SSH 隧道。 

1. 转到创建服务器所在的资源组。 选择 **添加** 。
2. 选择“Ubuntu Server 18.04 LTS”。
3. 在“基本信息”标签页中的“项目详细信息”下，确保选择了正确的订阅，然后选择“新建资源组”。 对于名称，请键入 *myResourceGroup*。

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/project-details.png" alt-text="“项目详细信息”部分的屏幕截图，显示为虚拟机选择 Azure 订阅和资源组的位置。" lightbox="../../virtual-machines/linux/media/quick-create-portal/project-details.png"::: 

2. 在“实例详细信息”下，为“虚拟机名称”键入“myVM”，然后选择与数据库服务器相同的“区域” 。

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/instance-details.png" alt-text="“实例详细信息”部分的屏幕截图，可在其中提供虚拟机的名称并选择其区域、映像和大小。" lightbox="../../virtual-machines/linux/media/quick-create-portal/instance-details.png":::

3. 在“管理员帐户”下，选择“SSH 公钥” 。

4. 在“用户名”处键入 azureuser。

5. 对于“SSH 公钥源”，请保留默认的“生成新密钥对”，然后为“密钥对名称”键入 myKey 。

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png" alt-text="管理员帐户部分的屏幕截图，可以在其中选择身份验证类型并提供管理员凭据。" lightbox="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png":::

6. 在“入站端口规则” > “公共入站端口”下，选择“允许所选端口”，然后从下拉列表中选择“SSH (22)”和“HTTP (80)”。

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png" alt-text="“入站端口规则”部分的屏幕截图，可在其中选择允许建立入站连接的端口。" lightbox="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png":::

7. 选择“网络”页面以配置虚拟网络。 对于虚拟网络，选择为数据库服务器创建的 vnetenvironment1。

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png" alt-text="选择数据库服务器的现有虚拟网络的屏幕截图。" lightbox="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png":::

8. 选择“管理子网配置”，为服务器创建新子网。

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png" alt-text="管理子网的屏幕截图。" lightbox="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png":::

9. 为虚拟机添加新的子网。

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png" alt-text="为虚拟机添加新子网的屏幕截图。" lightbox="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png"::: 

10. 子网创建成功后，请关闭页面。

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/subnet-create-success.png" alt-text="为虚拟机添加新子网的成功屏幕截图。" lightbox="./media/quickstart-create-connect-server-vnet/subnet-create-success.png":::

11. 选择“查看 + 创建”  。
12. 选择“创建”。 “生成新密钥对”窗口打开时，选择“下载私钥并创建资源” 。 下载的密钥文件名为 myKey.pem。

    >[!IMPORTANT]
    > 确保知道 `.pem` 文件的下载位置。 在下一步中需要使用其路径。

13. 部署完成后，选择“转到资源”，查看虚拟机“概述”页 。

14. 选择公共 IP 地址并将其复制到剪贴板。

    :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/ip-address.png" alt-text="显示如何复制虚拟机的 IP 地址的屏幕截图。" lightbox="../../virtual-machines/linux/media/quick-create-portal/ip-address.png":::

## <a name="install-postgresql-client-tools"></a>安装 PostgreSQL 客户端工具

使用 Bash 或 PowerShell 创建与 VM 的 SSH 连接。 根据提示符，打开与虚拟机的 SSH 连接。 将 IP 地址替换为 VM 中的 IP 地址，并将 `.pem` 的路径替换为密钥文件的下载路径。

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> 下次在 Azure 中创建 VM 时，可以使用此次创建的 SSH 密钥。 下次创建 VM 时，只需为“SSH 公钥源”选择“使用存储在 Azure 中的密钥” 。 你的计算机上已有私钥，因此无需下载任何内容。

需要安装 postgresql-client 工具才能连接到服务器。

```bash
sudo apt-getupdate
sudo apt-get install postgresql-client
```

由于通过 SSL 强制执行与数据库的连接，因此需要下载公共 SSL 证书。

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
```

## <a name="connect-to-the-server-from-azure-linux-virtual-machine"></a>从 Azure Linux 虚拟机连接到服务器
安装 psql 客户端工具后，即可从本地环境连接到服务器。

```bash
psql --host=mydemoserver-pg.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres --set=sslmode=require --set=sslrootcert=DigiCertGlobalRootCA.crt.pem
```

## <a name="clean-up-resources"></a>清理资源
现在已在资源组中创建 Azure Database for PostgreSQL 灵活服务器。 如果将来不再需要这些资源，可以通过删除资源组来删除它们，也可以只删除 PostgreSQL 服务器。 若要删除资源组，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“资源组”。
1. 在资源组列表中，选择资源组的名称。
1. 在资源组的“概述”页中，选择“删除资源组” 。
1. 在确认对话框中，键入资源组的名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [连接 Python 应用](connect-python.md)
