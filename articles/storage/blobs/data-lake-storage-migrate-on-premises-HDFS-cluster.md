---
title: 使用 Azure Data Box 从本地 HDFS 存储迁移到 Azure 存储
description: 使用 Data Box 设备将数据从本地 HDFS 存储迁移到 Azure 存储（Blob 存储或 Data Lake Storage Gen2）。
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 2ac820aa7606ae310c5a7a3fd28869f34f846aaf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621179"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>使用 Azure Data Box 从本地 HDFS 存储迁移到 Azure 存储

通过使用 Data Box 设备，可将数据从 Hadoop 群集的本地 HDFS 存储迁移到 Azure 存储（Blob 存储或 Data Lake Storage Gen2）。 有 Data Box Disk、80-TB Data Box 或 770-TB Data Box Heavy 可供选择。

本文将帮助你完成以下任务：

> [!div class="checklist"]
> - 准备好迁移数据。
> - 将数据复制到 Data Box Disk、Data Box 或 Data Box Heavy 设备。
> - 将设备寄回 Microsoft。
> - 将访问权限应用于文件和目录（仅限 Data Lake Storage Gen2）

## <a name="prerequisites"></a>先决条件

要完成迁移，你需要准备好以下各项。

- 一个 Azure 存储帐户。

- 一个包含源数据的本地 Hadoop 群集。

