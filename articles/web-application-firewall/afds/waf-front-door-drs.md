---
title: Azure Front Door 上的 Azure Web 应用程序防火墙 DRS 规则组和规则
description: 本文提供有关 Web 应用程序防火墙 DRS 规则组和规则的信息。
ms.service: web-application-firewall
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 95839937f2356f74d29499bb45e7cd0e1159b02c
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546342"
---
# <a name="web-application-firewall-drs-rule-groups-and-rules"></a>Web 应用程序防火墙 DRS 规则组和规则

Azure Front Door Web 应用程序防火墙 (WAF) 可保护 Web 应用程序免受常见漏洞和攻击影响。 Azure 托管的规则集可轻松针对一组常见的安全威胁来部署保护。 由于此类规则集由 Azure 托管，因此这些规则会根据需要进行更新以预防新的攻击签名。


## <a name="default-rule-sets"></a>默认规则集

Azure 托管的默认规则集包含针对以下威胁类别的规则：

- 跨站点脚本
- Java 攻击
- 本地文件包含
- PHP 注入攻击
- 远程命令执行
- 远程文件包含
- 会话固定
- SQL 注入保护
- 协议攻击者

将新的攻击签名添加到规则集时，默认规则集的版本号将递增。
默认规则集在 WAF 策略的检测模式下默认启用。 可以禁用或启用默认规则集内的各个规则以满足应用程序要求。 还可以根据规则设置特定操作（允许/阻止/重定向/记录）。

有时你可能需要忽略 WAF 评估中的某些请求属性。 一个常见的例子是用于身份验证的 Active Directory 插入令牌。 可以为托管规则、规则组或整个规则集配置排除列表。  

默认操作为“阻止”。 此外，如果想要绕过默认规则集中的任何预配置规则，可以在同一 WAF 策略中配置自定义规则。

在评估默认规则集中的规则之前，自定义规则始终适用。 如果请求与某个自定义规则相匹配，将应用相应的规则操作。 请求将被阻止，或通过后端传递。 不会处理任何其他自定义规则或默认规则集中的规则。 还可以从 WAF 策略中删除默认规则集。

### <a name="microsoft-threat-intelligence-collection-rules"></a>Microsoft 威胁情报收集规则

Microsoft 威胁情报收集规则由 Microsoft 情报团队合作编写，以提供更高的覆盖范围、针对特定漏洞的补丁，并更好地减少误报。

### <a name="anomaly-scoring-mode"></a>异常评分模式

OWASP 有两种模式，用于决定是否阻止流量：传统模式和异常评分模式。

在传统模式下，与任何规则匹配的流量被视为独立于任何其他规则匹配。 此模式易于理解。 但其局限在于，未提供相关信息以表明与特定请求匹配的规则数。 因此，引入了异常评分模式。 这是 OWASP 3.x 的默认模式。

在异常评分模式下，当防火墙处于阻止模式时，不会立即阻止与任何规则匹配的流量。 规则具有一定的严重性：“严重”、“错误”、“警告”或“通知”。 此严重性会影响请求的数值，该数值称为异常分数。 例如，一个“警告”规则匹配对应的分数为 3。 一个“严重”规则匹配对应的分数为 5。

|严重性  |值  |
|---------|---------|
|严重     |5|
|错误        |4|
|警告      |3|
|通知       |2|

异常分数的阈值为 5，用于阻止流量。 因此，单个关键规则匹配足以让 WAF 阻止请求，即使在阻止模式下也是如此。 但一个“警告”规则匹配仅使异常分数增加 3，而这并不足以阻止流量。

> [!NOTE]
> 正文检查仅适用于 DRS 2.0

### <a name="drs-20"></a>DRS 2.0

DRS 2.0 包含下表中所示的 17 个规则组。 每个组包含多个可以禁用的规则。

> [!NOTE]
> DRS 2.0 仅在 Azure Front Door 高级版中可用。

