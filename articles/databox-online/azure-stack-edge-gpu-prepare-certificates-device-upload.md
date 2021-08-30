---
title: 准备证书以上传到 Azure Stack Edge Pro GPU/Pro R/Mini R
description: 介绍如何准备证书以上传到 Azure Stack Edge Pro GPU/Pro R/Mini R 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: 2db11b9a1d159e292140901b50c2d7b659c88235
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364023"
---
# <a name="prepare-certificates-to-upload-on-your-azure-stack-edge-pro-gpu"></a>准备证书以上传到 Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何将证书转换为适当的格式，以便可以上传到 Azure Stack Edge 设备。 如果自带证书，通常需要执行此过程。

要详细了解如何创建这些证书，请参阅[使用 Azure PowerShell 创建证书](azure-stack-edge-gpu-create-certificates-powershell.md)。


## <a name="prepare-certificates"></a>准备证书

如果自带证书，则为设备创建的证书默认位于客户端的“个人存储”中。 这些证书需要在你的客户端上导出为合适的格式文件，然后才能上传到设备。

- 准备根证书：根证书必须导出为扩展名为 `.cer` 的 DER 格式。 有关详细步骤，请参阅将[将证书导出为 DER 格式](#export-certificates-as-der-format)。

- 准备终结点证书：终结点证书必须导出为包含私钥的 .pfx 文件。 有关详细步骤，请参阅将[将证书导出为包含私钥的 .pfx 文件](#export-certificates-as-pfx-format-with-private-key)。 


## <a name="export-certificates-as-der-format"></a>以 DER 格式导出证书

1. 运行 certlm.msc 以启动本地计算机证书存储。

1. 在“个人证书存储”中，选择根证书。 右键单击并选择“所有任务”->“导出...”

    ![导出证书 DER 1](media/azure-stack-edge-series-manage-certificates/export-cert-cer-1.png)

2. 证书向导将打开。 选择格式“DER 编码的二进制 x.509 (.cer)”。 选择“**下一步**”。

    ![导出证书 DER 2](media/azure-stack-edge-series-manage-certificates/export-cert-cer-2.png)

3. 浏览并选择要导出 .cer 格式文件的位置。

    ![导出证书 DER 3](media/azure-stack-edge-series-manage-certificates/export-cert-cer-3.png)

4. 选择“完成”。

    ![导出证书 DER 4](media/azure-stack-edge-series-manage-certificates/export-cert-cer-4.png)


## <a name="export-certificates-as-pfx-format-with-private-key"></a>将证书导出为包含私钥的 .pfx 格式

执行以下步骤，在 Windows 计算机上导出具有私钥的 SSL 证书。 

> [!IMPORTANT]
> 在用于创建证书的同一台计算机上执行以下步骤。 

1. 运行 certlm.msc 以启动本地计算机证书存储。

1. 双击“个人”文件夹，然后单击“证书”。

    ![导出证书 1](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-1.png)
 
2. 右键单击要备份的证书，然后选择“所有任务”>“导出...”

    ![导出证书 2](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-2.png)

3. 按照“证书导出向导”将证书备份到 .pfx 文件。

    ![导出证书 3](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-3.png)

4. 选择“是，导出私钥”。

    ![导出证书 4](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-4.png)

5. 选择“在证书路径中包括所有证书(如果可能)”、“导出所有扩展属性”和“启用证书隐私”。 

    > [!IMPORTANT]
    > 请不要选择“如果导出成功，请删除私钥选项”。

    ![导出证书 5](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-5.png)

6. 请输入你能记住的密码。 确认该密码。 密码可保护私钥。

    ![导出证书 6](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-6.png)

7. 选择在设置位置保存文件。

    ![导出证书 7](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-7.png)
  
8. 选择“完成”。

    ![导出证书 8](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-8.png)

9. 你会收到一条指示已成功导出的消息。 选择“确定”。

    ![导出证书 9](media/azure-stack-edge-series-manage-certificates/export-cert-pfx-9.png)

.pfx 文件备份现在保存到你选择的位置，并且已准备好进行移动或存储，以安全留存。


## <a name="next-steps"></a>后续步骤

了解如何[将证书上传到设备](azure-stack-edge-gpu-manage-certificates.md)。
