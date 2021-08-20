---
title: 包含手写数字的 MNIST 数据库
titleSuffix: Azure Open Datasets
description: 了解如何在 Azure 开放数据集中使用包含手写数字的 MNIST 数据库数据集。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1935c8702002fe00fd99de7d6b095d95ecce8475
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038555"
---
# <a name="the-mnist-database-of-handwritten-digits"></a>包含手写数字的 MNIST 数据库

包含手写数字的 MNIST 数据库有一个 60,000 示例的训练集，还有一个 10,000 示例的测试集。 这些数字已在大小方面规范化，在固定大小的图像中居中。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

此数据集来源于 [MNIST 手写数字数据库](http://yann.lecun.com/exdb/mnist/)。 它是[美国国家标准与技术研究所](https://www.nist.gov/)发布的大型 [NIST 手写体及字符数据库](https://www.nist.gov/srd/nist-special-database-19)的子集。


## <a name="storage-location"></a>存储位置

- Blob 帐户：azureopendatastorage
- 容器名：mnist

在容器中可直接使用以下四个文件：
  - train-images-idx3-ubyte.gz：训练集图像（9912422 字节）
  - train-labels-idx1-ubyte.gz：训练集标签（28881 字节）
  - t10k-images-idx3-ubyte.gz：测试集图像（1648877 字节）
  - t10k-labels-idx1-ubyte.gz：测试集标签（4542 字节）

## <a name="data-access"></a>数据访问

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=mnist -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=mnist)。

### <a name="load-mnist-into-a-data-frame-using-azure-machine-learning-tabular-datasets"></a>使用 Azure 机器学习表格数据集将 MNIST 加载到数据帧中。 
有关 Azure 机器学习数据集的详细信息，请参阅[创建 Azure 机器学习数据集](../machine-learning/how-to-create-register-datasets.md)。 

#### <a name="get-complete-dataset-into-a-data-frame"></a>获取数据帧的完整数据集

```python
from azureml.opendatasets import MNIST

mnist = MNIST.get_tabular_dataset()
mnist_df = mnist.to_pandas_dataframe()
mnist_df.info()
```

#### <a name="get-train-and-test-data-frames"></a>获取训练和测试数据帧

```python
mnist_train = MNIST.get_tabular_dataset(dataset_filter='train')
mnist_train_df = mnist_train.to_pandas_dataframe()
X_train = mnist_train_df.drop("label", axis=1).astype(int).values/255.0
y_train = mnist_train_df.filter(items=["label"]).astype(int).values

mnist_test = MNIST.get_tabular_dataset(dataset_filter='test')
mnist_test_df = mnist_test.to_pandas_dataframe()
X_test = mnist_test_df.drop("label", axis=1).astype(int).values/255.0
y_test = mnist_test_df.filter(items=["label"]).astype(int).values
```

#### <a name="plot-some-images-of-the-digits"></a>绘制一些数字图像 

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

### <a name="download-or-mount-mnist-raw-files-azure-machine-learning-file-datasets"></a>下载或装载 MNIST 原始文件 Azure 机器学习文件数据集。 
这仅适用于基于 Linux 的计算。 有关 Azure 机器学习数据集的详细信息，请参阅[创建 Azure 机器学习数据集](../machine-learning/how-to-create-register-datasets.md)。 

```python
mnist_file = MNIST.get_file_dataset()
mnist_file
```

```python
mnist_file.to_path()
```

#### <a name="download-files-to-local-storage"></a>将文件下载到本地存储

```python
import os
import tempfile

data_folder = tempfile.mkdtemp()
data_paths = mnist_file.download(data_folder, overwrite=True)
data_paths
```

#### <a name="mount-files-useful-when-training-job-will-run-on-a-remote-compute"></a>装载文件。 训练作业将在远程计算上运行时非常有用。

```python
import gzip
import struct
import pandas as pd
import numpy as np

# load compressed MNIST gz files and return pandas dataframe of numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        gz.read(4)
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return pd.DataFrame(res)
```

```python
import sys
mount_point = tempfile.mkdtemp()
print(mount_point)
print(os.path.exists(mount_point))

if sys.platform == 'linux':
  print("start mounting....")
  with mnist_file.mount(mount_point):
    print("list dir...")
    print(os.listdir(mount_point))
    print("get the dataframe info of mounted data...")
    train_images_df = load_data(next(path for path in data_paths if path.endswith("train-images-idx3-ubyte.gz")))
    print(train_images_df.info())
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=mnist -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=mnist)。

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
```

```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/t10k-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('https://azureopendatastorage.blob.core.windows.net/mnist/t10k-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-labels.gz'))
```

```python
import gzip
import struct

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res
```

```python
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(
    data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(
    data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

<!-- nbend -->

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=mnist -->

> [!TIP]
> [改为下载笔记本](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=azureml-opendatasets&registryId=mnist)。

### <a name="load-mnist-into-a-data-frame-using-azure-machine-learning-tabular-datasets"></a>使用 Azure 机器学习表格数据集将 MNIST 加载到数据帧中。 
有关 Azure 机器学习数据集的详细信息，请参阅[创建 Azure 机器学习数据集](../machine-learning/how-to-create-register-datasets.md)。 

#### <a name="get-complete-dataset-into-a-data-frame"></a>获取数据帧的完整数据集

```python
# This is a package in preview.
from azureml.opendatasets import MNIST

mnist = MNIST.get_tabular_dataset()
mnist_df = mnist.to_spark_dataframe()
```

```python
display(mnist_df.limit(5))
```

### <a name="download-or-mount-mnist-raw-files-azure-machine-learning-file-datasets"></a>下载或装载 MNIST 原始文件 Azure 机器学习文件数据集。 
这仅适用于基于 Linux 的计算。 有关 Azure 机器学习数据集的详细信息，请参阅[创建 Azure 机器学习数据集](../machine-learning/how-to-create-register-datasets.md)。 

```python
mnist_file = MNIST.get_file_dataset()
mnist_file
```

```python
mnist_file.to_path()
```

#### <a name="download-files-to-local-storage"></a>将文件下载到本地存储

```python
import os
import tempfile

mount_point = tempfile.mkdtemp()
mnist_file.download(mount_point, overwrite=True)
```

#### <a name="mount-files-useful-when-training-job-will-run-on-a-remote-compute"></a>装载文件。 训练作业将在远程计算上运行时非常有用。

```python
import gzip
import struct
import pandas as pd
import numpy as np

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        gz.read(4)
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return pd.DataFrame(res)
```

```python
import sys
mount_point = tempfile.mkdtemp()
print(mount_point)
print(os.path.exists(mount_point))
print(os.listdir(mount_point))

if sys.platform == 'linux':
  print("start mounting....")
  with mnist_file.mount(mount_point):
    print(context.mount_point )
    print(os.listdir(mount_point))  
    train_images_df = load_data(os.path.join(mount_point, 'train-images-idx3-ubyte.gz'))
    print(train_images_df.info())
```

<!-- nbend -->

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

没有适用于此平台/包组合的示例。

---

## <a name="next-steps"></a>后续步骤

查看[开放数据集目录](dataset-catalog.md)中的其余数据集。