- 一台 [Azure Data Box 设备](https://azure.microsoft.com/services/storage/databox/)。

  - [订购 Data Box](../../databox/data-box-deploy-ordered.md) 或 [Data Box Heavy](../../databox/data-box-heavy-deploy-ordered.md)。

  - 为 [Data Box](../../databox/data-box-deploy-set-up.md) 或 [Data Box Heavy](../../databox/data-box-heavy-deploy-set-up.md) 布线并将其连接到本地网络。

如果准备就绪，就开始吧。

## <a name="copy-your-data-to-a-data-box-device"></a>将数据复制到 Data Box 设备

如果你的数据刚好适合一台 Data Box 设备，则将数据复制到该 Data Box 设备。

如果数据大小超出了 Data Box 设备的容量，请使用[（可选过程）跨多台 Data Box 设备拆分数据](#appendix-split-data-across-multiple-data-box-devices)，然后执行此步骤。

若要将数据从本地 HDFS 存储复制到 Data Box 设备，请进行几项设置，然后使用 [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 工具。

请按照以下步骤，通过 Blob/对象存储的 REST API 将数据复制到 Data Box 设备。 REST API 接口会使设备显示为群集的 HDFS 存储。

1. 通过 REST 复制数据之前，请确定要连接到 Data Box 或 Data Box Heavy 中的 REST 接口的安全性和连接基元。 登录 Data Box 的本地 Web UI，转到“连接并复制”页面。 根据设备的 Azure 存储帐户，在“访问设置”下找到并选择“REST” 。

    ![“连接并复制”页面](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. 在“访问存储帐户并上传数据”对话框中，复制 Blob 服务终结点和存储帐户密钥 。 省略 Blob 服务终结点中的 `https://` 和尾随斜杠。

    这样的话，终结点为：`https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`。 要使用的 URI 的主机部分是：`mystorageaccount.blob.mydataboxno.microsoftdatabox.com`。 有关示例，请查看如何[通过 HTTP 连接到 REST](../../databox/data-box-deploy-copy-data-via-rest.md)。

     ![“访问存储帐户并上传数据”对话框](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. 将终结点和 Data Box 或 Data Box Heavy 节点 IP 地址添加到每个节点上的 `/etc/hosts`。

    ```
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    如果要对 DNS 使用其他某种机制，应确保可解析 Data Box 终结点。

4. 将 shell 变量 `azjars` 设为 jar 文件 `hadoop-azure` 和 `azure-storage` 的位置。 可在 Hadoop 安装目录下找到这些文件。

    若要确定这些文件是否存在，请使用以下命令：`ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`。 请将 `<hadoop_install_dir>` 占位符替换为安装 Hadoop 的目录路径。 请务必使用完全限定的路径。

    示例：

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. 创建要用于数据复制的存储容器。 还应指定目标目录，作为此命令的一部分。 目前，它可能是一个虚拟的目标目录。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    - 请将 `<blob_service_endpoint>` 占位符替换为 Blob 服务终结点的名称。

    - 请将 `<account_key>` 占位符替换为帐户的访问密钥。

    - 请将 `<container-name>` 占位符替换为容器的名称。

    - 请将 `<destination_directory>` 占位符替换为要将数据复制到的目录的名称。

6. 运行 list 命令，确保容器和目录均已创建。

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   - 请将 `<blob_service_endpoint>` 占位符替换为 Blob 服务终结点的名称。

   - 请将 `<account_key>` 占位符替换为帐户的访问密钥。

   - 请将 `<container-name>` 占位符替换为容器的名称。

7. 将数据从 Hadoop HDFS 复制到 Data Box Blob 存储，然后放入前面创建的容器。 如果找不到要复制到的目录，命令会自动创建该目录。

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    - 请将 `<blob_service_endpoint>` 占位符替换为 Blob 服务终结点的名称。

    - 请将 `<account_key>` 占位符替换为帐户的访问密钥。

    - 请将 `<container-name>` 占位符替换为容器的名称。

    - 请将 `<exlusion_filelist_file>` 占位符替换为包含文件排除列表的文件名称。

    - 请将 `<source_directory>` 占位符替换为包含要复制的数据的目录名称。

    - 请将 `<destination_directory>` 占位符替换为要将数据复制到的目录的名称。

    使用 `-libjars` 选项将 `hadoop-azure*.jar` 和依赖的 `azure-storage*.jar` 文件设为可用于 `distcp`。 对于某些群集，可能已发生此情况。

    以下示例演示如何使用 `distcp` 命令复制数据。

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```

    若要提高复制速度：

    - 尝试更改映射器数目。 （在上述示例中，`m` 表示 4 个映射器。）

    - 尝试并行运行多个 `distcp`。

    - 请记住，大文件的性能优于小文件。

## <a name="ship-the-data-box-to-microsoft"></a>将 Data Box 寄送到 Microsoft

按照以下步骤准备 Data Box 设备并将其寄送到 Microsoft。

1. 首先，[准备寄送 Data Box 或 Data Box Heavy](../../databox/data-box-deploy-copy-data-via-rest.md)。

2. 设备准备完成后，下载 BOM 文件。 稍后会使用这些 BOM 或清单文件来验证上传到 Azure 的数据。

3. 关闭设备并拔下电缆。

4. 安排 UPS 取件。

    - 有关 Data Box 设备的信息，请查看[寄送 Data Box](../../databox/data-box-deploy-picked-up.md)。

    - 有关 Data Box Heavy 设备的信息，请查看[寄送 Data Box Heavy](../../databox/data-box-heavy-deploy-picked-up.md)。

5. Microsoft 收到你的设备后，会将它连接到数据中心网络，数据会被上传到你在设备下单时指定的存储帐户。 根据 BOM 文件验证所有数据是否均已上传到 Azure。

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>将访问权限应用于文件和目录（仅限 Data Lake Storage Gen2）

你已将数据上传到 Azure 存储帐户。 现在，将访问权限应用于文件和目录。

> [!NOTE]
> 仅当使用 Azure Data Lake Storage Gen2 作为数据存储时，才需要执行此步骤。 如果只使用不带分层命名空间的 Blob 存储帐户作为数据存储，可跳过此部分。

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>为 Azure Data Lake Storage Gen2 帐户创建服务主体

若要创建服务主体，请查看[如何：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。

- 执行该文中[将应用程序分配给角色](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)部分中的步骤时，请确保将“存储 Blob 数据参与者”角色分配给服务主体。

- 执行本文[获取用于登录的值](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)部分中的步骤时，请将应用程序 ID 和客户端密码值另存到文本文件中。 很快就会需要这些值。

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>生成具有相应权限的已复制文件的列表

在本地 Hadoop 群集中，运行以下命令：

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

此命令会生成具有相应权限的已复制文件的列表。

> [!NOTE]
> 根据 HDFS 中的文件数，运行此命令可能需要很长时间。

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>生成标识列表，并将其映射到 Azure Active Directory (ADD) 标识

1. 下载 `copy-acls.py` 脚本。 请查看本文的[下载帮助程序脚本并设置边缘节点以运行它们](#download-helper-scripts)部分。

2. 运行以下命令，生成唯一标识的列表。

   ```bash

   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   此脚本会生成一个名为 `id_map.json` 的文件，其中包含需要映射到基于 ADD 的标识的标识。

3. 在文本编辑器中打开 `id_map.json` 文件。

4. 对于文件中显示的每个 JSON 对象，请更新 AAD 用户主体名称 (UPN) 或 ObjectId (OID) 的 `target` 属性，使其显示相应已映射的标识。 完成后，保存文件。 下一步需要用到该文件。

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>将权限应用于已复制的文件并应用标识映射

运行以下命令，将权限应用于复制到 Data Lake Storage Gen2 帐户的数据：

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

- 将 `<storage-account-name>` 占位符替换为存储帐户的名称。

- 请将 `<container-name>` 占位符替换为容器的名称。

- 将 `<application-id>` 和 `<client-secret>` 占位符替换为创建服务主体时收集的应用程序 ID 和客户端密码。

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>附录：跨多台 Data Box 设备拆分数据

将数据移到 Data Box 设备之前，需要下载一些帮助程序脚本，确保已整理数据来适应一台 Data Box 设备中，并排除所有不必要的文件。

<a id="download-helper-scripts"></a>

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>下载帮助程序脚本并设置边缘节点以运行它们

1. 在本地 Hadoop 群集的边缘节点或头节点中，运行以下命令：

   ```bash

   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   该命令会克隆包含帮助程序脚本的 GitHub 存储库。

2. 确保已在本地计算机上安装了 [jq](https://stedolan.github.io/jq/) 包。

   ```bash

   sudo apt-get install jq
   ```

3. 安装 python 包 [Requests](https://2.python-requests.org/en/master/)。

   ```bash

   pip install requests
   ```

4. 设置对所需脚本的执行权限。

   ```bash

   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>确保已整理数据以适应一台 Data Box 设备

如果数据大小超出了一台 Data Box 设备的大小，可将数据拆分为组，将其存储到多台 Data Box 设备。

如果数据未超出一台 Data Box 设备的大小，可继续到下一部分。

1. 借助提升的权限，按照上一部分中的指南运行已下载的 `generate-file-list` 脚本。

   命令参数的说明如下：

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. 将生成的文件列表复制到 HDFS，以便 [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) 作业可访问这些列表。

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>排除不必要的文件

你需要从 DisCp 作业中排除某些目录。 例如，排除包含使群集保持运行的状态信息的目录。

在计划启动 DistCp 作业的本地 Hadoop 群集中，创建一个文件来指定要排除的目录列表。

下面是一个示例：

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>后续步骤

了解如何将 Data Lake Storage Gen2 与 HDInsight 群集配合使用。 有关详细信息，请参阅[将 Azure Data Lake Storage Gen2 与 Azure HDInsight 群集配合使用](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)。
