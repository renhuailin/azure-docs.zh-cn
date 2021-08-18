---
title: 将 SSH 与 Hadoop 配合使用 - Azure HDInsight
description: 使用安全外壳 (SSH) 访问 HDInsight。 本文档介绍如何通过 Windows、Linux、Unix 或 macOS 客户端使用 ssh 命令连接到 HDInsight。
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020, devx-track-azurepowershell
ms.date: 02/28/2020
ms.openlocfilehash: 874782828c4d7e63c0e1ad5ea607ddbd09b31881
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291260"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>使用 SSH 连接到 HDInsight (Apache Hadoop)

了解如何使用[安全外壳 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) 安全地连接到 Azure HDInsight 上的 Apache Hadoop。 有关通过虚拟网络进行连接的信息，请参阅 [Azure HDInsight 虚拟网络体系结构](./hdinsight-virtual-network-architecture.md)。 另请参阅[为 Azure HDInsight 群集计划虚拟网络部署](./hdinsight-plan-virtual-network-deployment.md)。

下表包含使用 SSH 客户端连接到 HDInsight 时所需的地址和端口信息：

| 地址 | 端口 | 连接到... |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | 主头节点 |
| `<clustername>-ssh.azurehdinsight.net` | 23 | 辅助头节点 |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | 边缘节点（如果存在边缘节点，则可以是任何其他群集类型） |

将 `<clustername>` 替换为群集的名称。 将 `<edgenodename>` 替换为边缘节点的名称。

如果群集包含边缘节点，建议 __始终连接到边缘节点__（使用 SSH）。 头节点托管服务对于 Hadoop 运行状况而言至关重要。 边缘节点只会运行其上放置的软件。 有关使用边缘节点的详细信息，请参阅 [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)（在 HDInsight 中使用边缘节点）。

> [!TIP]
> 首先连接到 HDInsight 时，SSH 客户端可能会显示一个警告，指出无法验证主机。 当系统提示时，请选择“是”，将主机添加到 SSH 客户端的受信任服务器列表。
>
> 如果此前已使用同一名称连接到某个服务器，则可能会收到一个警告，指出存储的主机密钥与服务器的主机密钥不匹配。 请参阅 SSH 客户端的文档，了解如何删除服务器名称的现有条目。

## <a name="ssh-clients"></a>SSH 客户端

Linux、Unix 和 macOS 系统提供 `ssh` 和 `scp` 命令。 `ssh` 客户端通常用于在基于 Linux 或 Unix 的系统中创建远程命令行会话。 `scp` 客户端用于在客户端和远程系统之间安全地复制文件。

默认情况下，Microsoft Windows 不安装任何 SSH 客户端。 `ssh` 和 `scp` 客户端通过以下包提供给 Windows 使用：

* [OpenSSH 客户端](/windows-server/administration/openssh/openssh_install_firstuse)。 此客户端是 Windows 10 Fall Creators Update 中引入的可选功能。

* [Windows 10 版 Bash on Ubuntu](/windows/wsl/about)。

* [Azure Cloud Shell](../cloud-shell/quickstart.md)。 此 Cloud Shell 在浏览器中提供 Bash 环境。

