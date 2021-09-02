---
title: 在 Azure VMware 解决方案上配置 GitHub Enterprise Server
description: 了解如何在 Azure VMware 解决方案私有云上设置 GitHub Enterprise Server。
ms.topic: how-to
ms.date: 07/07/2021
ms.openlocfilehash: 7b3289742c765e6b809cd96db3bfa51d8407a282
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322779"
---
# <a name="configure-github-enterprise-server-on-azure-vmware-solution"></a>在 Azure VMware 解决方案上配置 GitHub Enterprise Server

在本文中，你将在 Azure VMware 解决方案私有云上设置 GitHub Enterprise Server（即 [GitHub.com](https://github.com/) 的“本地”版本）。 该方案包含一个 GitHub Enterprise Server 实例，该实例可以为 3,000 名开发人员提供服务，这些开发人员在 GitHub Actions 上每分钟执行多达 25 个作业。 这包括（在撰写本文时）预览功能（例如 GitHub Actions）的设置。 要根据你的特定需求自定义设置，请查看[在 VMware 上安装 GitHub Enterprise Server](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations) 中列出的要求。

## <a name="before-you-begin"></a>准备阶段

GitHub Enterprise Server 需要有效的许可证密钥。 您可以注册以获得[试用许可证](https://enterprise.github.com/trial)。 如果希望通过集成来扩展 GitHub Enterprise Server 的功能，则有资格获得免费的五席位开发人员许可证。 通过 [GitHub 的合作伙伴计划](https://partner.github.com/)申请此许可证。

## <a name="install-github-enterprise-server-on-vmware"></a>在 VMware 上安装 GitHub Enterprise Server

1. 下载适用于 VMware ESXi/vSphere (OVA) 的[最新版本的 GitHub Enterprise Server](https://enterprise.github.com/releases/2.19.0/download)并[部署 OVA 模板](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html)（之前已下载）。

   :::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="显示 VMware 上的 GitHub Enterprise Server 安装选项的屏幕截图。"::: 

   :::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="显示“部署 OVA 模板”菜单选项的屏幕截图。":::    

1. 为新虚拟机提供可识别的名称，例如 GitHubEnterpriseServer。 不需要在 VM 名称中包含发布详细信息，因为这些详细信息在升级实例时会变得陈旧。 

1. 现在选择所有默认值（我们将在短期内编辑这些详细信息），然后等待 OVA 导入。

1. 导入后，根据需要[调整硬件配置](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#creating-the-github-enterprise-server-instance)。 在我们的示例方案中，我们将需要以下配置。

   | 资源 | 标准设置 | 标准设置 +“Beta 功能”（操作） |
   | --- | --- | --- |
   | vCPU | 4 | 8 |
   | 内存 | 32 GB | 61 GB |
   | 附加存储 | 250 GB | 300 GB |
   | 根存储 | 200 GB | 200 GB |

   你的需求可能会有所不同。 请参阅[在 VMware 上安装 GitHub Enterprise Server](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations) 中关于硬件注意事项的指南。 另请参阅[为 VMware 添加 CPU 或内存资源](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware)，以根据你的情况自定义硬件配置。

## <a name="configure-the-github-enterprise-server-instance"></a>配置 GitHub Enterprise Server 实例

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="“安装 GitHub Enterprise”窗口的屏幕截图。"::: 

新预配的虚拟机 (VM) 启动后，[通过浏览器对其进行配置](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance)。 你需要上传许可证文件并设置管理控制台密码。 请确保在安全的地方记下此密码。

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="用于添加新 SSH 密钥的“GitHub Enterprise SSH 访问屏幕”的屏幕截图。":::    

建议至少执行以下步骤：

1. 将公共 SSH 密钥上传到管理控制台，以便[通过 SSH 访问管理 shell](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)。 

2. [在实例上配置 TLS](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls)，以便可以使用由受信任的证书颁发机构签名的证书。 应用设置。

   :::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="显示应用到实例的设置的屏幕截图。":::

1. 在实例重启时，为 GitHub Actions 配置 Blob 存储。

   >[!NOTE]
   >GitHub Actions [当前在 Github Enterprise Server 版本 2.22 上作为受限 beta 版本提供](https://docs.github.com/en/enterprise/admin/github-actions)。
    
   要在 GitHub Enterprise Server 上启用 GitHub Actions（当前可作为“beta 版本”功能），必须具备外部 blob 存储。 Actions 使用此外部 blob 存储来存储工件和日志。 GitHub Enterprise Server 上的 Actions [支持将 Azure Blob 存储作为存储提供程序](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements)（以及其他存储提供程序）。 因此，我们将预配新的 Azure 存储帐户，其[存储帐户类型](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts)为 BlobStorage。
    
   :::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="屏幕截图显示为预配 Azure Blob 存储帐户而需要输入的实例详细信息。":::
    
1. 新 BlobStorage 资源部署完成后，保存连接字符串（位于“访问密钥”下）。 稍后你将需要这个字符串。

1. 实例重启后，在实例上创建新的管理员账户。 请务必也要记下该用户的密码。

   :::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="显示 GitHub Enterprise 的“创建管理员帐户”的屏幕截图。":::

### <a name="other-configuration-steps"></a>其他配置步骤

要强化实例以用于生产用途，建议执行以下可选设置步骤：

1. 配置[高可用性](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/)以防止出现以下情况：

    - 软件崩溃（操作系统或应用程序级别）
    - 硬件故障（存储、CPU、RAM 等）
    - 虚拟化主机系统故障
    - 网络在逻辑或物理上切断

2. [配置](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance)[备份实用程序](https://github.com/github/backup-utils)，提供用于灾难恢复的版本快照，并托管在与主实例分离的可用性中。
3. 使用有效的 TLS 证书[设置子域隔离](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)，以缓解跨站点脚本和其他相关漏洞。


## <a name="set-up-the-github-actions-runner"></a>设置 GitHub Actions 运行器

> [!NOTE]
> GitHub Actions [当前在 Github Enterprise Server 版本 2.22 上作为受限 beta 版本提供](https://docs.github.com/en/enterprise/admin/github-actions)。

此时，你应该有了一个正在运行的 GitHub Enterprise Server 实例，并创建了一个管理员帐户。 你还应该具有 GitHub Actions 用于持久性的外部 blob 存储。

创建一个用于让 GitHub Actions 运行的位置。我们将再次使用 Azure VMware 解决方案。

1. 在群集上配置新的 VM，并使其基于[最新版本的 Ubuntu 服务器](http://releases.ubuntu.com/20.04.1/)。

   :::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="显示虚拟机名称和新 VM 预配位置的屏幕截图。":::

1. 选择计算资源、存储和兼容性，继续完成设置。

1. 选择要在 VM 上安装的来宾 OS。

   :::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="该屏幕截图显示要安装在 VM 上的来宾 OS 系列和来宾 OS 版本。":::

1. 创建 VM 后，接通其电源并通过 SSH 连接到该 VM。

1. 接下来，安装 [Actions 运行器](https://github.com/actions/runner)应用程序，该应用程序从 GitHub Actions 工作流运行作业。 通过[发布页面](https://github.com/actions/runner/releases)或运行以下快速脚本，确定并下载 Actions 运行器的最新 Linux x64 版本。 该脚本需要你的 VM 上同时存在 curl 和 [jq](https://stedolan.github.io/jq/)。

   ```bash
   LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \
    
   jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )
    
   DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \
    
   jq -r '.browser\_download\_url' )
    
   curl -OL $DOWNLOAD\_URL
   ```
    
   你现在应该在 VM 上有一个本地文件，即 actions-runner-linux-arm64-\*.tar.gz。 在本地提取这个 tarball：
    
   ```bash
   tar xzf actions-runner-linux-arm64-\*.tar.gz
   ```
    
   提取后将在本地解压缩一些文件，其中包括 `config.sh` 和 `run.sh` 脚本。

## <a name="enable-github-actions"></a>启用 GitHub Actions

>[!NOTE]
>GitHub Actions [当前在 Github Enterprise Server 版本 2.22 上作为受限 beta 版本提供](https://docs.github.com/en/enterprise/admin/github-actions)。

在 GitHub Enterprise Server 实例上配置和启用 GitHub Actions。 

1. [通过 SSH 访问 GitHub Enterprise Server 实例的管理 shell](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)，然后运行以下命令：

1. 设置包含 Blob 存储连接字符串的环境变量。

   ```bash
   export CONNECTION\_STRING="<your connection string from the blob storage step>"
   ```    

1. 配置操作存储。
    
   ```bash
   ghe-config secrets.actions.storage.blob-provider azure
  
   ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING`      
   ```

1. 应用设置。

   ```bash
   ghe-config-apply
   ```    

1. 执行预检查，安装 GitHub Enterprise Server 上的 Actions 所需的其他软件。
    
   ```bash
   ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"
   ```

1. 启用操作，并重新应用配置。
 
   ```bash
   ghe-config app.actions.enabled true
    
   ghe-config-apply      
   ```

1. 检查 Blob 存储的运行状况。

   ```bash
   ghe-actions-check -s blob
   ```

   应会看到以下输出：“Blob 存储正常”。

1. 现在已配置 GitHub Actions，请为用户启用。 以管理员身份登录到 GitHub Enterprise Server 实例，然后选择任意页面右上角的 :::image type="icon" source="media/github-enterprise-server/rocket-icon.png":::。 

1. 在左侧边栏中，依次选择“企业概述”、“策略”、“操作”，然后选择用于为所有组织启用 Actions 的选项。   

1. 从“自托管运行器”选项卡中配置运行器。选择“新增”，然后从下拉菜单中选择“新建运行器”  。 系统将显示一组要运行的命令。

1. 复制命令以配置运行器，例如：

   ```bash
   ./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA
   ```

1. 复制 `config.sh` 命令并将其粘贴到 Actions 运行器（先前已创建）上的会话中。

   :::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="显示 GitHub Actions 运行器注册和设置的屏幕截图。":::

1. 使用 `./run.sh` 命令运行运行器：

   >[!TIP]
   >要使该运行器可用于企业中的组织，请编辑其组织访问权限。 可以将访问权限限制为组织的一个子集，甚至特定存储库。
   >
   >:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="如何编辑自托管运行器的访问权限的屏幕截图。":::


## <a name="optional-configure-github-connect"></a>（可选）配置 GitHub Connect

尽管这一步骤是可选的，但如果你计划使用 GitHub.com 上可用的开源操作，我们建议执行该步骤。 它这一步可以让你在工作流中引用这些可重复使用的操作，从而在他人的工作基础上继续开发。

要启用 GitHub Connect，请遵循[使用 GitHub Connect 启用对 GitHub.com 操作的自动访问权限](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)。

启用 GitHub Connect 之后，选择“在工作流运行中使用来自 GitHub.com 的操作的服务器”选项。

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="“服务器可以在工作流运行中使用来自 GitHub.com 的操作”为“已启用”的屏幕截图。":::

## <a name="set-up-and-run-your-first-workflow"></a>设置并运行首个工作流

现在已设置了 Actions 和 GitHub Connect，接下来让我们妥善运用。 这是一个示例工作流，该工作流引用了出色的 [octokit/request-action](https://github.com/octokit/request-action)，允许我们使用由 GitHub Actions 提供技术支持的 GitHub API 通过交互来为 GitHub 编写脚本。

在此基本工作流中，我们将使用 `octokit/request-action`，利用 API ​​在 GitHub 上打开问题。

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="示例工作流的屏幕截图。":::

>[!NOTE]
>GitHub.com 托管该操作，但是当它在 GitHub Enterprise Server 上运行时，会自动使用 GitHub Enterprise Server API。

如果选择不启用 GitHub Connect，则可以使用以下备用工作流。

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="替代示例工作流的屏幕截图。":::

1. 导航到你实例上的存储库，并将上述工作流添加为：`.github/workflows/hello-world.yml`

   :::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="另一个替代示例工作流的屏幕截图。":::

1. 在存储库的“操作”选项卡中，等待工作流程执行。

   :::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="已执行示例工作流的屏幕截图。":::

   还可以看到运行器对工作流进行处理。

   :::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="运行器处理的工作流屏幕截图。":::

如果运行顺利，你应该在存储库中看到一个名为“Hello world”的新问题。

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="GitHub 中由 github-actions 创建的 Hello world 问题的屏幕截图。":::

祝贺你！ 你刚刚在 Azure VMware Solution 私有云上运行的 GitHub Enterprise Server 上完成了第一个 Actions 工作流。

本文在你的 Azure VMware 解决方案私有云之上设置了 GitHub Enterprise Server 的新实例，该实例是 GitHub.com 的自托管等效项。 该实例包括对 GitHub Actions 的支持，并使用 Azure Blob 存储来保持日志和工件的持久性。 但是，我们只是浅显地介绍了你可以通过 GitHub Actions 执行的操作。 在 [GitHub 的 Marketplace](https://github.com/marketplace)上查看 Actions 列表，或[自行创建](https://docs.github.com/en/actions/creating-actions)。

## <a name="next-steps"></a>后续步骤

现在你已经了解了在 Azure VMware 解决方案私有云上设置了 GitHub Enterprise Server 的知识，你可能需要了解以下内容： 

- [如何开始使用 GitHub Actions](https://docs.github.com/en/actions)
- [如何加入 beta 计划](https://resources.github.com/beta-signup/)
- [GitHub Enterprise Server 的管理](https://githubtraining.github.io/admin-training/#/00_getting_started)
