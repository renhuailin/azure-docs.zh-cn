---
title: 使用 SSH 连接到 SFTP 服务器
description: 使用 SSH 和 Azure 逻辑应用自动完成用于监视、创建、管理、发送和接收 SFTP 服务器文件的任务
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 08/05/2021
tags: connectors
ms.openlocfilehash: 32638d71f2c700700be5eb1b2ad63f18969d82a0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750020"
---
# <a name="create-and-manage-sftp-files-using-ssh-and-azure-logic-apps"></a>使用 SSH 和 Azure 逻辑应用监视、创建和管理 SFTP 文件

若要使用 [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) 协议自动完成用于在采用[安全文件传输协议 (SFTP)](https://www.ssh.com/ssh/sftp/) 的服务器上创建和管理文件的任务，可以使用 Azure 逻辑应用和 SFTP-SSH 连接器来生成并自动完成集成工作流。 SFTP 是通过任何可靠数据流提供文件访问、文件传输和文件管理的网络协议。

下面是可以自动完成的一些示例任务：

* 添加或更改文件时进行监视。
* 获取、创建、复制、重命名、更新、列出和删除文件。
* 创建文件夹。
* 获取文件内容和元数据。
* 将存档提取到文件夹。

在工作流中，可以使用触发器来监视 SFTP 服务器上的事件，并使输出可为其他操作所用。 可以使用操作针对 SFTP 服务器执行各种任务。 还可以让其他操作使用来自 SFTP-SSH 操作的输出。 例如，如果你定期从 SFTP 服务器检索文件，则可以使用 Office 365 Outlook 连接器或 Outlook.com 连接器发送有关这些文件及其内容的电子邮件警报。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

有关 SFTP-SSH 连接器和 SFTP 连接器之间的差异，请参阅本主题后面的[比较 SFTP-SSH 与 SFTP](#comparison) 部分。

## <a name="limits"></a>限制

* SFTP-SSH 连接器目前不支持以下 SFTP 服务器：

  * IBM DataPower
  * MessageWay
  * OpenText Secure MFT
  * OpenText GXS

* 支持[分块](../logic-apps/logic-apps-handle-large-messages.md)的 SFTP-SSH 操作最多可以处理 1 GB 的文件，而不支持分块的 SFTP-SSH 操作最多可以处理 50 MB 的文件。 默认分块大小为 15 MB。 但是，此大小可动态变化，最小为 5 MB，并可逐渐增加，最大为 50 MB。 大小的动态调整视诸如网络延迟、服务器响应时间等因素而定。

  > [!NOTE]
  > 对于[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中的逻辑应用，此连接器的 ISE 标记版本需要分块操作以使用 [ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

  当改用[指定固定区块大小](#change-chunk-size)时，可以重写此自适应行为。 此大小的范围为 5 MB 到 50 MB。 例如，假设你有一个 45 MB 的文件，以及一个可以支持该文件大小且没有延迟的网络。 自适应分块会导致多次调用，而不是一次调用。 若要减少调用次数，可以尝试设置 50 MB 的区块大小。 在不同情况下，如果逻辑应用超时（例如，当使用 15 MB 的块时），可以尝试将该大小减小到 5 MB。

  分块大小与连接相关联。 这个属性意味着你可以为支持分块的操作和不支持分块的操作使用同一个连接。 在这种情况下，不支持分块的操作的区块大小范围为 5 MB 到 50 MB。 下表显示了哪些 SFTP-SSH 操作支持分块：

  | 操作 | 分块支持 | 重写区块大小支持 |
  |--------|------------------|-----------------------------|
  | **复制文件** | 否 | 不适用 |
  | **创建文件** | 是 | 是 |
  | **创建文件夹** | 不适用 | 不适用 |
  | **删除文件** | 不适用 | 不适用 |
  | **将存档提取到文件夹** | 不适用 | 不适用 |
  | **获取文件内容** | 是 | 是 |
  | **使用路径获取文件内容** | 是 | 是 |
  | **获取文件元数据** | 不适用 | 不适用 |
  | **使用路径获取文件元数据** | 不适用 | 不适用 |
  | **列出文件夹中的文件** | 不适用 | 不适用 |
  | **重命名文件** | 不适用 | 不适用 |
  | **更新文件** | 否 | 不适用 |
  ||||

* SFTP-SSH 触发器不支持消息分块。 请求文件内容时，触发器仅选择 15 MB 或更小的文件。 若要获取大于 15 MB 的文件，请改为遵循以下模式：

  1. 使用只返回文件属性的 SFTP-SSH 触发器。 这些触发器具有包含说明的名（仅限属性）。

  1. 在使用触发器后，采取 SFTP-SSH Get file content（获取文件内容）操作。 这一操作将读取完整的文件并默认使用消息区块。

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH 与 SFTP 的比较

以下列表描述了不同于 SFTP 连接器的密钥 SFTP SSH 功能：

* 使用 [SSH.NET 库](https://github.com/sshnet/SSH.NET)，该库是支持 .NET 的开源安全外壳 (SSH) 库。

* 提供“创建文件夹”操作，用于在 SFTP 服务器上的指定路径中创建文件夹。

* 提供“重命名文件”操作，用于在 SFTP 服务器上重命名文件。

* 缓存至 SFTP 服务器的链接，缓存时间可长达 1 小时。 此功能可提高性能并减少连接器尝试连接到服务器的频率。 若要设置此缓存行为的持续时间，请在 SFTP 服务器上编辑 SSH 配置中的 [ClientAliveInterval 属性](https://man.openbsd.org/sshd_config#ClientAliveInterval)。

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* SFTP 服务器地址和帐户凭据，便于工作流访问 SFTP 帐户。 还需要有权访问 SSH 私钥和 SSH 私钥密码。 若要在上传大文件时使用分块，你需要对 SFTP 服务器上的根文件夹具有读写权限。 否则，你将收到“401 未授权”错误。

  SFTP-SSH 连接器支持私钥身份验证和密码身份验证。 然而，SFTP-SSH 连接器仅支持以下私钥格式、加密算法、指纹和密钥交换算法：

  * **私钥格式**：采用 OpenSSH 和 ssh.com 格式的 RSA (Rivest Shamir Adleman) 和 DSA（数字签名算法）密钥。 如果私钥为 PuTTY (.ppk) 文件格式，请先[将密钥转换为 OpenSSH (.pem) 文件格式](#convert-to-openssh)。
  * **加密算法**：DES-EDE3-CBC、DES-EDE3-CFB、DES-CBC、AES-128-CBC、AES-192-CBC 和 AES-256-CBC
  * **指纹**：MD5
  * 密钥交换算法：curve25519-sha256、curve25519-sha256@libssh.org、ecdh-sha2-nistp256、ecdh-sha2-nistp384、ecdh-sha2-nistp521、diffie-hellman-group-exchange-sha256、diffie-hellman-group-exchange-sha1、diffie-hellman-group16-sha512、diffie-hellman-group14-sha256、diffie-hellman-group14-sha1 和 diffie-hellman-group1-sha1

  在向工作流添加所需的 SFTP-SSH 触发器或操作之后，必须提供 SFTP 服务器的连接信息。 为此连接提供 SSH 密钥时，请勿手动输入或编辑密钥，否则可能导致连接失败。 而是确保从 SSH 私钥文件中复制密钥，并将该密钥粘贴到连接详细信息中。 有关详细信息，请参阅本文后面的[使用 SSH 连接到 SFTP](#connect) 部分。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 SFTP 帐户的逻辑应用工作流。 若要从 SFTP-SSH 触发器开始，请[创建一个空白逻辑应用工作流](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 SFTP-SSH 操作，请使用另一个触发器（例如“重复”触发器）启动工作流。

## <a name="how-sftp-ssh-triggers-work"></a>SFTP-SSH 触发器的工作原理

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>轮询行为

SFTP-SSH 触发器会轮询 SFTP 文件系统并查找自上次轮询以来已更改的任何文件。 某些工具允许保留文件更改时的时间戳。 在这种情况下，必须禁用此功能才能让触发器正常工作。 下面是一些常见设置：

| SFTP 客户端 | 操作 |
|-------------|--------|
| Winscp | 转到“选项” > “首选项” > “传输” > “编辑” > “保留时间戳” > “禁用”      |
| FileZilla | 转到“传输” > “保留已传输文件的时间戳” > “禁用”   |
|||

当触发器找到新文件时，会检查该新文件是否完整，以及是否未部分写入。 例如，当触发器检查文件服务器时，可能正在更改某个文件。 为了避免返回部分写入的文件，该触发器会记录具有最近更改的文件的时间戳，但不会立即返回该文件。 仅当再次轮询服务器时，触发器才会返回该文件。 有时，此行为可能会导致延迟，长达触发器轮询间隔的两倍。

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>触发器重复周期移动和偏移

你需要首先为其创建连接的基于连接的触发器（例如 SFTP SSH 触发器）不同于在 Azure 逻辑应用中以原生方式运行的内置触发器，如[重复周期触发器](../connectors/connectors-native-recurrence.md)。 在基于连接的周期性触发器中，重复周期计划不是控制执行的唯一驱动因素，并且时区只确定初始开始时间。 后续运行取决于定期计划、上一次触发器执行以及其他可能导致运行时间发生偏差或产生意外行为的因素。 例如，当夏令时 (DST) 开始和结束时，意外的行为可能包括无法维持指定的时间表。 若要确保重复周期时间在 DST 生效时不会变化，请手动调整重复周期。 这样一来，工作流将继续在预期时间运行。 否则，开始时间将在 DST 开始时向前移动 1 小时，在 DST 结束时向后移动 1 小时。 有关详细信息，请参阅[基于连接的触发器的重复周期](../connectors/apis-list.md#recurrence-for-connection-based-triggers)。

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>将基于 PuTTY 的密钥转换为 OpenSSH

PuTTY 格式和 OpenSSH 格式使用不同的文件扩展名。 PuTTY 格式使用 .ppk 或 PuTTY 私钥，文件扩展名。 OpenSSH 格式使用 pem 或隐私增强邮件的文件扩展名。 如果你的私钥采用 PuTTY 格式，并且必须使用 OpenSSH 格式，请先按照以下步骤将密钥转换为 OpenSSH 格式：

### <a name="unix-based-os"></a>基于 Unix 的 OS

1. 如果 PuTTY 工具尚未安装在系统上，请立即安装，例如：

   `sudo apt-get install -y putty`

1. 运行此命令，以便创建一个可以与 SFTP-SSH 连接器配合使用的文件：

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   例如：

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. [下载最新的 PuTTY 生成器 (puttygen.exe) 工具](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)（如果尚未这样做），然后启动该工具。

1. 在此屏幕上，选择“加载”。

   ![选择“加载”](./media/connectors-sftp-ssh/puttygen-load.png)

1. 浏览到 PuTTY 格式的私钥文件，然后选择“打开”。

1. 在“转换”菜单中，选择“导出 OpenSSH 密钥”。 

   ![选择“导出 OpenSSH 密钥”](./media/connectors-sftp-ssh/export-openssh-key.png)

1. 使用 `.pem` 文件扩展名保存该私钥文件。

## <a name="considerations"></a>注意事项

本部分介绍使用此连接器的触发器和操作时要查看的注意事项。

<a name="different-folders-trigger-processing-file-storage"></a>

### <a name="use-different-sftp-folders-for-file-upload-and-processing"></a>使用不同的 SFTP 文件夹进行文件上传和处理

在 SFTP 服务器上，使用单独的文件夹来存储上传的文件，并使用触发器来监视这些文件以进行处理。 否则，触发器将不会触发且行为不可预测，例如，跳过触发器处理的随机数量的文件。 但是，这一要求意味着你需要一种在这些文件夹之间移动文件的方法。 

如果发生此问题，请从触发器监视的文件夹中删除这些文件，并使用其他文件夹存储已上传的文件。

<a name="create-file"></a>

### <a name="create-file"></a>创建文件

若要在 SFTP 服务器上创建文件，可以使用 SFTP-SSH“创建文件”操作。 当此操作创建文件时，逻辑应用服务也会自动调用 SFTP 服务器来获取文件的元数据。 但是，如果在逻辑应用服务调用获取元数据之前移动新创建的文件，则会收到 `404` 错误消息 `'A reference was made to a file or folder which does not exist'`。 若要在创建文件后跳过读取文件元数据的操作，请按照以下步骤[添加并将“获取所有文件元数据”属性设置为“否”](#file-does-not-exist) 。

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>使用 SSH 连接到 SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请在搜索框中输入 `sftp ssh` 作为筛选器。 在触发器列表下，选择所需的触发器。

   -或-

   对于现有逻辑应用，请在要添加操作的最后一个步骤下，选择“新建步骤”。 在搜索框中，输入 `sftp ssh` 作为筛选器。 在操作列表下，选择所需的操作。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 为连接提供所需的详细信息。

   > [!IMPORTANT]
   >
   > 在“SSH 私钥”属性中输入 SSH 私钥时，请遵循以下附加步骤，帮助确保提供此属性的完整正确值。  无效的密钥会导致连接失败。

   可以使用任何文本编辑器。以下步骤以 Notepad.exe 为例，说明如何正确复制并粘贴密钥。

   1. 在文本编辑器中打开 SSH 私钥文件。 这些步骤以记事本为例。

   1. 在记事本的“编辑”菜单中，选择“全选”。  

   1. 选择“编辑”   >   “复制”。

   1. 在 SFTP-SSH 触发器或操作中，粘贴已复制到“SSH 私钥”属性中的完整密钥，支持换行。 不要手动输入或编辑密钥。

1. 输入完连接详细信息后，请选择“创建”。

1. 现在，为所选触发器或操作提供所需的详细信息，然后继续生成逻辑应用的工作流。

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>重写区块大小

若要重写分块使用的默认自适应行为，可以指定从 5 MB 到 50 MB 的固定区块大小。

1. 在操作的右上角，选择省略号按钮（“…”），然后选择“设置” 。

   ![打开 SFTP-SSH 设置](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. 在“内容传输”下的“区块大小”属性中，输入从 `5` 到 `50` 的整数值，例如 ： 

   ![指定要改用的区块大小](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. 完成后，选择“完成”。

## <a name="examples"></a>示例

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH 触发器：添加或修改文件时

在 SFTP 服务器上添加或更改文件时，此触发器将启动工作流。 例如跟进操作，工作流可以使用条件来检查文件内容是否符合指定的标准。 如果内容符合条件，则“获取文件内容”SFTP-SSH 操作可以获取内容，另一个 sftp SSH 操作可以将该文件放入 SFTP 服务器上的另一个文件夹中。

**企业示例**：可以使用此触发器监视 SFTP 文件夹中表示客户订单的新文件。 然后，可以使用“获取文件内容”等 SFTP-SSH 操作来获取订单内容以做进一步处理，并将该订单存储在订单数据库中。

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP - SSH 操作：使用路径获取文件内容

此操作通过指定文件路径从 SFTP 服务器上的文件中获取内容。 例如，可以在前面的示例中添加触发器，并添加文件内容必须符合的条件。 如果条件为 true，则可以运行获取内容的操作。

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>排查问题

本部分介绍常见错误或问题的可能解决方案。

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504 错误：“由于被连接方在一段时间后未正确响应，因此连接失败，或者，由于连接的主机未能响应，因此已建立的连接失败”，或者，“向 SFTP 服务器发出请求的用时已超过 '00:00:30' 秒”

在逻辑应用无法成功建立与 SFTP 服务器的连接时，可能会发生此错误。 此问题可能会有其他不同原因，因此请尝试以下故障排除选项：

* 连接超时为 20 秒。 请检查 SFTP 服务器的性能是否良好，并且中间设备（如防火墙）是否并未增加开销。

* 如果设置了防火墙，请确保将所在区域的托管连接器 IP 地址添加到已批准的列表中。 若要查找逻辑应用所在区域的 IP 地址，请参阅[托管连接器出站 IP - Azure 逻辑应用](/connectors/common/outbound-ip-addresses)。

* 如果间歇性地发生此错误，请将 SFTP-SSH 操作上的“重试策略”设置更改为一个高于默认重试次数 (4) 的重试次数。

* 请检查 SFTP 服务器是否对每个 IP 地址的连接数施加限制。 如果存在限制，则可能需要限制并发逻辑应用实例的数量。

* 若要降低建立连接的成本，请在 SFTP 服务器的 SSH 配置中，将 [ClientAliveInterval](https://man.openbsd.org/sshd_config#ClientAliveInterval) 属性增加到大约一小时。

* 请查看 SFTP 服务器日志，以检查来自逻辑应用的请求是否已到达 SFTP 服务器。 若要获取有关连接问题的详细信息，还可以在防火墙和 SFTP 服务器上运行网络跟踪。

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404 错误：“引用了不存在的文件或文件夹”

如果工作流通过 SFTP-SSH“创建文件”操作在 SFTP 服务器上创建文件，但在逻辑应用服务可以获取该文件的元数据之前立即移动了文件，则可能会发生此错误。 当工作流运行“创建文件”操作时，逻辑应用服务会自动调用 SFTP 服务器来获取文件的元数据。 但是，如果逻辑应用移动了该文件，逻辑应用服务将无法再找到该文件，因此你将收到 `404` 错误消息。

如果无法避免或延迟移动文件，则可以在创建文件后跳过读取文件元数据的操作，方法是执行以下步骤：

1. 在“创建文件”操作中，打开“添加新参数”列表，选择“获取所有文件元数据”属性，并将值设置为“否”   。

1. 如果以后需要此文件元数据，可以使用“获取文件元数据”操作。

## <a name="connector-reference"></a>连接器参考

有关此连接器的更多技术详细信息，例如触发器、操作和限制（如此连接器的 Swagger 文件所述），请参阅[连接器的参考页](/connectors/sftpwithssh/)。

> [!NOTE]
> 对于[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中的逻辑应用，此连接器的 ISE 标记版本需要分块操作以使用 [ISE 消息限制](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
