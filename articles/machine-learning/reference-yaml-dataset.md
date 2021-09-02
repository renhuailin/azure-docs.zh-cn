---
title: CLI (v2) 数据集 YAML 架构
titleSuffix: Azure Machine Learning
description: CLI (v2) 数据集 YAML 架构的参考文档。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: lostmygithubaccount
ms.author: copeters
ms.date: 08/03/2021
ms.reviewer: laobri
ms.openlocfilehash: 5e22c9bed80b1e29cba490e8bbd42c44ab4fe453
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778650"
---
# <a name="cli-v2-dataset-yaml-schema"></a>CLI (v2) 数据集 YAML 架构

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="schema"></a>架构

可在 https://azuremlschemas.azureedge.net/latest/dataset.schema.json 中找到源 JSON 架构。 为方便起见，下面将以 JSON 和 YAML 格式提供架构。

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/azureml-examples-main/cli/.schemas/jsons/latest/dataset.schema.json":::

# <a name="yaml"></a>[YAML](#tab/yaml)

:::code language="yaml" source="~/azureml-examples-main/cli/.schemas/yamls/latest/dataset.schema.yml":::

---

## <a name="remarks"></a>备注

`az ml dataset` 命令可用于管理 Azure 机器学习数据集。

## <a name="next-steps"></a>后续步骤

- [安装并使用 CLI (v2)](how-to-configure-cli.md)