|规则组|说明|
|---|---|
|**[常规](#general-20)**|常规组|
|**[METHOD-ENFORCEMENT](#drs911-20)**|锁定方法（PUT、PATCH）|
|**[PROTOCOL-ENFORCEMENT](#drs920-20)**|防范协议和编码问题|
|**[PROTOCOL-ATTACK](#drs921-20)**|防范标头注入、请求走私和响应拆分|
|**[APPLICATION-ATTACK-LFI](#drs930-20)**|防范文件和路径攻击|
|**[APPLICATION-ATTACK-RFI](#drs931-20)**|防范远程文件包含 (RFI) 攻击|
|**[APPLICATION-ATTACK-RCE](#drs932-20)**|防范远程代码执行攻击|
|**[APPLICATION-ATTACK-PHP](#drs933-20)**|防范 PHP 注入攻击|
|**[APPLICATION-ATTACK-NodeJS](#drs934-20)**|防范 Node JS 攻击|
|**[APPLICATION-ATTACK-XSS](#drs941-20)**|防范跨站点脚本攻击|
|**[APPLICATION-ATTACK-SQLI](#drs942-20)**|防范 SQL 注入攻击|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-20)**|防范会话固定攻击|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-20)**|防范 JAVA 攻击|
|**[MS-ThreatIntel-WebShells](#drs9905-20)**|防范 Web shell 攻击|
|**[MS-ThreatIntel-AppSec](#drs9903-20)**|防范 AppSec 攻击|
|**[MS-ThreatIntel-SQLI](#drs99031-20)**|防范 SQLI 攻击|
|**[MS-ThreatIntel-CVEs](#drs99001-20)**|防范 CVE 攻击|

### <a name="drs-11"></a>DRS 1.1
|规则组|说明|
|---|---|
|**[PROTOCOL-ATTACK](#drs921-11)**|防范标头注入、请求走私和响应拆分|
|**[APPLICATION-ATTACK-LFI](#drs930-11)**|防范文件和路径攻击|
|**[APPLICATION-ATTACK-RFI](#drs931-11)**|防范远程文件包含攻击|
|**[APPLICATION-ATTACK-RCE](#drs932-11)**|防止远程命令执行|
|**[APPLICATION-ATTACK-PHP](#drs933-11)**|防范 PHP 注入攻击|
|**[APPLICATION-ATTACK-XSS](#drs941-11)**|防范跨站点脚本攻击|
|**[APPLICATION-ATTACK-SQLI](#drs942-11)**|防范 SQL 注入攻击|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-11)**|防范会话固定攻击|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-11)**|防范 JAVA 攻击|
|**[MS-ThreatIntel-WebShells](#drs9905-11)**|防范 Web shell 攻击|
|**[MS-ThreatIntel-AppSec](#drs9903-11)**|防范 AppSec 攻击|
|**[MS-ThreatIntel-SQLI](#drs99031-11)**|防范 SQLI 攻击|
|**[MS-ThreatIntel-CVEs](#drs99001-11)**|防范 CVE 攻击|

### <a name="drs-10"></a>DRS 1.0

|规则组|说明|
|---|---|
|**[PROTOCOL-ATTACK](#drs921-10)**|防范标头注入、请求走私和响应拆分|
|**[APPLICATION-ATTACK-LFI](#drs930-10)**|防范文件和路径攻击|
|**[APPLICATION-ATTACK-RFI](#drs931-10)**|防范远程文件包含攻击|
|**[APPLICATION-ATTACK-RCE](#drs932-10)**|防止远程命令执行|
|**[APPLICATION-ATTACK-PHP](#drs933-10)**|防范 PHP 注入攻击|
|**[APPLICATION-ATTACK-SQLI](#drs942-10)**|防范 SQL 注入攻击|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-10)**|防范会话固定攻击|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-10)**|防范 JAVA 攻击|



### <a name="bot-rules"></a>机器人规则

|规则组|说明|
|---|---|
|**[BadBots](#bot100)**|防范恶意机器人|
|**[GoodBots](#bot200)**|识别善意机器人|
|**[UnknownBots](#bot300)**|标识未知机器人|



在 Azure Front Door 上使用 Web 应用程序防火墙时可以使用以下规则组和规则。

# <a name="drs-20"></a>[DRS 2.0](#tab/drs20)

## <a name="20-rule-sets"></a><a name="drs20"></a> 2.0 规则集

### <a name="general"></a><a name="general-20"></a>常规
|RuleId|说明|
|---|---|
|200002|未能分析请求正文。|
|200003|多部分请求正文无法通过严格的验证|


### <a name="method-enforcement"></a><a name="drs911-20"></a> METHOD ENFORCEMENT
|RuleId|说明|
|---|---|
|911100|方法不受策略允许|

### <a name="protocol-enforcement"></a><a name="drs920-20"></a> PROTOCOL-ENFORCEMENT
|RuleId|说明|
|---|---|
|920100|无效的 HTTP 请求行|
|920120|尝试了多部分/表单数据绕过|
|920121|尝试了多部分/表单数据绕过|
|920160|Content-Length HTTP 标头不是数字。|
|920170|包含正文内容的 GET 或 HEAD 请求。|
|920171|包含 Transfer-Encoding 的 GET 或 HEAD 请求。|
|920180|POST 请求缺少 Content-Length 标头。|
|920190|范围：最后一个字节值无效。|
|920200|范围：字段太多（6 个或以上）|
|920201|范围：pdf 请求的字段太多（35 个或以上）|
|920210|找到了多个/有冲突的连接标头数据。|
|920220|URL 编码滥用攻击尝试|
|920230|检测到多个 URL 编码|
|920240|URL 编码滥用攻击尝试|
|920260|Unicode 全角/半角滥用攻击企图|
|920270|请求中的字符无效（null 字符）|
|920271|请求中的字符无效（不可列显的字符）|
|920280|请求缺少 Host 标头|
|920290|Host 标头为空|
|920300|请求缺少 Accept 标头|
|920310|请求包含空的 Accept 标头|
|920311|请求包含空的 Accept 标头|
|920320|缺少用户代理标头|
|920330|用户代理标头为空|
|920340|请求包含内容但缺少 Content-Type 标头|
|920341|请求包含内容，但需要 Content-Type 标头|
|920350|Host 标头是数字 IP 地址|
|920420|请求内容类型不受策略允许|
|920430|HTTP 协议版本不受策略允许|
|920440|策略限制了 URL 文件扩展名|
|920450|策略限制了 HTTP 标头|
|920470|Content-Type 标头非法|
|920480|请求内容类型字符集不受策略允许|

### <a name="protocol-attack"></a><a name="drs921-20"></a> PROTOCOL-ATTACK

|RuleId|说明|
|---|---|
|921110|HTTP 请求走私攻击|
|921120|HTTP 响应拆分攻击|
|921130|HTTP 响应拆分攻击|
|921140|通过标头展开的 HTTP 标头注入攻击|
|921150|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF）|
|921151|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF）|
|921160|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF 和标头名称）|

### <a name="lfi---local-file-inclusion"></a><a name="drs930-20"></a> LFI - 本地文件包含
|RuleId|说明|
|---|---|
|930100|路径遍历攻击 (/../)|
|930110|路径遍历攻击 (/../)|
|930120|OS 文件访问企图|
|930130|受限文件访问企图|

### <a name="rfi---remote-file-inclusion"></a><a name="drs931-20"></a> RFI - 远程文件包含
|RuleId|说明|
|---|---|
|931100|可能的远程文件包含 (RFI) 攻击：使用 IP 地址的 URL 参数|
|931110|可能的远程文件包含 (RFI) 攻击：对 URL 有效负载使用常见 RFI 漏洞参数名|
|931120|可能的远程文件包含 (RFI) 攻击：在 URL 有效负载中使用尾随问号 (?)|
|931130|可能的远程文件包含 (RFI) 攻击：域外引用/链接|

### <a name="rce---remote-command-execution"></a><a name="drs932-20"></a> RCE - 远程命令执行
|RuleId|说明|
|---|---|
|932100|远程命令执行：Unix 命令注入|
|932105|远程命令执行：Unix 命令注入|
|932110|远程命令执行：Windows 命令注入|
|932115|远程命令执行：Windows 命令注入|
|932120|远程命令执行：找到 Windows PowerShell 命令|
|932130|远程命令执行：找到 Unix Shell 表达式|
|932140|远程命令执行：找到 Windows FOR/IF 命令|
|932150|远程命令执行：直接 Unix 命令执行|
|932160|远程命令执行：找到 Unix Shell 代码|
|932170|远程命令执行：Shellshock (CVE-2014-6271)|
|932171|远程命令执行：Shellshock (CVE-2014-6271)|
|932180|受限文件上传企图|

### <a name="php-attacks"></a><a name="drs933-20"></a> PHP 攻击
|RuleId|说明|
|---|---|
|933100|PHP 注入攻击：找到开始/结束标记|
|933110|PHP 注入攻击：找到 PHP 脚本文件上传|
|933120|PHP 注入攻击：找到配置指令|
|933130|PHP 注入攻击：找到变量|
|933131|PHP 注入攻击：找到变量|
|933140|PHP 注入攻击：找到 I/O 流|
|933150|PHP 注入攻击：找到高风险的 PHP 函数名称|
|933151|PHP 注入攻击：找到中等风险的 PHP 函数名称|
|933160|PHP 注入攻击：找到高风险的 PHP 函数调用|
|933161|PHP 注入攻击：找到低值 PHP 函数调用|
|933170|PHP 注入攻击：序列化对象注入|
|933180|PHP 注入攻击：找到可变函数调用|
|933200|PHP 注入攻击：检测到包装器方案|
|933210|PHP 注入攻击：找到可变函数调用|

### <a name="node-js-attacks"></a><a name="drs934-20"></a> Node JS 攻击
|RuleId|说明|
|---|---|
|934100|Node.js 注入攻击|

### <a name="xss---cross-site-scripting"></a><a name="drs941-20"></a> XSS - 跨站脚本
|RuleId|说明|
|---|---|
|941100|检测到通过 libinjection 展开的 XSS 攻击|
|941101|检测到通过 libinjection 展开的 XSS 攻击。|
|941110|XSS 筛选器 - 类别 1：脚本标记向量|
|941120|XSS 筛选器 - 类别 2：事件处理程序向量|
|941130|XSS 筛选器 - 类别 3：属性向量|
|941140|XSS 筛选器 - 类别 4：Javascript URI 向量|
|941150|XSS 筛选器 - 类别 5：不允许的 HTML 属性|
|941160|NoScript XSS InjectionChecker：HTML 注入|
|941170|NoScript XSS InjectionChecker：属性注入|
|941180|节点验证器方块列表关键字|
|941190|使用样式表的 XSS|
|941200|使用 VML 帧的 XSS|
|941210|使用经过模糊处理的 Javascript 的 XSS|
|941220|使用经过模糊处理的 VB Script 的 XSS|
|941230|使用“embed”标记的 XSS|
|941240|使用“import”或“implementation”属性的 XSS|
|941250|IE XSS 筛选器 - 检测到攻击。|
|941260|使用“meta”标记的 XSS|
|941270|使用“link”href 的 XSS|
|941280|使用“base”标记的 XSS|
|941290|使用“applet”标记的 XSS|
|941300|使用“object”标记的 XSS|
|941310|US-ASCII 格式错误编码 XSS 筛选器 - 检测到攻击。|
|941320|检测到可能的 XSS 攻击 - HTML 标记处理程序|
|941330|IE XSS 筛选器 - 检测到攻击。|
|941340|IE XSS 筛选器 - 检测到攻击。|
|941350|UTF-7 编码 IE XSS - 检测到攻击。|
|941360|检测到 JavaScript 混淆。|
|941370|找到 JavaScript 全局变量|
|941380|检测到 AngularJS 客户端模板注入|

>[!NOTE]
> 本文包含对术语“黑名单”的引用，Microsoft 不再使用该术语。 在从软件中删除该术语后，我们会将其从本文中删除。


### <a name="sqli---sql-injection"></a><a name="drs942-20"></a> SQLI - SQL 注入
|RuleId|说明|
|---|---|
|942100|检测到通过 libinjection 展开的 SQL 注入攻击|
|942110|SQL 注入攻击：检测到常见注入测试|
|942120|SQL 注入攻击：检测到 SQL 运算符|
|942130|SQL 注入攻击：检测到 SQL 同义反复。|
|942140|SQL 注入攻击：检测到常用 DB 名称|
|942150|SQL 注入攻击|
|942160|检测到使用 sleep() 或 benchmark() 的盲注 sqli 测试。|
|942170|检测到包含条件查询的 SQL 基准和休眠注入企图|
|942180|检测到基本 SQL 身份验证绕过尝试 1/3|
|942190|检测到 MSSQL 代码执行和信息收集尝试|
|942200|检测到 MySQL 注释/空间经过模糊处理的注入和反引号终止|
|942210|检测链式 SQL 注入尝试次数 1/2|
|942220|查找整数溢出攻击，这些攻击来自 skipfish，但 3.0.00738585072007e-308 是“幻数”故障|
|942230|检测到条件 SQL 注入企图|
|942240|检测 MySQL 字符集开关和 MSSQL DoS 尝试|
|942250|检测 MATCH AGAINST、MERGE 和 EXECUTE IMMEDIATE 注入|
|942260|检测到基本 SQL 身份验证绕过尝试 2/3|
|942270|正在查找基本 sql 注入。 针对 mysql、oracle 和其他系统的常见攻击字符串。|
|942280|检测 Postgres pg_sleep 注入、waitfor 延迟攻击和数据库关闭尝试|
|942290|查找基本 MongoDB SQL 注入企图|
|942300|检测到 MySQL 注释、条件和 ch(a)r 注入|
|942310|检测链式 SQL 注入尝试次数 2/2|
|942320|检测 MySQL 和 PostgreSQL 存储过程/函数注入|
|942330|检测到经典 SQL 注入探测 1/2|
|942340|检测到基本 SQL 身份验证绕过尝试 3/3|
|942350|检测 MySQL UDF 注入和其他数据/结构操作企图|
|942360|检测到连接的基本 SQL 注入和 SQLLFI 尝试|
|942361|检测基于关键字 alter 或 union 的基本 SQL 注入|
|942370|检测到经典 SQL 注入探测 2/2|
|942380|SQL 注入攻击|
|942390|SQL 注入攻击|
|942400|SQL 注入攻击|
|942410|SQL 注入攻击|
|942430|受限 SQL 字符异常情况检测 (args)：已超出特殊字符数 (12)|
|942440|检测到 SQL 注释序列。|
|942450|识别到 SQL 十六进制编码|
|942460|元字符异常检测警报 - 重复的非单词字符|
|942470|SQL 注入攻击|
|942480|SQL 注入攻击|
|942500|检测到 MySQL 内联注释。|
|942510|检测到引号或反引号尝试 SQLi 绕过。|


### <a name="session-fixation"></a><a name="drs943-20"></a> SESSION-FIXATION
|RuleId|说明|
|---|---|
|943100|可能的会话固定攻击：在 HTML 中设置 Cookie 值|
|943110|可能的会话固定攻击：包含域外引用方的 SessionID 参数名称|
|943120|可能的会话固定攻击：不包含引用方的 SessionID 参数名称|

### <a name="java-attacks"></a><a name="drs944-20"></a> Java 攻击
|RuleId|说明|
|---|---|
|944100|远程命令执行：Apache Struts、Oracle WebLogic|
|944110|检测潜在的有效负载执行|
|944120|可能的有效负载执行和远程命令执行|
|944130|可疑的 Java 类|
|944200|利用 Java 反序列化 Apache Commons|
|944210|可能使用 Java 序列化|
|944240|远程命令执行：Java 序列化|
|944250|远程命令执行：检测到可疑的 Java 方法|

### <a name="ms-threatintel-webshells"></a><a name="drs9905-20"></a> MS-ThreatIntel-WebShells
|RuleId|说明|
|---|---|
|99005002|Web Shell 交互尝试 (POST)|
|99005003|Web Shell 上传尝试 (POST) - CHOPPER PHP|
|99005004|Web Shell 上传尝试 (POST) - CHOPPER ASPX|

### <a name="ms-threatintel-appsec"></a><a name="drs9903-20"></a> MS-ThreatIntel-AppSec
|RuleId|说明|
|---|---|
|99030001|标头中的路径遍历规避 (/.././../)|
|99030002|请求正文中的路径遍历规避 (/.././../)|

### <a name="ms-threatintel-sqli"></a><a name="drs99031-20"></a> MS-ThreatIntel-SQLI
|RuleId|说明|
|---|---|
|99031001|SQL 注入攻击：检测到常见注入测试|
|99031002|检测到 SQL 注释序列。|

### <a name="ms-threatintel-cves"></a><a name="drs99001-20"></a> MS-ThreatIntel-CVEs
|RuleId|说明|
|---|---|
|99001001|使用已知凭据尝试利用 F5 tmui (CVE-2020-5902) REST API|

# <a name="drs-11"></a>[DRS 1.1](#tab/drs11)

## <a name="11-rule-sets"></a><a name="drs11"></a> 1.1 规则集

### <a name="protocol-attack"></a><a name="drs921-11"></a> PROTOCOL-ATTACK
|RuleId|说明|
|---|---|
|921110|HTTP 请求走私攻击|
|921120|HTTP 响应拆分攻击|
|921130|HTTP 响应拆分攻击|
|921140|通过标头展开的 HTTP 标头注入攻击|
|921150|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF）|
|921151|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF）|
|921160|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF 和标头名称）|

### <a name="lfi---local-file-inclusion"></a><a name="drs930-11"></a> LFI - 本地文件包含
|RuleId|说明|
|---|---|
|930100|路径遍历攻击 (/../)|
|930110|路径遍历攻击 (/../)|
|930120|OS 文件访问企图|
|930130|受限文件访问企图|

### <a name="rfi---remote-file-inclusion"></a><a name="drs931-11"></a> RFI - 远程文件包含
|RuleId|说明|
|---|---|
|931100|可能的远程文件包含 (RFI) 攻击：使用 IP 地址的 URL 参数|
|931110|可能的远程文件包含 (RFI) 攻击：对 URL 有效负载使用常见 RFI 漏洞参数名|
|931120|可能的远程文件包含 (RFI) 攻击：在 URL 有效负载中使用尾随问号 (?)|
|931130|可能的远程文件包含 (RFI) 攻击：域外引用/链接|

### <a name="rce---remote-command-execution"></a><a name="drs932-11"></a> RCE - 远程命令执行
|RuleId|说明|
|---|---|
|932100|远程命令执行：Unix 命令注入|
|932105|远程命令执行：Unix 命令注入|
|932110|远程命令执行：Windows 命令注入|
|932115|远程命令执行：Windows 命令注入|
|931120|远程命令执行：找到 Windows PowerShell 命令|
|932130|远程命令执行：找到 Unix Shell 表达式|
|932140|远程命令执行：找到 Windows FOR/IF 命令|
|932150|远程命令执行：直接 Unix 命令执行|
|932160|远程命令执行：Shellshock (CVE-2014-6271)|
|932170|远程命令执行：Shellshock (CVE-2014-6271)|
|932171|远程命令执行：Shellshock (CVE-2014-6271)|
|932180|受限文件上传企图|

### <a name="php-attacks"></a><a name="drs933-11"></a> PHP 攻击
|RuleId|说明|
|---|---|
|933100|PHP 注入攻击：找到 PHP 开启标记|
|933110|PHP 注入攻击：找到 PHP 脚本文件上传|
|933120|PHP 注入攻击：找到配置指令|
|933130|PHP 注入攻击：找到变量|
|933140|PHP 注入攻击：找到 I/O 流|
|933150|PHP 注入攻击：找到高风险的 PHP 函数名称|
|933151|PHP 注入攻击：找到中等风险的 PHP 函数名称|
|933160|PHP 注入攻击：找到高风险的 PHP 函数调用|
|933170|PHP 注入攻击：序列化对象注入|
|933180|PHP 注入攻击：找到可变函数调用|

### <a name="xss---cross-site-scripting"></a><a name="drs941-11"></a> XSS - 跨站脚本
|RuleId|说明|
|---|---|
|941100|检测到通过 libinjection 展开的 XSS 攻击|
|941101|检测到通过 libinjection 展开的 XSS 攻击|
|941110|XSS 筛选器 - 类别 1：脚本标记向量|
|941120|XSS 筛选器 - 类别 2：事件处理程序向量|
|941130|XSS 筛选器 - 类别 3：属性向量|
|941140|XSS 筛选器 - 类别 4：Javascript URI 向量|
|941150|XSS 筛选器 - 类别 5：不允许的 HTML 属性|
|941160|NoScript XSS InjectionChecker：HTML 注入|
|941170|NoScript XSS InjectionChecker：属性注入|
|941180|节点验证器方块列表关键字|
|941190|IE XSS 筛选器 - 检测到攻击。|
|941200|IE XSS 筛选器 - 检测到攻击。|
|941210|IE XSS 筛选器 - 检测到攻击。|
|941220|IE XSS 筛选器 - 检测到攻击。|
|941230|IE XSS 筛选器 - 检测到攻击。|
|941240|IE XSS 筛选器 - 检测到攻击。|
|941250|IE XSS 筛选器 - 检测到攻击。|
|941260|IE XSS 筛选器 - 检测到攻击。|
|941270|IE XSS 筛选器 - 检测到攻击。|
|941280|IE XSS 筛选器 - 检测到攻击。|
|941290|IE XSS 筛选器 - 检测到攻击。|
|941300|IE XSS 筛选器 - 检测到攻击。|
|941310|US-ASCII 格式错误编码 XSS 筛选器 - 检测到攻击。|
|941320|检测到可能的 XSS 攻击 - HTML 标记处理程序|
|941330|IE XSS 筛选器 - 检测到攻击。|
|941340|IE XSS 筛选器 - 检测到攻击。|
|941350|UTF-7 编码 IE XSS - 检测到攻击。|

>[!NOTE]
> 本文包含对术语“黑名单”的引用，Microsoft 不再使用该术语。 在从软件中删除该术语后，我们会将其从本文中删除。

### <a name="sqli---sql-injection"></a><a name="drs942-11"></a> SQLI - SQL 注入
|RuleId|说明|
|---|---|
|942100|检测到通过 libinjection 展开的 SQL 注入攻击|
|942110|SQL 注入攻击：检测到常见注入测试|
|942120|SQL 注入攻击：检测到 SQL 运算符|
|942140|SQL 注入攻击：检测到常用 DB 名称|
|942150|SQL 注入攻击|
|942160|检测到使用 sleep() 或 benchmark() 的盲注 sqli 测试。|
|942170|检测到包含条件查询的 SQL 基准和休眠注入企图|
|942180|检测到基本 SQL 身份验证绕过尝试 1/3|
|942190|检测到 MSSQL 代码执行和信息收集尝试|
|942200|检测到 MySQL 注释/空间经过模糊处理的注入和反引号终止|
|942210|检测链式 SQL 注入尝试次数 1/2|
|942220|查找整数溢出攻击，这些攻击来自 skipfish，但 3.0.00738585072007e-308 是“幻数”故障|
|942230|检测到条件 SQL 注入企图|
|942240|检测 MySQL 字符集开关和 MSSQL DoS 尝试|
|942250|检测 MATCH AGAINST、MERGE 和 EXECUTE IMMEDIATE 注入|
|942260|检测到基本 SQL 身份验证绕过尝试 2/3|
|942270|正在查找基本 sql 注入。 针对 mysql、oracle 和其他系统的常见攻击字符串。|
|942280|检测 Postgres pg_sleep 注入、waitfor 延迟攻击和数据库关闭尝试|
|942290|查找基本 MongoDB SQL 注入企图|
|942300|检测到 MySQL 注释、条件和 ch(a)r 注入|
|942310|检测链式 SQL 注入尝试次数 2/2|
|942320|检测 MySQL 和 PostgreSQL 存储过程/函数注入|
|942330|检测到经典 SQL 注入探测 1/3|
|942340|检测到基本 SQL 身份验证绕过尝试 3/3|
|942350|检测 MySQL UDF 注入和其他数据/结构操作企图|
|942360|检测到连接的基本 SQL 注入和 SQLLFI 尝试|
|942361|检测基于关键字 alter 或 union 的基本 SQL 注入|
|942370|检测到经典 SQL 注入探测 2/3|
|942380|SQL 注入攻击|
|942390|SQL 注入攻击|
|942400|SQL 注入攻击|
|942410|SQL 注入攻击|
|942430|受限 SQL 字符异常情况检测 (args)：已超出特殊字符数 (12)|
|942440|检测到 SQL 注释序列。|
|942450|识别到 SQL 十六进制编码|
|942470|SQL 注入攻击|
|942480|SQL 注入攻击|

### <a name="session-fixation"></a><a name="drs943-11"></a> SESSION-FIXATION
|RuleId|说明|
|---|---|
|943100|可能的会话固定攻击：在 HTML 中设置 Cookie 值|
|943110|可能的会话固定攻击：包含域外引用方的 SessionID 参数名称|
|943120|可能的会话固定攻击：不包含引用方的 SessionID 参数名称|

### <a name="java-attacks"></a><a name="drs944-11"></a> Java 攻击
|RuleId|说明|
|---|---|
|944100|远程命令执行：检测到可疑的 Java 类|
|944110|可能的会话固定攻击：在 HTML 中设置 Cookie 值|
|944120|远程命令执行：Java 序列化 (CVE-2015-5842)|
|944130|检测到可疑的 Java 类|
|944200|检测到 magic 字节，正在使用可能的 java 序列化|
|944210|检测到 Base64 编码的 magic 字节，正在使用可能的 java 序列化|
|944240|远程命令执行：Java 序列化 (CVE-2015-5842)|
|944250|远程命令执行：检测到可疑的 Java 方法|

### <a name="ms-threatintel-webshells"></a><a name="drs9905-11"></a> MS-ThreatIntel-WebShells
|RuleId|说明|
|---|---|
|99005002|Web Shell 交互尝试 (POST)|
|99005003|Web Shell 上传尝试 (POST) - CHOPPER PHP|
|99005004|Web Shell 上传尝试 (POST) - CHOPPER ASPX|

### <a name="ms-threatintel-appsec"></a><a name="drs9903-11"></a> MS-ThreatIntel-AppSec
|RuleId|说明|
|---|---|
|99030001|标头中的路径遍历规避 (/.././../)|
|99030002|请求正文中的路径遍历规避 (/.././../)|

### <a name="ms-threatintel-sqli"></a><a name="drs99031-11"></a> MS-ThreatIntel-SQLI
|RuleId|说明|
|---|---|
|99031001|SQL 注入攻击：检测到常见注入测试|
|99031002|检测到 SQL 注释序列。|

### <a name="ms-threatintel-cves"></a><a name="drs99001-11"></a> MS-ThreatIntel-CVEs
|RuleId|说明|
|---|---|
|99001001|使用已知凭据尝试利用 F5 tmui (CVE-2020-5902) REST API|

# <a name="drs-10"></a>[DRS 1.0](#tab/drs10)

## <a name="10-rule-sets"></a><a name="drs10"></a> 1.0 规则集

### <a name="protocol-attack"></a><a name="drs921-10"></a> PROTOCOL-ATTACK
|RuleId|说明|
|---|---|
|921110|HTTP 请求走私攻击|
|921120|HTTP 响应拆分攻击|
|921130|HTTP 响应拆分攻击|
|921140|通过标头展开的 HTTP 标头注入攻击|
|921150|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF）|
|921151|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF）|
|921160|通过有效负载展开的 HTTP 标头注入攻击（检测到 CR/LF 和标头名称）|

### <a name="lfi---local-file-inclusion"></a><a name="drs930-10"></a> LFI - 本地文件包含
|RuleId|说明|
|---|---|
|930100|路径遍历攻击 (/../)|
|930110|路径遍历攻击 (/../)|
|930120|OS 文件访问企图|
|930130|受限文件访问企图|

### <a name="rfi---remote-file-inclusion"></a><a name="drs931-10"></a> RFI - 远程文件包含
|RuleId|说明|
|---|---|
|931100|可能的远程文件包含 (RFI) 攻击：使用 IP 地址的 URL 参数|
|931110|可能的远程文件包含 (RFI) 攻击：对 URL 有效负载使用常见 RFI 漏洞参数名|
|931120|可能的远程文件包含 (RFI) 攻击：在 URL 有效负载中使用尾随问号 (?)|
|931130|可能的远程文件包含 (RFI) 攻击：域外引用/链接|

### <a name="rce---remote-command-execution"></a><a name="drs932-10"></a> RCE - 远程命令执行
|RuleId|说明|
|---|---|
|932100|远程命令执行：Unix 命令注入|
|932105|远程命令执行：Unix 命令注入|
|932110|远程命令执行：Windows 命令注入|
|932115|远程命令执行：Windows 命令注入|
|932120|远程命令执行：找到 Windows PowerShell 命令|
|932130|远程命令执行：找到 Unix Shell 表达式|
|932140|远程命令执行：找到 Windows FOR/IF 命令|
|932150|远程命令执行：直接 Unix 命令执行|
|932160|远程命令执行：找到 Unix Shell 代码|
|932170|远程命令执行：Shellshock (CVE-2014-6271)|
|932171|远程命令执行：Shellshock (CVE-2014-6271)|
|932180|受限文件上传企图|

### <a name="php-attacks"></a><a name="drs933-10"></a> PHP 攻击
|RuleId|说明|
|---|---|
|933100|PHP 注入攻击：找到开始/结束标记|
|933110|PHP 注入攻击：找到 PHP 脚本文件上传|
|933120|PHP 注入攻击：找到配置指令|
|933130|PHP 注入攻击：找到变量|
|933131|PHP 注入攻击：找到变量|
|933140|PHP 注入攻击：找到 I/O 流|
|933150|PHP 注入攻击：找到高风险的 PHP 函数名称|
|933151|PHP 注入攻击：找到中等风险的 PHP 函数名称|
|933160|PHP 注入攻击：找到高风险的 PHP 函数调用|
|933161|PHP 注入攻击：找到低值 PHP 函数调用|
|933170|PHP 注入攻击：序列化对象注入|
|933180|PHP 注入攻击：找到可变函数调用|

### <a name="xss---cross-site-scripting"></a><a name="drs941-10"></a> XSS - 跨站脚本
|RuleId|说明|
|---|---|
|941100|检测到通过 libinjection 展开的 XSS 攻击|
|941101|检测到通过 libinjection 展开的 XSS 攻击。|
|941110|XSS 筛选器 - 类别 1：脚本标记向量|
|941120|XSS 筛选器 - 类别 2：事件处理程序向量|
|941130|XSS 筛选器 - 类别 3：属性向量|
|941140|XSS 筛选器 - 类别 4：Javascript URI 向量|
|941150|XSS 筛选器 - 类别 5：不允许的 HTML 属性|
|941160|NoScript XSS InjectionChecker：HTML 注入|
|941170|NoScript XSS InjectionChecker：属性注入|
|941180|节点验证器方块列表关键字|
|941190|使用样式表的 XSS|
|941200|使用 VML 帧的 XSS|
|941210|使用经过模糊处理的 Javascript 的 XSS|
|941220|使用经过模糊处理的 VB Script 的 XSS|
|941230|使用“embed”标记的 XSS|
|941240|使用“import”或“implementation”属性的 XSS|
|941250|IE XSS 筛选器 - 检测到攻击。|
|941260|使用“meta”标记的 XSS|
|941270|使用“link”href 的 XSS|
|941280|使用“base”标记的 XSS|
|941290|使用“applet”标记的 XSS|
|941300|使用“object”标记的 XSS|
|941310|US-ASCII 格式错误编码 XSS 筛选器 - 检测到攻击。|
|941320|检测到可能的 XSS 攻击 - HTML 标记处理程序|
|941330|IE XSS 筛选器 - 检测到攻击。|
|941340|IE XSS 筛选器 - 检测到攻击。|
|941350|UTF-7 编码 IE XSS - 检测到攻击。|

>[!NOTE]
> 本文包含对术语“黑名单”的引用，Microsoft 不再使用该术语。 在从软件中删除该术语后，我们会将其从本文中删除。

### <a name="sqli---sql-injection"></a><a name="drs942-10"></a> SQLI - SQL 注入
|RuleId|说明|
|---|---|
|942100|检测到通过 libinjection 展开的 SQL 注入攻击|
|942110|SQL 注入攻击：检测到常见注入测试|
|942120|SQL 注入攻击：检测到 SQL 运算符|
|942140|SQL 注入攻击：检测到常用 DB 名称|
|942150|SQL 注入攻击|
|942160|检测到使用 sleep() 或 benchmark() 的盲注 sqli 测试。|
|942170|检测到包含条件查询的 SQL 基准和休眠注入企图|
|942180|检测到基本 SQL 身份验证绕过尝试 1/3|
|942190|检测到 MSSQL 代码执行和信息收集尝试|
|942200|检测到 MySQL 注释/空间经过模糊处理的注入和反引号终止|
|942210|检测链式 SQL 注入尝试次数 1/2|
|942220|查找整数溢出攻击，这些攻击来自 skipfish，但 3.0.00738585072007e-308 是“幻数”故障|
|942230|检测到条件 SQL 注入企图|
|942240|检测 MySQL 字符集开关和 MSSQL DoS 尝试|
|942250|检测 MATCH AGAINST、MERGE 和 EXECUTE IMMEDIATE 注入|
|942260|检测到基本 SQL 身份验证绕过尝试 2/3|
|942270|正在查找基本 sql 注入。 针对 mysql、oracle 和其他系统的常见攻击字符串。|
|942280|检测 Postgres pg_sleep 注入、waitfor 延迟攻击和数据库关闭尝试|
|942290|查找基本 MongoDB SQL 注入企图|
|942300|检测到 MySQL 注释、条件和 ch(a)r 注入|
|942310|检测链式 SQL 注入尝试次数 2/2|
|942320|检测 MySQL 和 PostgreSQL 存储过程/函数注入|
|942330|检测到经典 SQL 注入探测 1/2|
|942340|检测到基本 SQL 身份验证绕过尝试 3/3|
|942350|检测 MySQL UDF 注入和其他数据/结构操作企图|
|942360|检测到连接的基本 SQL 注入和 SQLLFI 尝试|
|942361|检测基于关键字 alter 或 union 的基本 SQL 注入|
|942370|检测到经典 SQL 注入探测 2/2|
|942380|SQL 注入攻击|
|942390|SQL 注入攻击|
|942400|SQL 注入攻击|
|942410|SQL 注入攻击|
|942430|受限 SQL 字符异常情况检测 (args)：已超出特殊字符数 (12)|
|942440|检测到 SQL 注释序列。|
|942450|识别到 SQL 十六进制编码|
|942470|SQL 注入攻击|
|942480|SQL 注入攻击|

### <a name="session-fixation"></a><a name="drs943-10"></a> SESSION-FIXATION
|RuleId|说明|
|---|---|
|943100|可能的会话固定攻击：在 HTML 中设置 Cookie 值|
|943110|可能的会话固定攻击：包含域外引用方的 SessionID 参数名称|
|943120|可能的会话固定攻击：不包含引用方的 SessionID 参数名称|

### <a name="java-attacks"></a><a name="drs944-10"></a> Java 攻击
|RuleId|说明|
|---|---|
|944100|远程命令执行：Apache Struts、Oracle WebLogic|
|944110|检测潜在的有效负载执行|
|944120|可能的有效负载执行和远程命令执行|
|944130|可疑的 Java 类|
|944200|利用 Java 反序列化 Apache Commons|
|944210|可能使用 Java 序列化|
|944240|远程命令执行：Java 序列化|
|944250|远程命令执行：检测到可疑的 Java 方法|

# <a name="bot-rules"></a>[机器人规则](#tab/bot)

## <a name="bot-manager-rule-sets"></a><a name="bot"></a> 机器人管理器规则集

### <a name="bad-bots"></a><a name="bot100"></a> 恶意机器人
|RuleId|说明|
|---|---|
|Bot100100|威胁情报检测到的恶意机器人|
|Bot100200|已伪造其标识的恶意机器人|

### <a name="good-bots"></a><a name="bot200"></a> 善意机器人
|RuleId|说明|
|---|---|
|Bot200100|搜索引擎爬网程序|
|Bot200200|未经验证的搜索引擎爬网程序|

### <a name="unknown-bots"></a><a name="bot300"></a> 未知机器人
|RuleId|说明|
|---|---|
|Bot300100|未指定的标识|
|Bot300200|用于 Web 爬网和攻击的工具和框架|
|Bot300300|常规用途 HTTP 客户端和 SDK|
|Bot300400|服务代理|
|Bot300500|站点运行状况监视服务|
|Bot300600|威胁情报检测到的未知机器人|
|Bot300700|其他机器人|

---


## <a name="next-steps"></a>后续步骤

- [Azure Front Door 的 Web 应用程序防火墙的自定义规则](waf-front-door-custom-rules.md)
