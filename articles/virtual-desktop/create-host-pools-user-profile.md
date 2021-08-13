---
title: Azure 虚拟桌面 FSLogix 配置文件容器共享 - Azure
description: 如何使用基于虚拟机的文件共享为 Azure 虚拟桌面主机池设置 FSLogix 配置文件容器。
author: Heidilohr
ms.topic: how-to
ms.date: 08/20/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: a1398c23adcadaf245bae7271ed91e7d2a6763da
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756088"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>使用文件共享为主机池创建配置文件容器

Azure 虚拟桌面服务提供 FSLogix 配置文件容器作为推荐的用户配置文件解决方案。 不建议使用用户配置文件磁盘 (UPD) 解决方案，未来版本的 Azure 虚拟桌面将弃用该解决方案。

本文将介绍如何使用基于虚拟机的文件共享为主机池设置 FSLogix 配置文件容器共享。 强烈建议使用 Azure 文件存储，不要使用文件共享。 有关更多 FSLogix 文档，请参阅 [FSLogix 站点](https://docs.fslogix.com/)。

>[!NOTE]
>如果正在查找有关 Azure 上不同 FSLogix 配置文件容器存储选项的比较资料，请参阅 [FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>创建将用作文件共享的新虚拟机

创建虚拟机时，请确保将该虚拟机放置在与主机池虚拟机相同的虚拟网络中，或者放置到已连接主机池虚拟机的虚拟网络中。 可以通过多种方式创建虚拟机：

- [从 Azure Gallery 映像创建虚拟机](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [从托管映像创建虚拟机](../virtual-machines/windows/create-vm-generalized-managed.md)
- [从非托管映像创建虚拟机](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-from-user-image)

创建虚拟机后，通过执行以下操作将其加入域：

1. 用创建虚拟机时提供的凭据[连接到虚拟机](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 在虚拟机上，启动“控制面板”并选择“系统”。 
3. 依次选择“计算机名称”、“更改设置”和“更改…”  
4. 选择“域”，然后输入虚拟网络上的 Active Directory 域。
5. 使用对于加入域的计算机拥有特权的域帐户进行身份验证。

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>准备虚拟机以充当用户配置文件的文件共享

下面是有关如何准备虚拟机以充当用户配置文件的文件共享的常规说明：

1. 将 Azure 虚拟桌面 Active Directory 用户添加到 [Active Directory 域服务安全组](/windows/security/identity-protection/access-control/active-directory-security-groups/)。 此安全组将用于向你刚刚创建的文件共享虚拟机验证 Azure 虚拟桌面用户的身份。
2. [连接到文件共享虚拟机](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
3. 在文件共享虚拟机上，在将用作配置文件共享的“C 驱动器”上创建一个文件夹。
4. 右键单击新文件夹，选择“属性”，选择“共享”，然后选择“高级共享...”。
5. 选择“共享此文件夹”，选择“权限...”，然后选择“添加...”。
6. 搜索向其中添加了 Azure 虚拟桌面用户的安全组，并确保该组具有“完全控制”权限。
7. 添加安全组后，右键单击该文件夹，选择“属性”，选择“共享”，然后复制要稍后使用的“网络路径”。

有关权限的详细信息，请参阅 [FSLogix 文档](/fslogix/fslogix-storage-config-ht/)。

## <a name="configure-the-fslogix-profile-container"></a>配置 FSLogix 配置文件容器

若要配置具有 FSLogix 软件的虚拟机，请在注册到主机池的每个计算机上执行以下操作：

1. 用创建虚拟机时提供的凭据[连接到虚拟机](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine)。
2. 启动 Internet 浏览器并导航到[此链接](https://go.microsoft.com/fwlink/?linkid=2084562)，下载 FSLogix 代理。
3. 在 .zip 文件中导航到 \\\\Win32\\ 版本或 \\\\X64\\ 版本，并运行“FSLogixAppsSetup”以安装 FSLogix 代理。  若要了解有关如何安装 FSLogix 的详细信息，请参阅 [下载并安装 FSLogix](/fslogix/install-ht/)。
4. 导航到“Program Files > FSLogix > Apps”以确认已安装代理。
5. 从开始菜单中，以管理员身份运行“RegEdit”。 导航到“计算机\\ HKEY_LOCAL_MACHINE\\software\\FSLogix”。
6. 创建名为“配置文件”的密钥。
7. 为配置文件密钥创建以下值：

| 名称                | 类型               | 数据/值                        |
|---------------------|--------------------|-----------------------------------|
| 已启用             | DWORD              | 1                                 |
| VHDLocations        | 多字符串值 | “文件共享的网络路径”     |

>[!IMPORTANT]
>为了帮助保护 Azure 中的 Azure 虚拟桌面环境，建议不要在 VM 上打开入站端口 3389。 Azure 虚拟桌面不需要打开入站端口 3389，就能让用户访问主机池的 VM。 如果必须打开端口 3389 以进行故障排除，我们建议你使用[实时 VM 访问](../security-center/security-center-just-in-time.md)。