* [Git](https://git-scm.com/)。

还有多个图形 SSH 客户端，例如 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) 和 [MobaXterm](https://mobaxterm.mobatek.net/)。 尽管可以使用这些客户端连接到 HDInsight，但连接的过程与使用 `ssh` 实用工具时不同。 有关详细信息，请参阅所用图形客户端的文档。

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>身份验证：SSH 密钥

SSH 密钥使用[公钥加密](https://en.wikipedia.org/wiki/Public-key_cryptography)对 SSH 会话进行身份验证。 SSH 密钥比密码更安全，使用它可以轻松确保对 Hadoop 群集进行访问时的安全性。

如果使用密钥保护 SSH 帐户，客户端必须在连接时提供匹配的私钥：

* 可将大多数客户端配置为使用 __默认密钥__。 例如，在 Linux 和 Unix 环境中，`ssh` 客户端会在 `~/.ssh/id_rsa` 位置查找私钥。

* 可以指定 __私钥的路径__。 在 `ssh` 客户端中，可使用 `-i` 参数指定私钥的路径。 例如，`ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`。

* 如果对不同的服务器使用 __多个私钥__，可以考虑使用 [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) 之类的实用工具。 在建立 SSH 会话时，可以通过 `ssh-agent` 实用工具自动选择要使用的密钥。

> [!IMPORTANT]
> 如果使用密码保护私钥，使用该密钥时必须输入密码。 为提供方便，`ssh-agent` 等实用工具可以缓存密码。

### <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

使用 `ssh-keygen` 命令创建公钥和私钥文件。 以下命令生成可在 HDInsight 中使用的 2048 位 RSA 密钥对：

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

在密钥创建过程中会提示你输入信息。 例如，密钥的存储位置，或者是否要使用密码。 完成该过程后，会创建两个文件：一个公钥文件和一个私钥文件。

* __公钥__ 用于创建 HDInsight 群集。 公钥的扩展名为 `.pub`。

* __私钥__ 用于在 HDInsight 群集中对客户端进行身份验证。

> [!IMPORTANT]
> 可以使用密码保护密钥。 密码实际上是私钥中的一个密码。 即使有人获取了私钥，但他们必须知道该密码才能使用该私钥。

### <a name="create-hdinsight-using-the-public-key"></a>使用公钥创建 HDInsight

| 创建方法 | 如何使用公钥 |
| ------- | ------- |
| Azure 门户 | 取消选中“对 SSH 使用群集登录密码”，然后选择“公钥”作为 SSH 身份验证类型。 最后，在“SSH 公钥”字段中选择公钥文件，或粘贴该文件的文本内容。</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png" alt-text="创建 HDInsight 群集时的 SSH 公钥对话框"::: |
| Azure PowerShell | 使用 [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet 的 `-SshPublicKey` 参数，并以字符串的形式传递公钥内容。|
| Azure CLI | 使用 [`az hdinsight create`](/cli/azure/hdinsight#az_hdinsight_create) 命令的 `--sshPublicKey` 参数，并以字符串的形式传递公钥内容。 |
| Resource Manager 模板 | 有关在模板中使用 SSH 密钥的示例，请参阅 [Deploy HDInsight on Linux with SSH key](https://azure.microsoft.com/resources/templates/hdinsight-linux-ssh-publickey/)（使用 SSH 密钥在 Linux 上部署 HDInsight）。 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.hdinsight/hdinsight-linux-ssh-publickey/azuredeploy.json) 文件中的 `publicKeys` 元素用于在创建群集时向 Azure 传递密钥。 |

## <a name="authentication-password"></a>身份验证：密码

可以使用密码保护 SSH 帐户。 使用 SSH 连接到 HDInsight 时，系统会提示输入密码。

> [!WARNING]
> Microsoft 不建议将密码身份验证用于 SSH。 密码可能被猜出，容易受到暴力破解攻击。 我们建议[使用 SSH 密钥进行身份验证](#sshkey)。

> [!IMPORTANT]
> SSH 帐户密码在创建 HDInsight 群集 70 天后过期。 如果密码过期，可以使用[管理 HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords) 文档中的信息更改它。

### <a name="create-hdinsight-using-a-password"></a>使用密码创建 HDInsight

| 创建方法 | 如何指定密码 |
| --------------- | ---------------- |
| Azure 门户 | 默认情况下，SSH 用户帐户的密码与群集登录帐户的密码相同。 若要使用不同的密码，请取消选中“对 SSH 使用群集登录密码”，然后在“SSH 密码”字段中输入密码。</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png" alt-text="创建 HDInsight 群集时的 SSH 密码对话框":::|
| Azure PowerShell | 使用 [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet 的 `--SshCredential` 参数，并传递包含 SSH 用户帐户名和密码的 `PSCredential` 对象。 |
| Azure CLI | 使用 [`az hdinsight create`](/cli/azure/hdinsight#az_hdinsight_create) 命令的 `--ssh-password` 参数，并提供密码值。 |
| Resource Manager 模板 | 有关在模板中使用密码的示例，请参阅 [Deploy HDInsight on Linux with SSH password](https://azure.microsoft.com/resources/templates/hdinsight-linux-ssh-password/)（使用 SSH 密码在 Linux 上部署 HDInsight）。 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.hdinsight/hdinsight-linux-ssh-password/azuredeploy.json) 文件中的 `linuxOperatingSystemProfile` 元素用于在创建群集时向 Azure 传递 SSH 帐户名和密码。|

### <a name="change-the-ssh-password"></a>更改 SSH 密码

有关更改 SSH 用户帐户密码的信息，请参阅 [Manage HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords)（管理 HDInsight）文档的 __Change passwords__（更改密码）部分。

## <a name="authentication-domain-joined-hdinsight"></a>已加入域的 HDInsight 身份验证

如果使用已加入域的 HDInsight 群集，必须在通过 SSH 本地用户建立连接后使用 `kinit` 命令。 此命令会提示输入域用户和密码，并在与群集关联的 Azure Active Directory 域中对会话进行身份验证。

还可以在每个已加入域的节点（例如头节点、边缘节点）上启用 Kerberos 身份验证，以便通过域帐户使用 ssh。 为此，请编辑 sshd 配置文件：

```bash
sudo vi /etc/ssh/sshd_config
```

取消注释并将 `KerberosAuthentication` 更改为 `yes`

```bash
sudo service sshd restart
```

使用 `klist` 命令验证 Kerberos 身份验证是否成功。

有关详细信息，请参阅 [Configure domain-joined HDInsight](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)（配置已加入域的 HDInsight）。

## <a name="connect-to-nodes"></a>连接到节点

可以通过 Internet 在端口 22 和 23 上访问头节点和边缘节点（如果有）。

* 连接到头节点时，请使用端口 22 连接到主头节点，使用端口 23 连接到辅助头节点。   要使用的完全限定的域名为 `clustername-ssh.azurehdinsight.net`，其中的 `clustername` 为群集的名称。

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```

* 连接到边缘节点时请使用端口 22。 完全限定的域名为 `edgenodename.clustername-ssh.azurehdinsight.net`，其中的 `edgenodename` 是在创建边缘节点时提供的名称。 `clustername` 是群集的名称。

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]
> 前面的示例假定你使用的是密码身份验证，或者系统会自动进行证书身份验证。 如果使用 SSH 密钥对进行身份验证，且系统不会自动使用证书，则请使用 `-i` 参数指定私钥。 例如，`ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`。

连接以后，提示符会改为指示 SSH 用户名和连接到的节点。 例如，在以 `sshuser` 身份连接到主头节点时，提示符为 `sshuser@<active-headnode-name>:~$`。

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>连接到工作节点和 Apache Zookeeper 节点

工作节点和 Zookeeper 节点不能从 Internet 直接访问。 但可以从群集头节点或边缘节点访问。 以下是连接到其他节点的一般步骤：

1. 使用 SSH 连接到头节点或边缘节点：

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. 通过 SSH 连接到头节点或边缘节点后，使用 `ssh` 命令连接到群集中的工作节点：

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    若要检索节点名称列表，请参阅[使用 Apache Ambari REST API 管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) 文档。

如果 SSH 帐户使用某个 __密码__ 进行了保护，请在连接时输入该密码。

如果 SSH 帐户使用 __SSH 密钥__ 进行了保护，请确保在客户端启用 SSH 转发。

> [!NOTE]
> 直接访问群集中所有节点的另一种方法是在 Azure 虚拟网络中安装 HDInsight。 然后，可将远程计算机加入相同的虚拟网络，这样就可以直接访问群集中的所有节点。
>
> 有关详细信息，请参阅[为 HDInsight 规划虚拟网络](hdinsight-plan-virtual-network-deployment.md)。

### <a name="configure-ssh-agent-forwarding"></a>配置 SSH 代理转发

> [!IMPORTANT]
> 以下步骤假设在基于 Linux 或 UNIX 的系统上操作，并且能够使用基于 Windows 10 的 Bash。 如果这些步骤不适用于系统，可能需要查阅 SSH 客户端的文档。

1. 使用文本编辑器打开 `~/.ssh/config`。 如果此文件不存在，可以在命令行中输入 `touch ~/.ssh/config` 来创建。

2. 将以下文本添加到 `config` 文件。

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    将 __Host__ 信息替换为使用 SSH 连接到的节点的地址。 上面的示例使用边缘节点。 此条目为指定的节点配置 SSH 代理转发。

3. 在终端中通过使用以下命令测试 SSH 代理转发：

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    此命令返回类似于以下文本的信息：

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    如果未返回任何内容，则 `ssh-agent` 没有运行。 有关详细信息，请参阅 [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh)（将 ssh-agent 与 ssh 配合使用）中的代理启动脚本信息，或者查阅 SSH 客户端文档。

4. 验证 **ssh-agent** 正在运行后，请使用以下步骤将 SSH 私钥添加到代理：

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    如果私钥存储在不同文件中，请将 `~/.ssh/id_rsa` 替换为该文件的路径。

5. 使用 SSH 连接到群集边缘节点或头节点。 然后使用 SSH 命令连接到工作节点或 zookeeper 节点。 使用转发的密钥建立连接。

## <a name="next-steps"></a>后续步骤

* [对 HDInsight 使用 SSH 隧道](hdinsight-linux-ambari-ssh-tunnel.md)
* [在 HDInsight 中使用边缘节点](hdinsight-apps-use-edge-node.md#access-an-edge-node)
* [将 SCP 与 HDInsight 配合使用](./use-scp.md)
