---
title: Azure 机器学习的 Git 集成
titleSuffix: Azure Machine Learning
description: 了解 Azure 机器学习如何与本地 Git 存储库集成，以在训练运行过程中跟踪存储库、分支和当前的提交信息。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 04/08/2021
ms.openlocfilehash: 37c7d94bfb645e444b4fb937823dcb8d56bcb9c7
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112457726"
---
# <a name="git-integration-for-azure-machine-learning"></a>Azure 机器学习的 Git 集成

[Git](https://git-scm.com/) 是一种常用的版本控制系统，可用于共享和协作处理项目。 

Azure 机器学习完全支持用于跟踪工作的 Git 存储库 - 你可以将存储库直接克隆到共享工作区文件系统上，在本地工作站上使用 Git，或者从 CI/CD 管道使用 Git。

在向 Azure 机器学习提交作业时，如果源文件存储在本地 git 存储库中，那么系统会将有关存储库的信息作为训练过程的一部分进行跟踪。

由于 Azure 机器学习会跟踪来自本地 git 存储库的信息，因此它不会绑定到任何特定的中心存储库。 可以从 GitHub、GitLab、Bitbucket、Azure DevOps 或任何其他与 git 兼容的服务克隆存储库。

> [!TIP]
> 使用 Visual Studio Code 通过图形用户界面与 Git 交互。 若要使用 Visual Studio Code 连接到 Azure 机器学习远程计算实例，请参阅[在 Visual Studio Code（预览版）中连接到 Azure 机器学习计算实例](how-to-set-up-vs-code-remote.md)
>
> 有关 Visual Studio Code 版本控制功能的详细信息，请参阅[使用 VS Code 中的版本控制](https://code.visualstudio.com/docs/editor/versioncontrol)和[在 VS Code 中使用 GitHub](https://code.visualstudio.com/docs/editor/github)。

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>将 Git 存储库克隆到你的工作区文件系统
Azure 机器学习为工作区中的所有用户提供了一个共享文件系统。
要将 Git 存储库克隆到此文件共享中，建议创建一个计算实例并[打开一个终端](how-to-access-terminal.md)。
打开终端后，你可以访问完整的 Git 客户端，并可以通过 Git CLI 体验来克隆和使用 Git。

我们建议你将存储库克隆到你的用户目录中，避免与其他人直接在你的工作分支上发生冲突。

你可以克隆你能够向其证明身份的任何 Git 存储库（GitHub、Azure Repos、BitBucket 等）

有关克隆的详细信息，请参阅[如何使用 Git CLI](https://guides.github.com/introduction/git-handbook/) 页面上的指南。

## <a name="authenticate-your-git-account-with-ssh"></a>通过 SSH 对 Git 帐户进行身份验证
### <a name="generate-a-new-ssh-key"></a>生成新的 SSH 密钥
1) 在 Azure 机器学习笔记本选项卡中[打开终端窗口](./how-to-access-terminal.md)。

2) 粘贴以下文本，并将其替换为电子邮件地址。

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

这会创建一个新的 SSH 密钥，并使用提供的电子邮件作为标签。

```
> Generating public/private rsa key pair.
```

3) 当系统提示“输入用于保存密钥的文件”时，请按 Enter 键。 这会接受默认文件位置。

4) 验证该默认位置为“/home/azureuser/.ssh”，然后按 Enter。 否则，请指定位置“/home/azureuser/.ssh”。

> [!TIP]
> 请确保 SSH 密钥保存在“/home/azureuser/.ssh”中。 此文件保存在计算实例上，只有计算实例的所有者才能访问

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) 在提示符下，键入安全密码。 建议向 SSH 密钥添加密码，以提高安全性

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>将公钥添加到 Git 帐户
1) 在终端窗口中，复制公钥文件的内容。 如果重命名了该密钥，请将 id_rsa.pub 替换为公钥文件名。

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **在终端中复制和粘贴**
> * Windows：使用 `Ctrl-Insert` 复制，使用 `Ctrl-Shift-v` 或 `Shift-Insert` 粘贴。
> * Mac OS：使用 `Cmd-c` 复制，使用 `Cmd-v` 粘贴。
> * FireFox/IE 可能不会正确支持剪贴板权限。

