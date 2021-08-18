---
title: 在 Azure HDInsight 中更新 Azure 存储帐户访问密钥
description: 了解如何在 Azure HDInsight 群集中更新 Azure 存储帐户访问密钥。
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/29/2021
ms.openlocfilehash: 9adfb5e438aec8625cd7c4b164f5999181d9c31f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725354"
---
# <a name="update-azure-storage-account-access-keys-in-hdinsight-cluster"></a>在 HDInsight 群集中更新 Azure 存储帐户访问密钥

本文将介绍如何在 Azure HDInsight 中轮换主要或辅助存储帐户的 Azure 存储帐户访问密钥。

>[!CAUTION]
> 直接在存储端上轮换访问密钥会使 HDInsight 群集无法访问。

## <a name="prerequisites"></a>先决条件

* 我们将通过一种方法来以交错、交替的方式轮换存储帐户的主访问密钥和辅助访问密钥，以确保 HDInsight 群集在整个过程中都可访问。

    以下示例演示了如何使用主存储访问密钥和辅助存储访问密钥并对其设置轮换策略：
    1. 创建 HDInsight 群集时，在存储帐户上使用访问密钥 1。
    1. 为访问密钥 2 设置每 N 天进行轮换的轮换策略。 在此轮换中，更新 HDInsight 以使用访问密钥 1，然后在存储帐户上轮换访问密钥 2。
    1. 为访问密钥 1 设置每 N/2 天进行轮换的轮换策略。 在此轮换中，更新 HDInsight 以使用访问密钥 2，然后在存储帐户上轮换访问密钥 1。
    1. 使用上述方法时，访问密钥 1 将在 N/2 天、3N/2 天等进行轮换，访问密钥 2 将在 N 天、2N 天、3N 天等进行轮换。

* 若要为存储帐户密钥设置定期轮换，请参阅[自动轮换机密](../key-vault/secrets/tutorial-rotation-dual.md)。

## <a name="update-storage-account-access-keys"></a>更新存储帐户访问密钥

使用[脚本操作](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)更新密钥时请注意以下事项：

|属性 | Value |
|---|---|
|Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/update-storage-account-v01.sh`|
|节点类型|头|
|parameters|`ACCOUNTNAME` `ACCOUNTKEY` `-p`（可选）|

* `ACCOUNTNAME` 是 HDInsight 群集上的存储帐户的名称。
* `ACCOUNTKEY` 是 `ACCOUNTNAME` 的访问密钥。
* `-p` 是可选项。 如果指定此参数，则密钥不会加密，并以纯文本形式存储在 core-site.xml 文件中。

## <a name="known-issues"></a>已知问题

上述脚本仅在群集端直接更新访问密钥，不会在 HDInsight 资源提供程序端续订副本。 因此，在轮换访问密钥后，存储帐户中托管的脚本操作将失败。

解决方法：使用 [SAS URI](hdinsight-storage-sharedaccesssignature-permissions.md) 执行脚本操作，或使脚本可公开访问。

## <a name="next-steps"></a>后续步骤

* [添加其他存储帐户](hdinsight-hadoop-add-storage.md)