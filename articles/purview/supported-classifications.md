---
title: 支持的分类列表
description: 此页列出 Azure 监控范围中受支持的系统分类。
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 11/20/2020
ms.openlocfilehash: 7458b027add8be86d9491c674c2f1a0bc9fbc68c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551743"
---
# <a name="supported-classifications-in-azure-purview"></a>Azure 监控范围中支持的分类

本文列出了 Azure 监控范围 (预览) 中支持和定义的系统分类。

## <a name="defined-system-classifications"></a>定义的系统分类

Azure 监控范围按 [RegEx](https://wikipedia.org/wiki/Regular_expression) 和 [布隆筛选器](https://wikipedia.org/wiki/Bloom_filter)分类数据。 以下列表描述了 Azure 监控范围定义的系统分类的格式、模式和关键字。

每个分类名称都以 MICROSOFT 为前缀。

## <a name="bloom-filter-classifications"></a>布隆筛选器分类

## <a name="city-country-and-place"></a>城市、国家/地区

"城市"、"国家/地区" 和 "位置" 筛选器已使用可用于准备数据的最佳数据集进行了准备。

## <a name="person"></a>人员

已使用以下两个数据集准备 Person 布隆筛选器。

- [2010美国人口普查数据 (162-K 条目) ](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [使用所有年份 1880-2019 (98-K 条目 (来自 SSN) 的常用婴儿名称) ](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>RegEx 分类

## <a name="aba-routing"></a>ABA 路由

### <a name="format"></a>格式

九位数，可能采用格式化模式或未格式化模式

### <a name="pattern"></a>模式

成

- 以0、1、2、3、6、7或8开头的四位数字
- 连字符
- 四位数
- 连字符
- 不带格式的数字：以0、1、2、3、6、7或8开头的九个连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>阿根廷国家标准 (DNI) 号

### <a name="format"></a>格式

带有或不带句点的8位数字

### <a name="pattern"></a>模式

八位数字：

- 两位数
- 可选期间
- 三位数
- 可选期间
- 三位数

### <a name="keywords"></a>关键字

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>澳大利亚银行帐号

### <a name="format"></a>格式

具有或不具有银行状态分支号的6到10位数字

### <a name="pattern"></a>模式

帐号是六到10位数字。

澳大利亚银行状态分支号码：

- 三位数
- 连字符
- 三位数

### <a name="keywords"></a>关键字

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>澳大利亚驾照号码

### <a name="format"></a>格式
九个字母和数字

### <a name="pattern"></a>模式
九个字母和数字：

- 两位数 (不区分大小写) 
- 两位数
-  (不区分大小写的五个数字或字母) 

要么

- 一至两个可选字母 (不区分大小写) 
- 4到9位数字

要么

-  (不区分大小写的九位数或字母) 

### <a name="keywords"></a>关键字

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australia-medical-account-number"></a>澳大利亚医疗帐号

### <a name="format"></a>格式

10-11 位

### <a name="pattern"></a>模式

10-11 位数：

- 第一个数字在2-6 范围内
- 第九位是检查数字
- 第十个数字是问题数字
- 第11个数字 (可选) 为单个数字

### <a name="keywords"></a>关键字

#### <a name="keyword_australia_medical_account_number"></a>Keyword_Australia_Medical_Account_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>澳大利亚登记卡号码

### <a name="format"></a>格式

字母后跟7位数

### <a name="pattern"></a>模式

字母 (区分大小写) 后跟7位数

### <a name="keywords"></a>关键字

#### <a name="keyword_passport"></a>关键字 \_ passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>关键字 \_ 澳大利亚 \_ passport \_ 号码

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>澳大利亚税金文件编号

### <a name="format"></a>格式

8到9位数字

### <a name="pattern"></a>模式

通常以空格显示8到9的数字，如下所示：

- 三位数
- 可选的空间
- 三位数
- 可选的空间
- 最后一个数字为复选数字的两到三位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_australia_tax_file_number"></a>关键字 \_ 澳大利亚 \_ 税金 \_ 文件 \_ 编号

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>比利时国号

### <a name="format"></a>格式

11位数加可选分隔符

### <a name="pattern"></a>模式

11位数加分隔符：

- 6位数字和两个可选句点，格式为 YY。MM.DD 出生日期
- 用点、短划线和空格分隔的可选分隔符
- 对于男性，三个顺序数字 (奇数，甚至对于女性) 
- 用点、短划线和空格分隔的可选分隔符
- 两个检查数字

### <a name="keywords"></a>关键字

#### <a name="keyword_belgium_national_number"></a>关键字 \_ 比利时 \_ 国 \_ 号

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>巴西 CPF 号

### <a name="format"></a>格式

11个数字，其中包含一个检查数字，可以设置格式或未格式化

### <a name="pattern"></a>模式

成

- 三位数
- 句点
- 三位数
- 句点
- 三位数
- 连字符
- 两位数，是检查数字

未格式化：

- 11位，其中最后两个数字是检查数字

### <a name="keywords"></a>关键字

#### <a name="keyword_brazil_cpf"></a>关键字 \_ 巴西 \_ cpf

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>巴西法定实体编号 (CNPJ) 

### <a name="format"></a>格式

包含注册号、分支号和检查数字的14位数字，加上分隔符

### <a name="pattern"></a>模式

14位和分隔符：

- 两位数
- 句点
- 三位数
- 句点
- 三位数字 (前面的八位数字是注册号) 
- 正斜杠
- 四位数分支编号
- 连字符
- 两位数，是检查数字

### <a name="keywords"></a>关键字

#### <a name="keyword_brazil_cnpj"></a>关键字 \_ 巴西 \_ cnpj

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>巴西国家标识卡 (RG) 

### <a name="format"></a>格式

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>模式

:::no-loc text="Registro Geral (old format):":::

- 两位数
- 句点
- 三位数
- 句点
- 三位数
- 连字符
- 一个数字，它是一个检查数字

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 位
- 连字符
- 一个数字，它是一个检查数字

### <a name="keywords"></a>关键字

#### <a name="keyword_brazil_rg"></a>关键字 \_ 巴西 \_ rg

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>加拿大银行帐号

### <a name="format"></a>格式

7或12位数字

### <a name="pattern"></a>模式

加拿大银行帐号为7或12位。

加拿大银行帐户的转口号码为：

- 五位数
- 连字符
- 三位数或
- &quot;0 0&quot;
- 八位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_canada_bank_account_number"></a>关键字 \_ 加拿大 \_ 银行 \_ 帐号 \_

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>加拿大驾照号码

### <a name="format"></a>格式

依省

### <a name="pattern"></a>模式

涵盖阿尔伯达省、英属哥伦比亚、Manitoba、New 不伦瑞克、纽芬兰/拉布拉多、Nova 斯科舍、安大略省、Prince Edward 岛、魁北克和萨斯的各种模式

### <a name="keywords"></a>关键字

#### <a name="keyword_province_name_drivers_license_name"></a>关键字 \_ [省份 \_ name] \_ 驱动程序 \_ 许可证 \_ 名称

- 省/市/自治区缩写，例如 AB
- 省市自治区名，例如阿尔伯达省

#### <a name="keyword_canada_drivers_license"></a>关键字 \_ 加拿大 \_ 驱动程序 \_ 许可证

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>加拿大运行状况服务编号

### <a name="format"></a>格式

10 位

### <a name="pattern"></a>模式

10 位

### <a name="keywords"></a>关键字

#### <a name="keyword_canada_health_service_number"></a>关键字 \_ 加拿大 \_ 运行状况 \_ 服务 \_ 编号

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>加拿大 passport 号码

### <a name="format"></a>格式

后跟六个数字的两个大写字母

### <a name="pattern"></a>模式

后跟六个数字的两个大写字母

### <a name="keywords"></a>关键字

#### <a name="keyword_canada_passport_number"></a>关键字 \_ 加拿大 \_ passport \_ 号码

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>关键字 \_ passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>加拿大个人健康标识号 (PHIN) 

### <a name="format"></a>格式

九位数

### <a name="pattern"></a>模式

九位数

### <a name="keywords"></a>关键字

#### <a name="keyword_canada_phin"></a>关键字 \_ 加拿大 \_ phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>\_加拿大关键字 \_

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>加拿大社会保险电话号码

### <a name="format"></a>格式

带有可选连字符或空格的九位数

### <a name="pattern"></a>模式

成

- 三位数
- 连字符或空格
- 三位数
- 连字符或空格
- 三位数

未格式化：九位数

### <a name="keywords"></a>关键字

#### <a name="keyword_sin"></a>关键字 \_ sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>关键字 \_ sin \_ 协作

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>智利标识卡号

### <a name="format"></a>格式

7到8个数字加上分隔符一个检查数字或字母

### <a name="pattern"></a>模式

7到8个数字加分隔符：

- 一到两位数
- 可选期间
- 三位数
- 可选期间
- 三位数
- 破折号
- 一位或 (不区分大小写) 

### <a name="keywords"></a>关键字

#### <a name="keyword_chile_id_card"></a>关键字 \_ 智利 \_ id \_ 卡

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>中国居民标识卡 (PRC) 号

### <a name="format"></a>格式

18位数

### <a name="pattern"></a>模式

18位数：

- 六位数，即地址代码
- 格式为 YYYYMMDD 的八位数字，为出生日期
- 三位数字，即订单代码
- 一个数字，它是一个检查数字

### <a name="keywords"></a>关键字

#### <a name="keyword_china_resident_id"></a>关键字 \_ 中国 \_ 居民 \_ id

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>信用卡号

### <a name="format"></a>格式

可以设置格式或未格式化的14到16位 (dddddddddddddddd) ，并且必须通过 Luhn 测试。

### <a name="pattern"></a>模式

一种复杂且强健的模式，可检测世界各地所有主要品牌的卡，包括签证、MasterCard、发现卡、JCB、美国运通、礼品卡和 diner 卡。

### <a name="keywords"></a>关键字

#### <a name="keyword_cc_verification"></a>关键字 \_ 抄送 \_ 验证

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>关键字 \_ 抄送 \_ 名称

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>克罗地亚驾照号码

此敏感信息类型实体仅适用于欧盟驾照的 "敏感信息类型"。

### <a name="format"></a>格式

8位无空格和分隔符

### <a name="pattern"></a>模式

八位数字

### <a name="keywords"></a>关键字

#### <a name="keywords_eu_drivers_license_number"></a>\_欧盟 \_ 驱动程序的 \_ 许可证 \_ 号关键字

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>关键字 \_ 克罗地亚 \_ 欧盟 \_ 驱动程序的 \_ 许可证 \_ 编号

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>克罗地亚标识卡号

此敏感信息类型实体包含在欧盟国家识别号敏感信息类型中，并可作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

九位数

### <a name="pattern"></a>模式

九个连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_croatia_id_card"></a>关键字 \_ 克罗地亚 \_ id \_ 卡

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>克罗地亚个人身份 (OIB) 号

### <a name="format"></a>格式

11位数

### <a name="pattern"></a>模式

11位数：

- 10 位
- 最后一个数字是一个检查数字

### <a name="keywords"></a>关键字

#### <a name="keyword_croatia_oib_number"></a>关键字 \_ 克罗地亚 \_ oib \_ 号

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>丹麦个人识别码

### <a name="format"></a>格式

包含连字符的10位数字

### <a name="pattern"></a>模式

10位数：

- 格式为 DDMMYY 的六位数字，即出生日期
- 连字符
- 最后一个数字为复选数字的四位数

### <a name="keywords"></a>关键字

#### <a name="keyword_denmark_id"></a>关键字 \_ 丹麦 \_ id

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>欧盟借记卡号

### <a name="format"></a>格式

16位

### <a name="pattern"></a>模式

复杂和可靠的模式

### <a name="keywords"></a>关键字

#### <a name="keyword_eu_debit_card"></a>关键字 \_ 欧盟 \_ 借记卡 \_

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>关键字 \_ 卡 \_ 术语 \_ dict

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>关键字 \_ 卡 \_ 安全 \_ 术语 \_ dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>关键字 \_ 卡 \_ 过期 \_ 条款 \_ dict

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>欧盟驱动程序的许可证编号

这些是欧盟司机的 "敏感信息类型" 中的实体。

- 奥地利
- 比利时
- 保加利亚
- 克罗地亚
- 塞浦路斯
- 捷克语
- 丹麦
- 爱沙尼亚
- 芬兰
- 法国
- 德国
- 希腊
- 匈牙利
- 爱尔兰
- 意大利
- 拉脱维亚
- 立陶宛
- 卢森堡
- 马耳他
- 荷兰
- 波兰
- 葡萄牙
- 罗马尼亚
- 斯洛伐克
- 斯洛文尼亚
- 西班牙
- 瑞典
- 英国

## <a name="eu-national-identification-number"></a>欧盟国家识别号

这些是欧盟国家识别号敏感信息类型中的实体。

- 奥地利
- 比利时
- 保加利亚
- 克罗地亚
- 塞浦路斯
- 捷克语
- 丹麦
- 爱沙尼亚
- 芬兰
- 法国
- 德国
- 希腊
- 匈牙利
- 爱尔兰
- 意大利
- 拉脱维亚
- 立陶宛
- 卢森堡
- 马耳他
- 荷兰
- 波兰
- 葡萄牙
- 罗马尼亚
- 斯洛伐克
- 斯洛文尼亚
- 西班牙
- 英国

## <a name="eu-passport-number"></a>欧盟 passport 号码

这些是欧盟 passport 号码中的实体，typeThese 是欧盟 passport 号码捆绑中的实体。

- 奥地利
- 比利时
- 保加利亚
- 克罗地亚
- 塞浦路斯
- 捷克语
- 丹麦
- 爱沙尼亚
- 芬兰
- 法国
- 德国
- 希腊
- 匈牙利
- 爱尔兰
- 意大利
- 拉脱维亚
- 立陶宛
- 卢森堡
- 马耳他
- 荷兰
- 波兰
- 葡萄牙
- 罗马尼亚
- 斯洛伐克
- 斯洛文尼亚
- 西班牙
- 瑞典
- 英国

## <a name="eu-social-security-number-or-equivalent-identification"></a>欧盟社会保障号或等效标识

这些是欧盟社会保障号或等效标识敏感信息类型中的实体。

- 奥地利
- 比利时
- 克罗地亚
- 捷克语
- 丹麦
- 芬兰
- 法国
- 德国
- 希腊
- 匈牙利
- 葡萄牙
- 西班牙
- 瑞典

## <a name="eu-tax-identification-number"></a>欧盟税务标识号

这些实体采用欧盟税务标识号敏感信息类型。

- 奥地利
- 比利时
- 保加利亚
- 克罗地亚
- 塞浦路斯
- 捷克语
- 丹麦
- 爱沙尼亚
- 芬兰
- 法国
- 德国
- 希腊
- 匈牙利
- 爱尔兰
- 意大利
- 拉脱维亚
- 立陶宛
- 卢森堡
- 马耳他
- 荷兰
- 波兰
- 葡萄牙
- 罗马尼亚
- 斯洛伐克
- 斯洛文尼亚
- 西班牙
- 瑞典
- 英国



## <a name="finland-national-id"></a>芬兰国 ID

### <a name="format"></a>格式

六个数字加上一个字符，指示世纪加上三个数字加上一个复选数字

### <a name="pattern"></a>模式

模式必须包括以下各项：

- 格式为 DDMMYY 的6位数字，是出生日期
- 世纪标记 (为 "-"、"+" 或 "a" ) 
- 三位数个人识别码
- 不区分大小写的数字或字母 (不区分大小写) 

### <a name="keywords"></a>关键字

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>芬兰护照号码

此敏感信息类型实体在欧盟 Passport 号码的敏感信息类型中提供，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

九个字母和数字的组合

### <a name="pattern"></a>模式

九个字母和数字的组合：

-  (不区分大小写的两个字母) 
- 7位数

### <a name="keywords"></a>关键字

#### <a name="keywords_eu_passport_number_common"></a>\_标准欧盟 \_ passport \_ 号码 \_

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>关键字 \_ 芬兰 \_ passport \_ 号码

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>法国驾照号码

此敏感信息类型实体以欧盟驾照的 "敏感信息类型" 提供，并可作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

12 位数

### <a name="pattern"></a>模式

12个数字，其中包含对类似模式（如法语电话号码）的验证

### <a name="keywords"></a>关键字

#### <a name="keyword_french_drivers_license"></a>关键字 \_ 法语 \_ 驱动程序 \_ 许可证

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>法国国内 ID 卡 (CNI) 

### <a name="format"></a>格式

12 位数

### <a name="pattern"></a>模式

12 位数

### <a name="keywords"></a>关键字

#### <a name="keywords_france_eu_national_id_card"></a>\_法国 \_ 欧盟 \_ 国家 \_ id \_ 卡的关键字

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>法国 passport 号码

此敏感信息类型实体在欧盟 Passport 号码的敏感信息类型中提供，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

九位数字和字母

### <a name="pattern"></a>模式

九位数和字母：

- 两位数
-  (不区分大小写的两个字母) 
- 五位数

### <a name="keywords"></a>关键字

#### <a name="keyword_passport"></a>关键字 \_ passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>法国社会保障号 (INSEE) 或等效标识

此敏感信息类型实体包含在欧盟社会保障号和等效的 ID 敏感信息类型中，并以独立的敏感信息类型实体的形式提供。

### <a name="format"></a>格式

15 位

### <a name="pattern"></a>模式

必须匹配以下两种模式之一：

- 13个数字后跟一个空格，后跟两个数字或
- 15个连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_fr_insee"></a>关键字 \_ fr \_ insee

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>德国驾照号码

此敏感信息类型实体包含在欧盟驾照的 "敏感信息类型" 中，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

11位和字母的组合

### <a name="pattern"></a>模式

11位和字母 (不区分大小写) ：

- 数字或字母
- 两位数
- 六个数字或字母
- 一个数字
- 数字或字母

### <a name="keywords"></a>关键字

#### <a name="keyword_german_drivers_license_number"></a>关键字 \_ 德语 \_ 驱动程序 \_ 许可证 \_ 编号

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>德国标识卡号

### <a name="format"></a>格式

自2010年11月1日起：九个字母和数字

从 1 1987 年4月至31年 10 2010 月31日

### <a name="pattern"></a>模式

自2010年11月1日起：

- 一个字母 (区分大小写) 
- 八位数字

1月1日到 1987 2010 年10月31日之前：

- 10 位

### <a name="keywords"></a>关键字

#### <a name="keyword_germany_id_card"></a>关键字 \_ 德国 \_ id \_ 卡

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>德国登记卡号码

此敏感信息类型实体包含在欧盟 Passport 号码的敏感信息类型中，并可作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

10位数或字母

### <a name="pattern"></a>模式

模式必须包括以下各项：

- 第一个字符是此集合中的一个数字或一个字母， (C，F，G，H，J，K) 
- 三位数
- 此集合中有五个数字或字母 (C，-H，J N，P，R，T，V-Z) 
- 一个数字

### <a name="keywords"></a>关键字

#### <a name="keyword_german_passport"></a>关键字 \_ 德语 \_ passport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>希腊的国家/地区 ID 卡

### <a name="format"></a>格式

7-8 字母和数字的组合加上一个短划线

### <a name="pattern"></a>模式

 (旧格式的七个字母和数字) ：

- 一个字母 (希腊语字母表中的任何字母) 
- 破折号
- 六位数

8个字母和数字 (新格式) ：

- 两个字母的字母字符同时出现在希腊语和拉丁字母表中 (ABEZHIKMNOPTYX) 
- 破折号
- 六位数

### <a name="keywords"></a>关键字

#### <a name="keyword_greece_id_card"></a>关键字 \_ 希腊 \_ id \_ 卡

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>香港特别行政区标识卡 (HKID) 号

### <a name="format"></a>格式

8-9 字母和数字的组合以及最后一个字符周围的可选括号

### <a name="pattern"></a>模式

8-9 号的组合：

- 1-2 (不区分大小写) 
- 六位数
- 最后一个字符 (任意数字或字母 A) ，这是检查数字，可选择性地括在括号中。

### <a name="keywords"></a>关键字

#### <a name="keyword_hong_kong_id_card"></a>关键字 \_ 香港 \_ \_ id \_ 卡

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>IP 地址

### <a name="format"></a>格式

#### <a name="ipv4"></a>IPv4

复杂模式，用于格式化 (句点的帐户) 和未格式化 (无句点) IPv4 地址的版本

#### <a name="ipv6"></a>IPv6：

用于设置带格式的 IPv6 数字的复杂模式 (包含冒号) 

### <a name="pattern"></a>模式

### <a name="keywords"></a>关键字

#### <a name="keyword_ipaddress"></a>关键字 \_ ipaddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a> (平移) 的印度永久帐号

### <a name="format"></a>格式

10个字母或数字

### <a name="pattern"></a>模式

10个字母或数字：

- 三个字母 (区分大小写) 
- C、P、H、F、A、T、B、L、J、G (不区分大小写) 
- 字母
- 四位数字
- 字母 (区分大小写) 

### <a name="keywords"></a>关键字

#### <a name="keyword_india_permanent_account_number"></a>关键字 \_ 印度 \_ 永久 \_ 帐号 \_

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>印度唯一标识 (Aadhaar) 号

### <a name="format"></a>格式

12位数包含可选空格或短划线

### <a name="pattern"></a>模式

12位数：

- 数字，不是0或1
- 三位数
- 可选的空格或短划线
- 四位数字
- 可选的空格或短划线
- 最后一个数字，它是检查数字

### <a name="keywords"></a>关键字

#### <a name="keyword_india_aadhar"></a>\_印度 \_ aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>印度尼西亚标识卡 (KTP) 号

### <a name="format"></a>格式

包含可选句点的16位数

### <a name="pattern"></a>模式

16位数字：

- 两位数的省/市/自治区代码
- 句点 (可选) 
- 两位数 regency 或城市代码
- 两位数 subdistrict 代码
- 句点 (可选) 
- 格式为 DDMMYY 的六位数字，即出生日期
- 句点 (可选) 
- 四位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_indonesia_id_card"></a>关键字 \_ 印度尼西亚 \_ id \_ 卡

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a> (IBAN) 的国际银行帐号

### <a name="format"></a>格式

国家/地区代码 (两个字母) 加上检查数位 (两位数) 加 :::no-loc text="bban"::: 数字 (最多30个字符) 

### <a name="pattern"></a>模式

模式必须包括以下各项：

- 双字母国家/地区代码
- 两个检查数位 (后跟一个可选的空间) 
- 1-7 可以用空格分隔 (四个字母或数字组成的组) 
- 1-3 个字母或数字

每个国家/地区的格式略有不同。 IBAN 敏感信息类型涵盖以下60国家/地区：

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>关键字

无

## <a name="ireland-personal-public-service-pps-number"></a>爱尔兰个人公共服务 (PPS) 号

### <a name="format"></a>格式

旧格式 (到) Dec 2012：

- 7位后跟1-2 个字母

新格式 (1 2013 年1月1日) ：

- 7位后跟两个字母

### <a name="pattern"></a>模式

旧格式 (到) Dec 2012：

- 7位数
- 一至两个字母 (不区分大小写) 

新格式 (1 2013 年1月1日) ：

- 7位数
- 不区分大小写的字母 (区分大小写) 
- 一个-I 或 W 范围内的可选字母 &quot;&quot;

### <a name="keywords"></a>关键字

#### <a name="keywords_ireland_eu_national_id_card"></a>关键字 \_ 爱尔兰 \_ 欧盟 \_ 国家/地区 \_ id \_ 卡

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>以色列银行帐号

### <a name="format"></a>格式

13位数

### <a name="pattern"></a>模式

成

- 两位数
- 破折号
- 三位数
- 破折号
- 八位数字

未格式化：

- 13个连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_israel_bank_account_number"></a>关键字 \_ 以色列 \_ 银行 \_ 帐号 \_

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>以色列国家标识号

### <a name="format"></a>格式

九位数

### <a name="pattern"></a>模式

九个连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_israel_national_id"></a>关键字 \_ 以色列 \_ 国家 \_ ID

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>意大利驱动程序的许可证编号

此敏感信息类型实体包含在欧盟驾照的 "敏感信息类型" 中，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

10个字母和数字的组合

### <a name="pattern"></a>模式

10个字母和数字的组合：

- 一个字母 (区分大小写) 
- 字母 &quot; A &quot; 或 &quot; V &quot; (不区分大小写) 
- 7位数
- 一个字母 (区分大小写) 

### <a name="keywords"></a>关键字

#### <a name="keyword_italy_drivers_license_number"></a>关键字 \_ 意大利 \_ 驱动程序 \_ 许可证 \_ 编号

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>日本银行帐号

### <a name="format"></a>格式

7位或八位数字

### <a name="pattern"></a>模式

银行帐号：

- 7位或八位数字
- 银行帐户分支代码：
- 四位数
- 空格或短划线 (可选) 
- 三位数

### <a name="keywords"></a>关键字

#### <a name="keyword_jp_bank_account"></a>关键字 \_ 日本 \_ 银行 \_ 帐户

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>关键字 \_ 日本 \_ 银行 \_ 分支 \_ 代码

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>日本驾照号码

### <a name="format"></a>格式

12 位数

### <a name="pattern"></a>模式

12个连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_jp_drivers_license_number"></a>关键字 \_ jp \_ 驱动程序 \_ 许可证 \_ 编号

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>日本护照号码

### <a name="format"></a>格式

后跟七个数字的两个字母

### <a name="pattern"></a>模式

两个字母 (不区分大小写) 后跟7位数

#### <a name="keyword_jp_passport"></a>关键字 \_ jp \_ passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>日本居住卡号

### <a name="format"></a>格式

12个字母和数字

### <a name="pattern"></a>模式

12个字母和数字：

-  (不区分大小写的两个字母) 
- 八位数字
-  (不区分大小写的两个字母) 

### <a name="keywords"></a>关键字

#### <a name="keyword_jp_residence_card_number"></a>关键字 \_ 日本 \_ 居住 \_ 卡 \_ 号

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>日本居民注册号

### <a name="format"></a>格式

11位数

### <a name="pattern"></a>模式

11个连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_jp_resident_registration_number"></a>关键字 \_ 日本 \_ 居民 \_ 注册 \_ 号

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>日本社交保险编号 (SIN) 

### <a name="format"></a>格式

7-12 位

### <a name="pattern"></a>模式

7-12 位数：

- 四位数
- 连字符 (可选) 
- 六位数或
- 7-12 连续位数

### <a name="keywords"></a>关键字

#### <a name="keyword_jp_sin"></a>关键字 \_ jp \_ sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>马来西亚识别卡编号

### <a name="format"></a>格式

12位数包含可选连字符

### <a name="pattern"></a>模式

12位数：

- 格式为 YYMMDD 的六位数字，即出生日期
- 划线 (可选) 
- 两个字母的出生位置代码
- 划线 (可选) 
- 三个随机数字
- 一位数的性别代码

### <a name="keywords"></a>关键字

#### <a name="keyword_malaysia_id_card_number"></a>关键字 \_ 马来西亚 \_ id \_ 卡 \_ 号

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>荷兰公民服务 (BSN) 号码

### <a name="format"></a>格式

包含可选空格的8个9位数

### <a name="pattern"></a>模式

八-九位数字：

- 三位数
- 空格 (可选) 
- 三位数
- 空格 (可选) 
- 双三位数

### <a name="keywords"></a>关键字

#### <a name="keywords_netherlands_eu_national_id_card"></a>关键字 \_ 荷兰 \_ 欧盟 \_ 国家/地区 \_ id \_ 卡

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>新西兰的健康部

### <a name="format"></a>格式

三个字母、一个空格 (可选) 和四位数

### <a name="pattern"></a>模式

- 除 "I" 和 "O" 外，三个字母 (区分大小写) 
- 空格 (可选) 
- 四位数

### <a name="keywords"></a>关键字

#### <a name="keyword_nz_terms"></a>关键字 \_ nz \_ 术语

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>挪威标识号

### <a name="format"></a>格式

11位数

### <a name="pattern"></a>模式

11位数：

- 格式为 DDMMYY 的六位数字，即出生日期
- 三位数的单个数字
- 两个检查数字

### <a name="keywords"></a>关键字

#### <a name="keyword_norway_id_number"></a>关键字 \_ 挪威 \_ id \_ 号

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>菲律宾统一多用途标识号

### <a name="format"></a>格式

由连字符分隔的12位数

### <a name="pattern"></a>模式

12位数：

- 四位数
- 连字符
- 7位数
- 连字符
- 一位

### <a name="keywords"></a>关键字

#### <a name="keyword_philippines_id"></a>关键字 \_ 菲律宾 \_ id

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>波兰标识卡

### <a name="format"></a>格式

三个字母和六个数字

### <a name="pattern"></a>模式

三个字母 (不区分大小写) 后接六个数字

### <a name="keywords"></a>关键字

#### <a name="keyword_poland_national_id_passport_number"></a>关键字 \_ 波兰 \_ 国 \_ id \_ passport \_ 号码

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>波兰国 ID (PESEL) 

### <a name="format"></a>格式

11位数

### <a name="pattern"></a>模式

- 6位数 YYMMDD 格式表示出生日期
- 4位数
- 1个检查数字

### <a name="keywords"></a>关键字

#### <a name="keyword_pesel_identification_number"></a>关键字 \_ pesel \_ 标识号 \_

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>波兰 passport 号码

此敏感信息类型实体包含在欧盟 Passport 号码的敏感信息类型中，并可作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

两个字母和七个数字

### <a name="pattern"></a>模式

两个字母 (不区分大小写) 后跟7位数

### <a name="keywords"></a>关键字

#### <a name="keyword_poland_national_id_passport_number"></a>关键字 \_ 波兰 \_ 国 \_ id \_ passport \_ 号码

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>葡萄牙公民卡编号

### <a name="format"></a>格式

八位数字

### <a name="pattern"></a>模式

八位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_portugal_citizen_card"></a>关键字 \_ 葡萄牙 \_ 公民 \_ 卡

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>葡萄牙驾照号码

此敏感信息类型实体仅适用于欧盟驾照的 "敏感信息类型"。

### <a name="format"></a>格式

两种模式-两个字母后跟5-8 位，带有特殊字符

### <a name="pattern"></a>模式

模式1：两个字母后跟5/6，带有特殊字符：

- 两个字母 (不区分大小写) 
- 连字符
- 5或6位数字
- 空格
- 一位

模式2：一个字母后跟6/8 位，带有特殊字符：

- 一个字母 (区分大小写) 
- 连字符
- 六位或八位数字
- 空格
- 一位

### <a name="keywords"></a>关键字

#### <a name="keywords_eu_drivers_license_number"></a>\_欧盟 \_ 驱动程序的 \_ 许可证 \_ 号关键字

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>关键字 \_ 葡萄牙 \_ 欧盟 \_ 驱动程序的 \_ 许可证 \_ 编号

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>沙特阿拉伯国民身份证

### <a name="format"></a>格式

10 位

### <a name="pattern"></a>模式

10个连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_saudi_arabia_national_id"></a>关键字 \_ 沙特阿拉伯 \_ \_ 国家 \_ id

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>新加坡国家注册标识卡 (NRIC) 号

### <a name="format"></a>格式

九个字母和数字

### <a name="pattern"></a>模式

- 九个字母和数字：
- 字母 &quot; F &quot; 、 &quot; G &quot; 、 &quot; S &quot; 或 &quot; T &quot; 不 (区分大小写) 
- 7位数
- 字母复选数字

### <a name="keywords"></a>关键字

#### <a name="keyword_singapore_nric"></a>关键字 \_ 新加坡 \_ nric

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>南非识别号

### <a name="format"></a>格式

13位数可能包含空格

### <a name="pattern"></a>模式

13位数：

- 格式为 YYMMDD 的六位数字，即出生日期
- 四位数
- 单位数公民指标
- 数字 &quot; 8 &quot; 或 &quot; 9&quot;
- 一个数字，它是校验和数字

### <a name="keywords"></a>关键字

#### <a name="keyword_south_africa_identification_number"></a>关键字 \_ 南非 \_ \_ 标识号 \_

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>韩国居民注册号

### <a name="format"></a>格式

包含连字符的13位数

### <a name="pattern"></a>模式

13位数：

- 格式为 YYMMDD 的六位数字，即出生日期
- 连字符
- 由世纪和性别确定的一位数
- 四位数的出生代码
- 用一位数字来区分前面的数字相同的人员
- 检查数字。

### <a name="keywords"></a>关键字

#### <a name="keyword_south_korea_resident_number"></a>关键字 \_ \_ 韩国 \_ 居民 \_ 号码

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a> (SSN) 的西班牙社会保障号

此敏感信息类型实体包含在欧盟社会保障号或等效的 ID 敏感信息类型中，并可作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

11-12 位

### <a name="pattern"></a>模式

11-12 位数：

- 两位数
- 正斜杠 (可选) 
- 7到8位
- 正斜杠 (可选) 
- 两位数

### <a name="keywords"></a>关键字

无

## <a name="sweden-national-id"></a>瑞典国家 ID

### <a name="format"></a>格式

10或12位数字和可选分隔符

### <a name="pattern"></a>模式

10或12位数字和可选分隔符：

- 两位数 (可选) 
- 六位日期格式的数字 YYMMDD
- &quot; - &quot; 或 &quot; + &quot; (可选) 的分隔符
- 四位数

### <a name="keywords"></a>关键字

#### <a name="keywords_swedish_national_identifier"></a>关键字 \_ 瑞典语 \_ 国家 \_ 标识符

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>瑞典 passport 号码

此敏感信息类型实体包含在欧盟 Passport 号码的敏感信息类型中，并可作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

八位数字

### <a name="pattern"></a>模式

八个连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_sweden_passport"></a>关键字 \_ 瑞典 \_ passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>关键字 \_ passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>SWIFT 代码

### <a name="format"></a>格式

四个字母后跟5-31 个字母或数字

### <a name="pattern"></a>模式

四个字母后跟5-31 个字母或数字：

- 四字母银行代码 (不区分大小写) 
- 可选的空间
- 4-28 (BBAN 的基本银行帐号 (的字母或数字) # A3
- 可选的空间
- BBAN 的余数) 一到三个字母或数字 (

### <a name="keywords"></a>关键字

#### <a name="keyword_swift"></a>关键字 \_ swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>台湾国家标识号

### <a name="format"></a>格式

一 (英文) ，后跟九位数

### <a name="pattern"></a>模式

一个) 英文字母 (，后跟九位数：

- 一个字母 (英语，不区分大小写) 
- 数字 &quot; 1 &quot; 或 &quot; 2&quot;
- 八位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_taiwan_national_id"></a>关键字 \_ 台湾 \_ 国家/地区 \_ id

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>台湾护照号码

### <a name="format"></a>格式

- 生物识别护照号码：九位数
- 非生物识别护照号码：九位数

### <a name="pattern"></a>模式

生物识别护照号码：

- 字符 &quot; 3&quot;
- 八位数字

无生物识别护照号码：

- 九位数

### <a name="keywords"></a>关键字

#### <a name="keyword_taiwan_passport"></a>关键字 \_ 台湾 \_ passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>台湾居民证书 (ARC/TARC) 号

### <a name="format"></a>格式

10个字母和数字

### <a name="pattern"></a>模式

10个字母和数字：

-  (不区分大小写的两个字母) 
- 八位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_taiwan_resident_certificate"></a>关键字 \_ 台湾 \_ 居民 \_ 证书

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>英国 驾照号码

此敏感信息类型实体包含在欧盟驾照的 "敏感信息类型" 中，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

采用指定格式的18个字母和数字的组合

### <a name="pattern"></a>模式

18个字母和数字：

- 五个字母 (区分大小写) 或数字 &quot; 9 &quot; 代替字母
- 一位
- 如果驱动程序是女性，即51到62而不是01到 12) ，日期格式 (MMDDY 中日期格式的五位数字将递增50
- 两个字母 (区分大小写) 或数字 &quot; 9 &quot; 代替字母
- 五位数

### <a name="keywords"></a>关键字

#### <a name="keyword_uk_drivers_license"></a>关键字 \_ uk \_ 驱动程序 \_ 许可证

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>英国 选举滚动编号

### <a name="format"></a>格式

后跟1-4 位的两个字母

### <a name="pattern"></a>模式

两个字母 (不区分大小写) 后跟1-4 个数字

### <a name="keywords"></a>关键字

#### <a name="keyword_uk_electoral"></a>关键字 \_ uk \_ 选举

- 理事会提名
- 提名窗体
- 选举注册
- 选举滚动

## <a name="uk-national-health-service-number"></a>英国 国家健康状况服务编号

### <a name="format"></a>格式

10-17 位用空格分隔

### <a name="pattern"></a>模式

10-17 位数：

- 三位或10位数字
- 空格
- 三位数
- 空格
- 四位数

### <a name="keywords"></a>关键字

#### <a name="keyword_uk_nhs_number"></a>关键字 \_ uk \_ nhs \_ number

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>关键字 \_ uk \_ nhs \_ 数字1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>关键字 \_ uk \_ nhs \_ number \_ dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>英国 国家保险号码 (NINO) 

此敏感信息类型实体包含在欧盟国家识别号敏感信息类型中，并可作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

由空格或短划线分隔的七个字符或9个字符

### <a name="pattern"></a>模式

两种可能的模式：

-  (有效 NINOs 的两个字母只使用此前缀中的特定字符，此模式将验证;不区分大小写) 
- 六位数
- "A"、"B"、"C" 或 "d" (如前缀，只允许在后缀中使用某些字符;不区分大小写) 

要么

- 两个字母
- 空格或短划线
- 两位数
- 空格或短划线
- 两位数
- 空格或短划线
- 两位数
- 空格或短划线
- "A"、"B"、"C" 或 "d"


### <a name="keywords"></a>关键字

#### <a name="keyword_uk_nino"></a>关键字 \_ uk \_ nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>英国 唯一纳税人参考编号

此敏感信息类型仅可在中使用：

- 数据丢失防护策略
- 通信符合性策略
- 信息管理
- 记录管理
- Microsoft cloud app security

### <a name="format"></a>格式

10位数字，不含空格和分隔符

### <a name="pattern"></a>模式

10 位

### <a name="keywords"></a>关键字

#### <a name="keywords_uk_eu_tax_file_number"></a>关键字 \_ 英国 \_ 欧盟 \_ 税 \_ 文件 \_ 编号

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>美国银行帐号

### <a name="format"></a>格式

6-17 位

### <a name="pattern"></a>模式

6-17 连续位数

### <a name="keywords"></a>关键字

#### <a name="keyword_usa_bank_account"></a>关键字 \_ usa \_ 银行 \_ 帐户

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>美国驾照号码

### <a name="format"></a>格式

取决于状态

### <a name="pattern"></a>模式

取决于状态（例如纽约）：

- 九位格式（例如 ddd）将匹配。
- 9个数字（如 ddddddddd）不匹配。

### <a name="keywords"></a>关键字

#### <a name="keyword_us_drivers_license_abbreviations"></a>关键字 \_ us \_ 驱动程序 \_ 许可证 \_ 缩写

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>关键字 \_ us \_ 驱动程序 \_ 许可证

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>关键字 \_ [state \_ name] \_ 驱动程序 \_ 许可证 \_ 名称

- 状态缩写 (例如， &quot; NY &quot;) 
- 状态名称 (例如 &quot; 纽约 &quot;) 

## <a name="us-individual-taxpayer-identification-number-itin"></a>美国单独的纳税人标识号 (某种) 

### <a name="format"></a>格式

9位以 &quot; 9 开头 &quot; 并包含 &quot; 7 &quot; 或 &quot; 8 &quot; 作为第四位数，可选择使用空格或短划线设置格式

### <a name="pattern"></a>模式

成

- 数字 &quot; 9&quot;
- 两位数
- 空格或短划线
- &quot;7 &quot; 或 &quot; 8&quot;
- 一个数字
- 空格或短划线
- 四位数

纯

- 数字 &quot; 9&quot;
- 两位数
- &quot;7 &quot; 或 &quot; 8&quot;
- 五位数

### <a name="keywords"></a>关键字

#### <a name="keyword_itin"></a>关键字 \_ 某种

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>U.S. 社会保障号 (SSN) 

### <a name="format"></a>格式

九位数，可能采用格式化模式或未格式化模式

>[!Note]
> 如果在2011年前发出，则 SSN 具有强格式，其中某些部分的数字必须在特定范围内，才能有效 (但是没有) 的校验和。
>

### <a name="pattern"></a>模式

四个函数在四种不同的模式中查找 Ssn：

- Func \_ ssn 查找 ssn，2011其格式为短划线或空格 (ddd-dd-DDDD 或 ddd dd dddd) 
- Func \_ \_ 未格式化的 Ssn 查找 ssn，其预2011强格式设置为9个连续数字， (ddddddddd) 
- Func \_ 随机 \_ 格式化 \_ ssn 查找格式为短划线或空格 (ddd-DD-dddd 或 ddd dd dddd) 的后 2011 ssn
- Func \_ 随机 \_ 无格式 \_ Ssn 查找 2011 ssn，未格式化为九个连续数字 (ddddddddd) 

### <a name="keywords"></a>关键字

#### <a name="keyword_ssn"></a>关键字 \_ ssn

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>美国/英国 passport 号码

英国 passport 号码的敏感信息类型实体在欧盟 Passport 号码敏感信息类型中提供，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

九位数

### <a name="pattern"></a>模式

九个连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_passport"></a>关键字 \_ passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
