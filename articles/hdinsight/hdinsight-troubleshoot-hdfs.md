---
title: Azure HDInsight 中的 HDFS 故障排除
description: 获取有关使用 HDFS 和 Azure HDInsight 的常见问题答案。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: ce9ca7d247498d785b98dcef329a4132b7f07ac8
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291054"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>使用 Azure HDInsight 对 Apache Hadoop HDFS 进行故障排除

了解使用 Hadoop 分布式文件系统 (HDFS) 时的常见问题和解决方法。 有关完整的命令列表，请参阅 [HDFS 命令指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html)和[文件系统 Shell 指南](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)。

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>如何从群集内访问本地 HDFS？

### <a name="issue"></a>问题

从 HDInsight 群集内通过命令行和应用程序代码（而不是使用 Azure Blob 存储或 Azure Data Lake Storage）访问本地 HDFS。

### <a name="resolution-steps"></a>解决步骤

1. 在命令提示符下，按原义使用 `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...`，如以下命令中所示：

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. 从源代码按原义使用 URI `hdfs://mycluster/`，如以下示例应用程序中所示：

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. 使用以下命令在 HDInsight 群集上运行已编译的 .jar 文件（例如，名为 `java-unit-tests-1.0.jar` 的文件）：

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>写入 blob 时的存储异常

### <a name="issue"></a>问题

使用 `hadoop` 或 `hdfs dfs` 命令在 HBase 群集上编写大于或等于 ~12 GB 的文件时，可能会遇到以下错误：

```error
ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
copyFromLocal: java.io.IOException
        at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
        at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
        at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
        at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
        ... 7 more
```

### <a name="cause"></a>原因

写入 Azure 存储时，HDInsight 群集上的 HBase 的块大小将默认为 256 KB。 尽管这对 HBase API 或 REST API 来说可良好运行，但使用 `hadoop` 或 `hdfs dfs` 命令行实用工具时则会导致错误。

### <a name="resolution"></a>解决方法

使用 `fs.azure.write.request.size` 指定更大的块大小。 可以使用 `-D` 参数基于使用情况执行此修改。 以下命令是将此参数用于 `hadoop` 命令的示例：

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

还可使用 Apache Ambari 以全局方式增加 `fs.azure.write.request.size` 的值。 可以使用以下步骤在 Ambari Web UI 中更改该值：

1. 在浏览器中，转到群集的 Ambari Web UI。 URL 为 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是群集的名称。 出现提示时，输入群集的管理员名称和密码。
2. 在屏幕左侧选择“HDFS”，然后选择“配置”选项卡 。
3. 在“筛选...”字段中输入 `fs.azure.write.request.size`。
4. 将值从 262144 (256 KB) 更改为新的值。 例如，4194304 (4 MB)。

    :::image type="content" source="./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png" alt-text="通过 Ambari Web UI 更改值的图像" border="false":::

有关如何使用 Ambari 的详细信息，请参阅[使用 Apache Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)。

## <a name="du"></a>du

[`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du) 命令显示给定目录中包含的文件和目录的大小或文件的长度（如果它只是一个文件）。

`-s` 选项生成所显示文件长度的聚合汇总。  
`-h` 选项设置文件大小的格式。

示例：

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>rm

[-rm](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) 命令删除指定为参数的文件。

示例：

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](includes/hdinsight-troubleshooting-next-steps.md)]