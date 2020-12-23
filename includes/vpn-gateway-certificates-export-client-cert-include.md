---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 018cf621c65e86877a76c9861c999caf67f3b8cf
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553174"
---
生成客户端证书时，该证书会自动安装在用于生成它的计算机上。 如果想要在另一台客户端计算机上安装客户端证书，需要导出生成的客户端证书。

1. 若要导出客户端证书，请打开“管理用户证书”  。 生成的客户端证书默认位于“Certificates - Current User\Personal\Certificates”中。 右键单击要导出的客户端证书，单击“所有任务”  ，并单击“导出”  打开“证书导出向导”。 

   ![屏幕截图显示当前用户的 "证书" 窗口，其中选择了证书，并从 "所有任务" 中选择 "导出"。](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. 在“证书导出向导”中，单击“下一步”以继续。 

   ![屏幕截图显示 "证书导出向导" 欢迎消息。](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. 选择“是，导出私钥”  ，并单击“下一步”  。

   ![导出私钥](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. 在“导出文件格式”  页上，保留选择默认值。 请务必选中“包括证书路径中的所有证书(如果可能)”。  此外，此设置还会导出成功客户端身份验证所需的根证书信息。 否则，客户端身份验证会失败，因为客户端没有受信任的根证书。 然后单击“下一步”。 

   ![导出文件格式](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. 在“ **安全性** ”页上，必须保护私钥。 如果选择使用密码，请务必记下或牢记为此证书设置的密码。 然后单击“下一步”。 

   ![屏幕截图显示 "证书导出向导" 安全页，其中输入和确认密码并突出显示。](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. 在“要导出的文件”  中，“浏览”  到要将证书导出的目标位置。 在“文件名”  中，为证书文件命名。 然后单击“下一步”。 

   ![要导出的文件](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. 单击“完成”  导出证书。

   ![屏幕截图显示具有输入设置的 "证书导出向导"。](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)