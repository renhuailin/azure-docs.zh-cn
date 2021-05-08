---
title: 渲染应用程序
description: 可以将任何渲染应用程序与 Azure Batch 配合使用。 不过，常见的预安装应用程序都提供了 Azure 市场 VM 映像。
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 7dee2806aafe34edee8dcb3dc3577def9c4c01da
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107987499"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>在 Batch 渲染 VM 映像上预安装的应用程序

可以将任何渲染应用程序与 Azure Batch 配合使用。 不过，常见的预安装应用程序都提供了 Azure 市场 VM 映像。

在适用的情况下，可以对预安装的渲染应用程序使用即用即付许可。 创建 Batch 池时，可以指定必需的应用程序，将按分钟对 VM 和应用程序计费。 [Azure Batch 定价页面](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)上列出了应用程序价格。

某些应用程序仅支持 Windows，但大多数应用程序在 Windows 和 Linux 上都受支持。

> [!IMPORTANT]
> 渲染 VM 映像和即用即付许可[已弃用，将于 2024 年 2 月 29 日停用](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/)。 若要使用 Batch 进行渲染，[应使用自定义 VM 映像和标准应用程序许可。](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

## <a name="applications-on-latest-centos-7-rendering-image"></a>最新 CentOS 7 渲染映像上的应用程序

以下列表适用于 CentOS 渲染映像版本 1.2.0。

* Autodesk Maya I/O 2020 更新 4.6
* Autodesk Arnold for Maya 2020（Arnold 版本 6.2.0.0）MtoA-4.2.0-2020
* Chaos Group V-Ray for Maya 2020（版本 5.00.21）
* Blender (2.80)
* AZ 10

## <a name="applications-on-latest-windows-server-rendering-image"></a>最新 Windows Server 渲染映像上的应用程序

以下列表适用于 Windows Server 渲染映像 1.5.0 版。

* Autodesk Maya I/O 2020 更新 4.4
* Autodesk 3ds Max I/O 2021 更新 3
* Autodesk Arnold for Maya 2020（Arnold 版本 6.1.0.1）MtoA-4.1.1.1-2020
* Autodesk Arnold for 3ds Max 2021（Arnold 版本 6.1.0.1）MAXtoA-4.2.2.20-2021
* Chaos Group V-Ray for Maya 2020（版本 5.00.21）
* Chaos Group V-Ray for 3ds Max 2021（版本 5.00.05）
* Blender (2.79)
* Blender (2.80)
* AZ 10

> [!IMPORTANT]
> 若要在 [Azure Batch 扩展模板](https://github.com/Azure/batch-extension-templates)之外运行 V-Ray 和 Maya，请在运行渲染之前启动 `vrayses.exe`。 若要在模板之外启动 vrayses.exe，可以使用以下命令 `%MAYA_2020%\vray\bin\vrayses.exe"`。
>
> 有关示例，请参阅 GitHub 上 [Maya 和 V-Ray 模板](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json)的启动任务。

## <a name="applications-on-previous-windows-server-rendering-images"></a>以前的 Windows Server 渲染映像上的应用程序

以下列表适用于 Windows Server 2016 版本 1.3.8 渲染映像。

* Autodesk Maya I/O 2017 更新 5（版本 17.4.5459）
* Autodesk Maya I/O 2018 更新 6（版本 18.4.0.7622）
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 更新 4（版本 20.4.0.4254）
* Autodesk 3ds Max I/O 2019 更新 1（版本 21.2.0.2219）
* Autodesk 3ds Max I/O 2020 更新 2
* Autodesk Arnold for Maya 2017（Arnold 版本 5.3.0.2）MtoA-3.2.0.2-2017
* Autodesk Arnold for Maya 2018（Arnold 版本 5.3.0.2）MtoA-3.2.0.2-2018
* Autodesk Arnold for Maya 2019（Arnold 版本 5.3.0.2）MtoA-3.2.0.2-2019
* Autodesk Arnold for 3ds Max 2018（Arnold 版本 5.3.0.2）（版本 1.2.926）
* Autodesk Arnold for 3ds Max 2019（Arnold 版本 5.3.0.2）（版本 1.2.926）
* Autodesk Arnold for 3ds Max 2020（Arnold 版本 5.3.0.2）（版本 1.2.926）
* Chaos Group V-Ray for Maya 2017（版本 4.12.01）
* Chaos Group V-Ray for Maya 2018（版本 4.12.01）
* Chaos Group V-Ray for Maya 2019（版本 4.04.03）
* Chaos Group V-Ray for 3ds Max 2018（版本 4.20.01）
* Chaos Group V-Ray for 3ds Max 2019（版本 4.20.01）
* Chaos Group V-Ray for 3ds Max 2020（版本 4.20.01）
* Blender (2.79)
* Blender (2.80)
* AZ 10

以下列表适用于 Windows Server 2016 版本 1.3.7 渲染映像。

* Autodesk Maya I/O 2017 更新 5（版本 17.4.5459）
* Autodesk Maya I/O 2018 更新 4（版本 18.4.0.7622）
* Autodesk 3ds Max I/O 2019 更新 1（版本 21.2.0.2219）
* Autodesk 3ds Max I/O 2018 更新 4（版本 20.4.0.4254）
* Autodesk Arnold for Maya 2017（Arnold 版本 5.2.0.1）MtoA-3.1.0.1-2017
* Autodesk Arnold for Maya 2018（Arnold 版本 5.2.0.1）MtoA-3.1.0.1-2018
* Autodesk Arnold for 3ds Max 2018（Arnold 版本 5.0.2.4）（版本 1.2.926）
* Autodesk Arnold for 3ds Max 2019（Arnold 版本 5.0.2.4）（版本 1.2.926）
* Chaos Group V-Ray for Maya 2018（版本 3.52.03）
* Chaos Group V-Ray for 3ds Max 2018（版本 3.60.02）
* Chaos Group V-Ray for Maya 2019（版本 3.52.03）
* Chaos Group V-Ray for 3ds Max 2019（版本 4.10.01）
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray for 3ds Max 2019（版本 4.10.01）引入了对 V-ray 的重大更改。 若要使用之前的版本（版本 3.60.02），请使用 Windows Server 2016 版本 1.3.2 渲染节点。

## <a name="applications-on-previous-centos-rendering-images"></a>以前的 CentOS 渲染映像上的应用程序

以下列表适用于 CentOS 7.6 版本 1.1.6 渲染映像。

* Autodesk Maya I/O 2017 更新 5 (cut 201708032230)
* Autodesk Maya I/O 2018 更新 2 (cut 201711281015)
* Autodesk Maya I/O 2019 更新 1
* Autodesk Arnold for Maya 2017（Arnold 版本 5.3.1.1）MtoA-3.2.1.1-2017
* Autodesk Arnold for Maya 2018（Arnold 版本 5.3.1.1）MtoA-3.2.1.1-2018
* Autodesk Arnold for Maya 2019（Arnold 版本 5.3.1.1）MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017（版本 3.60.04）
* Chaos Group V-Ray for Maya 2018（版本 3.60.04）
* Blender (2.68)
* Blender (2.8)

## <a name="next-steps"></a>后续步骤

若要使用渲染 VM 映像，需要在创建池时在池配置中指定它们；请参阅 [Batch 池渲染功能](./batch-rendering-functionality.md)。