2) 选择并复制剪贴板中的密钥输出。

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate#step-2--add-the-public-key-to-azure-devops-servicestfs) 从 **步骤 2** 开始。

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2)。 从“步骤 4”开始。

### <a name="clone-the-git-repository-with-ssh"></a>通过 SSH 克隆 Git 存储库

1) 从 Git 存储库中复制 SSH Git 克隆 URL。

2) 将 URL 粘贴到下面的 `git clone` 命令中，以使用 SSH Git 存储库 URL。 该 URL 应类似于：

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

将看到如下所示的响应：

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH 可能会显示服务器的 SSH 指纹，并要求你对其进行验证。 应验证显示的指纹是否与 SSH 公钥页中的某个指纹相匹配。

SSH 将在连接到未知主机时显示此指纹，以防止[中间人攻击](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10))。 接受主机的指纹后，SSH 将不再提示你，除非指纹发生更改。

3) 当系统询问是否要继续连接时，请键入 `yes`。 Git 将克隆存储库，并设置原点远程，以便在将来的 Git 命令中使用 SSH 进行连接。

## <a name="track-code-that-comes-from-git-repositories"></a>跟踪来自 Git 存储库的代码

从 Python SDK 或机器学习 CLI 提交训练运行时，训练模型所需的文件将上传到工作区。 如果可在开发环境中使用 `git` 命令，则上传过程会使用该命令检查文件是否存储在 git 存储库中。 如果是，那么 git 存储库中的信息也会作为训练运行的一部分上传。 此信息存储在训练运行的以下属性中：

| 属性 | 用于获取值的 Git 命令 | 说明 |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | 从中克隆存储库的 URI。 |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | 从中克隆存储库的 URI。 |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | 提交运行时的活动分支。 |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | 提交运行时的活动分支。 |
| `azureml.git.commit` | `git rev-parse HEAD` | 为运行提交的代码的提交哈希。 |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | 为运行提交的代码的提交哈希。 |
| `azureml.git.dirty` | `git status --porcelain .` | 如果分支/提交异常，则为 `True`；否则为 `false`。 |

对于使用估计器、机器学习管道或脚本运行的运行，将发送此信息。

如果训练文件不在开发环境的 git 存储库中，或者 `git` 命令不可用，则不会跟踪与 git 相关的信息。

> [!TIP]
> 若要检查 git 命令在开发环境中是否可用，请打开 shell 会话、命令提示符、PowerShell 或其他命令行接口，并键入以下命令：
>
> ```
> git --version
> ```
>
> 如果已安装且在路径中，则会收到类似于 `git version 2.4.1` 的响应。 有关在开发环境中安装 git 的详细信息，请参阅 [Git 网站](https://git-scm.com/)。

## <a name="view-the-logged-information"></a>查看记录的信息

Git 信息存储在训练运行的属性中。 可以使用 Azure 门户、Python SDK 和 Azure CLI 查看此信息。 

### <a name="azure-portal"></a>Azure 门户

1. 在[工作室门户](https://ml.azure.com)中，选择你的工作区。
1. 选择“试验”，然后选择一个试验。
1. 从“运行号”列中选择一个运行。
1. 选择“输出 + 日志”，然后展开“日志”和“azureml”条目  。 选择以“###\_azure”开头的链接。

记录的信息包含类似于以下 JSON 的文本：

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

提交训练运行后，将返回 [Run](/python/api/azureml-core/azureml.core.run%28class%29) 对象。 此对象的 `properties` 属性包含记录的 git 信息。 例如，以下代码可检索提交哈希：

```python
run.properties['azureml.git.commit']
```

### <a name="azure-cli"></a>Azure CLI

`az ml run` CLI命令可用于从运行中检索属性。 例如，以下命令返回名为 `train-on-amlcompute` 的试验中最后一次运行的属性：

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

有关详细信息，请参阅 [az ml run](/cli/azure/ml(v1)/run) 参考文档。

## <a name="next-steps"></a>后续步骤

* [使用模型训练的计算目标](how-to-set-up-training-targets.md)
