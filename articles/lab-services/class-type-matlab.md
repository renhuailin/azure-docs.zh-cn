---
title: 使用 Azure 实验室服务设置实验室以教授 MATLAB | Microsoft Docs
description: 了解如何使用 Azure 实验室服务设置实验室以教授 MATLAB。
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "85444992"
---
# <a name="setup-a-lab-to-teach-matlab"></a>设置实验室以教授 MATLAB

[MATLAB](https://www.mathworks.com/products/matlab.html)（代表矩阵实验室）是 [MathWorks](https://www.mathworks.com/) 的编程平台。  它结合了计算能力和可视化效果，是数学、工程、物理和化学领域的常用工具。

如果使用的是[校园范围许可证](https://www.mathworks.com/academia/tah-support-program/administrators.html)，请参阅[下载 MATLAB 安装文件](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine)处的说明，以在模板计算机上下载 MATLAB 安装程序文件。  

在本文中，我们会演示如何设置将 MATLAB 客户端软件与许可证服务器一起使用的课程。

## <a name="license-server"></a>许可证服务器

为实验室修改模板计算机之前，需要将服务器设置为运行[网络许可证管理器](https://www.mathworks.com/help/install/administer-network-licenses.html)软件。  这些说明仅适用于对 MATLAB 选择网络许可选项（这允许用户共享许可证密钥池）的机构。  还需要保存许可证文件和文件安装密钥供以后使用。  有关如何下载许可证文件的详细说明，请参阅[在具有 Internet 连接的情况下安装网络许可证管理器](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html)一文的第一步。

[在具有 Internet 连接的情况下安装网络许可证管理器](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html)中提供了介绍如何安装许可服务器的详细说明。  若要启用借用，请参阅[借用许可证](https://www.mathworks.com/help/install/license/borrow-licenses.html)一文。

假设许可证服务器位于本地网络或 Azure 中的专用网络上，请不要忘记[将虚拟网络对等互连](how-to-connect-peer-virtual-network.md)到[实验室帐户](tutorial-setup-lab-account.md)。  必须先完成网络对等互连后再创建实验室，以便实验室虚拟机可以访问许可证服务器。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，你需要具有一个 Azure 订阅才能开始。  如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 在获得 Azure 订阅后，可以在 Azure 实验室服务中创建新实验室帐户，或者也可以使用现有的帐户。  若要创建新实验室帐户，请参阅[设置实验室帐户教程](tutorial-setup-lab-account.md)。

若要创建新实验室，请按照[设置课堂实验室教程](tutorial-setup-classroom-lab.md)操作。  应用以下设置：

| 虚拟机大小 | 映像 |
| -------------------- | ----- |
| 中型 | Windows 10 |

有更多操作系统支持 MATLAB。  有关详细信息，请参阅 [MATLAB 系统要求](https://www.mathworks.com/support/requirements/matlab-system-requirements.html)。

> [!WARNING]
> 在创建实验室之前，不要忘记将实验室帐户的[虚拟网络与许可证服务器的虚拟网络进行对等互连](https://www.mathworks.com/support/requirements/matlab-system-requirements.html)。

## <a name="template-machine"></a>模板计算机

在创建模板计算机后，请启动并连接到该计算机，以完成以下主要任务。

1. 下载 MATLAB 客户端软件的安装文件。
2. 使用文件安装密钥安装 MATLAB。

安装 MATLAB 是包含多个部分的过程。  第一部分会下载 MATLAB 以及要安装的任何其他产品的文件。  使用文件安装密钥要求预下载要安装的产品的所有安装文件。  第二部分会在模板 VM 上安装 MATLAB 软件并激活软件。  如果模板 VM 配置为使用许可证服务器激活，则学生 VM 会执行相同操作。

### <a name="download-installation-files"></a>下载安装文件

你必须是许可证管理员才能下载安装文件以及获取许可证文件和文件安装密钥。  下面是下载安装文件的步骤。

1. 对 [https://www.mathworks.com](https://www.mathworks.com) 登录帐户。
2. 选择“我的帐户”。
3. 在帐户页的“我的软件”部分下，单击附加到为实验室设置的网络许可证管理器的许可证。
4. 在许可证详细信息页上，单击“下载产品”。
5. 等待安装程序自行提取。
6. 启动安装程序。  
7. 在“登录 MathWorks 帐户”页上，输入你的 MathWorks 帐户。
8. 在“MathWorks 许可协议”页上，接受条款，然后单击“下一步”按钮 。
9. 单击“高级选项”下拉列表，然后选择“我要下载但不安装” 。
10. 在“选择目标文件夹”上，单击“下一步”。 
11. 选择“Windows”作为要安装 MATLAB 的计算机的平台。
12. 在“选择产品”页上，确保选择 MATLAB 以及要安装的任何其他 MathWorks 产品。
13. 在“确认选择并下载”页上，单击“开始下载” 。  
14. 等待下载所选产品。  单击“完成”。

还可以从 MathWorks 网站下载 ISO 映像。

1. 对 [https://www.mathworks.com](https://www.mathworks.com) 登录帐户。
2. 转到 [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads)。
3. 选择要安装的 MATLAB 版本。
4. 单击相关链接下的“获取 {version}.iso 映像”链接，其中 {version} 是类似于 R2020a 的内容。
5. 单击对应于 Windows 的蓝色下载版本链接。

### <a name="run-installer"></a>运行安装程序

下载文件后，第二步是运行安装程序。 同样，你必须是许可证管理员才能完成此步骤。  只有许可证管理员才能使用文件安装密钥安装 MATLAB。

1. 检查下载的许可证文件，并验证 SERVER 行是否正确列出了许可证服务器。  有关应如何设置许可证文件格式的信息，请参阅[更新网络许可证](https://www.mathworks.com/help/install/ug/network-license-files.html)、[许可证借用](https://www.mathworks.com/help/install/license/borrow-licenses.html)和[查找主机 ID](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license) 这些文章。
2. 启动 MATLAB 安装程序。
3. 在“登录 MathWorks 帐户”页上，输入你的 MathWorks 帐户。
4. 在“MathWorks 许可协议”页上，接受条款，然后单击“下一步”按钮 。
5. 单击“高级选项”下拉列表，然后选择“我有文件安装密钥” 。
6. 在“使用文件安装密钥进行安装”页上，输入许可证服务器的文件安装密钥。   单击“下一步”。
7. 在“选择许可证文件”页上，导航到之前下载安装文件时保存的许可证文件。
8. 在“选择目标文件夹”页上，单击“下一步”。 
9. 在“选择产品”页上，单击“下一步”。 
10. 在“选择选项”页上，单击“下一步”。 
11. 在“确认选择并安装”页上，单击“开始安装” 。
12. 在“安装完成”页上，验证是否选中了“激活 MATLAB” 。  单击“完成”。

## <a name="cost-estimate"></a>成本估算

我们来介绍此课程可能的估算成本。  此估算中不包括运行许可证服务器的成本。  课程将提供给 25 名学生。  计划的课程时长为 20 小时。  此外，每位学生会在计划课程时间之外获得 10 小时配额，用于完成家庭作业或课外作业。  我们所选的虚拟机大小为中等，包含 55 个实验室单位。

下面是此课程可能的成本估算示例：

25 名学生 \*（计划的 20 小时 + 配额的 10 小时）\* 55 个实验室单位 \* 每小时 0.01 美元 = 412.50 美元

>[!IMPORTANT]
> 成本估算仅用于示例目的。  有关定价的最新详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。  

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室时通用的。

- [创建、管理和发布模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)
