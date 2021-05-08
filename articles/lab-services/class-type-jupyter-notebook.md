---
title: 设置实验室以使用 Python 和 Jupyter Notebook 讲授数据科学 | Microsoft Docs
description: 了解如何设置实验室以使用 Python 和 Jupyter Notebook 讲授数据科学。
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: d4034f889334bcf1e4eaa3710a32db60b6a9936b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94648015"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>设置实验室以使用 Python 和 Jupyter Notebook 讲授数据科学
本文介绍了如何在实验室服务中设置模板虚拟机 (VM)，使用其中的教学工具教授学生如何使用 [Jupyter Notebook](http://jupyter-notebook.readthedocs.io/)，以及学生如何在虚拟机 (VM) 上连接他们的笔记本。

Jupyter Notebooks 是一个开源项目，可让你轻松地在名为“笔记本”的单张画布上组合使用丰富的文本和可执行的 Python 源代码。 运行笔记本会生成输入和输出的线性记录。 这些输出可能包括文本、信息表、散点图等。

## <a name="set-up-the-lab"></a>设置实验室

### <a name="lab-configuration"></a>实验室配置
若要设置此实验室，需要可以访问 Azure 订阅以及有实验室帐户才能开始。 与组织的管理员讨论，了解是否可以访问现有的 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

可以访问 Azure 订阅后，请按照教程：[设置实验室帐户](tutorial-setup-lab-account.md)中的说明，在 Azure 实验室服务中新建一个实验室帐户。 也可以使用现有实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置
为实验室帐户启用下表中所述的设置。 若要详细了解如何启用市场映像，请参阅[指定可供实验室创建者使用的市场映像](specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| ------------------- | ------------ |
| 市场映像 | 在实验室帐户中，根据你的操作系统启用其中一个 Azure 市场映像： <br/><ul><li>Data Science Virtual Machine – Windows Server 2019</li><li>Data Science Virtual Machine – Ubuntu 18.04</li></ul> |

> [!NOTE]
> 本文使用的是 Azure 市场中提供的 Data Science Virtual Machine 映像，因为这些映像是通过 Jupyter Notebook 进行预配置的。 不过，这些映像还包括许多其他用于数据科学的开发和建模工具。 如果你不想要这些额外的工具，只想用Jupyter Notebook 进行简单设置，则可以创建一个自定义的 VM 映像。 例如，[在 Azure 上安装 JupyperHub](http://tljh.jupyter.org/en/latest/install/azure.html)。 创建自定义映像后，可以将其上传到共享映像库，以供在 Azure 实验室服务中使用。 了解有关[在 Azure 实验室服务中使用共享映像库](how-to-attach-detach-shared-image-gallery.md)的详细信息。 

### <a name="lab-settings"></a>实验室设置
设置课堂实验室时，按下表所示配置虚拟机大小和虚拟机映像设置。 有关创建课堂实验室的说明，请参阅[设置课堂实验室](tutorial-setup-classroom-lab.md)。

| 实验室设置 | 值/说明 |
| ------------ | ------------------ | 
| 虚拟机大小 | <p>根据运行的工作负荷选取大小：</p><ul><li>小型或中型 - 适用于访问 Jupyter Notebook 的基本设置</li><li>小型 GPU（计算）- 此大小最适用于计算密集型和网络密集型应用程序（如人工智能和深度学习）</li></ul> | 
| 虚拟机映像 | <p>根据你的操作系统选取以下其中一个映像：</p><ul><li>[Data Science Virtual Machine – Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>模板虚拟机
创建实验室后，将根据所选的虚拟机大小和映像创建模板 VM。 为模板 VM 配置课堂的所有教授内容。 若要了解详细信息，请参阅[如何管理模板虚拟机](how-to-create-manage-template.md)。 

默认情况下，Data Science VM 映像附带了此类课堂所需的许多数据科学框架和工具。 例如，映像包括：

- [Jupyter Notebook](http://jupyter-notebook.readthedocs.io/)：这是一种 Web 应用程序，数据科学家通过这种应用可获取原始数据、运行计算，并查看相同环境中的所有结果。 该应用程序将在模板 VM 中本地运行。  
- [Visual Studio Code](https://code.visualstudio.com/)：这是一种集成开发环境 (IDE)，可在编写和测试笔记本时提供丰富的交互式体验。 有关详细信息，请参阅[在 Visual Studio Code 中使用 Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support)。

### <a name="provide-notebooks-for-the-class"></a>为课堂提供笔记本
接下来的任务是向学生提供适用的笔记本。 若要适用自己的笔记本，可以在模板 VM 上本地保存笔记本。 

如果要使用 Azure 机器学习中的示例笔记本，请参阅[如何使用 Jupyter Notebook 配置环境](../machine-learning/how-to-configure-environment.md#jupyter)。 

### <a name="optional-enable-graphical-desktop-for-linux"></a>可选：启用适用于 Linux 的图形桌面 
**Data Science Virtual Machine – Ubuntu** 映像已经预配了 X2GO 服务器，并可以接受客户端连接。 设置模板 VM 时，无需执行其他步骤。 

### <a name="publish-the-template-machine"></a>发布模板虚拟机
发布模板时，注册到实验室的每个学生都将获取模板 VM 的副本，以及设置在该虚拟机上的所有本地工具和笔记本。

## <a name="how-students-connect-to-jupyter-notebooks"></a>学生如何连接到 Jupyter 笔记本？
发布模板后，每个学生都将可以访问一个包含为课堂预先配置了所有内容（包括 Jupyter Notebook）的 VM。 以下部分介绍了学生连接到 Jupyter Notebook 的不同方式。 

### <a name="for-windows-vms"></a>对于 Windows VM
如果已向学生提供 Windows VM，则他们需要连接到对应的 VM，并且使用本地用的 Jupyter Notebook。 

若要连接到 Windows VM，则学生可以使用远程桌面连接 (RDP)。 有关详细信息，请参阅[如何访问课堂实验室](how-to-use-classroom-lab.md)。 

使用 Mac 或 Chromebook 的学生可按照下文说明连接到 Data Science Windows VM。 

- [在 Mac 上使用 RDP 连接到 VM](connect-virtual-machine-mac-remote-desktop.md)
- [在 Chromebook 上使用 RDP 连接到 VM](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>对于 Linux VM
如果已向学生提供 Linux VM，则有几个选项可供学生连接到 VM 中的 Jupyter Notebook：

- 连接到 VM 后本地访问 Jupyter Notebook
    - 用于终端会话的到 VM 的 SSH
     - X2Go 连接到 VM 以实现图形会话
- 使用 SSH 隧道将学生的本地计算机直接连接到 VM 上的 Jupyter 服务器。 

以下部分介绍了以上有关连接到 Jupyter Notebook 方法的详细信息。 

#### <a name="ssh-to-virtual-machine"></a>SSH 到虚拟机
学生可以通过 SSH 从一个终端会话连接到其 Linux VM。 有关详细信息，请参阅[如何访问课堂实验室](how-to-use-classroom-lab.md)。 如果他们使用的是 Windows 客户端计算机，则需要通过下载 [PuTTY](https://www.putty.org/) 或将[ Windows 中的 OpenSSH](/windows-server/administration/openssh/openssh_install_firstuse) 启用到命令提示符的 SSH。 

1.  启动 VM。
2.  VM 运行后，单击“连接”，会弹出一个提供了 SSH 命令字符串的对话框，如以下示例所示：
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  切换到命令提示符或终端，粘贴此命令，然后按 **ENTER**。
4.  输入密码登录到 VM。 

学生连接到 VM 后，可以本地访问和运行 Jupyter Notebook。

#### <a name="x2go-to-virtual-machine"></a>X2Go 到虚拟机
**Data Science Virtual Machine – Ubuntu** 映像已经预配了 X2GO 服务器，并可以接受客户端连接。 若要连接到 Linux 计算机的图形桌面，学生需按照以下一次性步骤在其客户端计算机上设置 X2Go：

1.  为客户端平台下载并安装 [X2Go 客户端](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)。
2.  在 [Azure 实验室服务门户](https://labs.azure.com)中，确保启动目标 Linux VM。
3.  VM 运行后，单击“连接”，会弹出一个提供了 SSH 命令字符串的对话框，如以下示例所示：

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. 获得此信息后，打开 X2Go 客户端应用并创建新的会话。 
5.  在“会话首选项”窗格中填写以下值：
    - **会话名称**：可以任意填写，但建议使用实验室 VM 的名称。
     - **主机**：`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **登录名**：学生
     - **SSH 端口**：12345
     - **会话类型**：XFCE
6. 选择“确定”。 

    > [!NOTE]
     > 创建新的 X2Go 会话时，确保使用 SSH 端口，而不是 RDP 端口。

若要连接到 VM，完成以下步骤：    

1.  在 X2Go 客户端中，双击要连接到的 VM。 

    ![X2Go 客户端](./media/class-type-jupyter-notebook/x2go-client.png)
2. 输入用于连接到 VM 的密码。 （可能必须授予 X2Go 绕过防火墙的权限才能完成连接。）
3.  现在应会看到 Ubuntu Data Science VM 的图形界面。


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>通过 SSH 隧道连接到 VM 上 Jupyter 服务器
一些学生可能需要直接从其本地计算机直接连接到其 Vm 内的 Jupyter 服务器。 SSH 协议允许在本地计算机和远程服务器（在我们的用例中，即学生的实验室 VM）之间进行端口转发，因此，使在服务器特定端口上运行的应用程序可以通过“隧道”连接到本地计算机上的映射端口。 学生应按照以下步骤，通过 SSH 隧道连接到实验室 VM 上的 Jupyter 服务器：

1.  在 [Azure 实验室服务门户](https://labs.azure.com)中，确保启动目标 Linux VM。
2.  VM 运行后，单击“连接”，会弹出一个提供了 SSH 命令字符串的对话框，如以下字符串所示：

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. 在本地计算机上，启动一个终端或命令提示符，然后将 SSH 连接字符串复制到其中。 然后，将 `-L 8888:localhost:8888` 添加到命令字符串，从而在端口之间创建“隧道”。 最后的字符串应如下所示：

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. 按 **ENTER** 运行该命令。 
5.  出现提示时，提供用于连接到实验室 VM 的密码。 
6.  连接到 VM 后，使用以下命令启动 Jupyter 服务器： 

    ```bash
     jupyter notebook
     ```
7. 运行命令将在终端或命令提示符中提供 URL。 URL 应如下所示：

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. 将此 URL 粘贴到本地计算机的浏览器中，以连接并处理你的 Jupyter Notebook。 

    > [!NOTE]
    > 此外，Visual Studio Code 还能实现良好的 [Jupyter Notebook 编辑体验](https://code.visualstudio.com/docs/python/jupyter-support)。 可以按照[如何连接到远程 Jupyter 服务器](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server)的说明进行操作，并使用上一步中的相同 URL，从 VS Code 而不是从浏览器进行连接。 


## <a name="cost-estimate"></a>成本估算
我们来介绍此课程可能的估算成本。 课程将提供给 25 名学生。 课程时间计划为 20 小时。 此外，在计划的课程时间之外还为每名学生提供 10 小时配额，用于完成家庭作业或课外作业。 所选的 VM 大小为小型 GPU（计算），即 139 个实验室单位。 如果使用小型（20 个实验室单位）或中型（42 个实验室单位），则可以将以下公式中的实验室单元部分替换为正确的数字。  

以下示例是此课堂的成本估算：25 名学生 * （20 个计划小时 + 10 个配额小时）* 139 个实验室单位 * 0.01 美元/小时 = 1042.5 美元

有关定价的更多详细信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>结束语
本文逐步介绍了为 Jupyter Noteclass 课堂创建实验室的步骤。 对于其他机器学习课程，可以使用类似的设置。

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室时通用的。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)