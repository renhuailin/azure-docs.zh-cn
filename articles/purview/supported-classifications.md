---
title: 支持的分类的列表
description: 此页列出 Azure Purview 中支持的系统分类。
author: anmuk601
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 4/1/2021
ms.openlocfilehash: e1d3d495d958465e966701aa7ce91bc2706b48e0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219580"
---
# <a name="supported-classifications-in-azure-purview"></a>Azure Purview 中支持的分类

本文列出 Azure Purview（预览版）中支持和定义的系统分类。


- **非重复匹配阈值**：在扫描程序运行数据模式前需要在列中找到的非重复数据值的总数。 非重复匹配阈值与模式匹配无关，但它是模式匹配的先决条件。 系统分类规则要求每列至少有 8 个不同的值，以便对它们分类。 系统需要此值来确保每列都包含足够的数据，以便扫描程序可以对其进行精确分类。 例如，不会对多行都包含值 1 的列进行分类。 也不会对一行包含值而其余行包含 null 值的列进行分类。 如果指定多种模式，此值会应用于每个模式。

- **最小匹配阈值**：扫描程序必须在列中找到的待应用分类的数据值匹配的最小百分比。 系统分类值设置为 60%。


## <a name="defined-system-classifications"></a>定义的系统分类

Azure Purview 通过 [RegEx](https://wikipedia.org/wiki/Regular_expression) 和 [Bloom 筛选器](https://wikipedia.org/wiki/Bloom_filter)来对数据进行分类。 下面的各个列表介绍 Azure Purview 定义的系统分类的格式、模式和关键字。

每个分类名称都以 Microsoft 为前缀。

## <a name="bloom-filter-classifications"></a>Bloom 筛选器分类

## <a name="city-country-and-place"></a>国家/地区、城市和地点

国家/地区、城市和地点筛选器是使用可用于准备数据的最佳数据集准备的。

## <a name="person"></a>人员

人员 Bloom 筛选器是使用以下两个数据集准备的。

- [2010 年美国人口普查姓氏数据（16.2 万条）](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [热门婴儿名字（来自 SSN），使用的是 1880 到 2019 年的数据（9.8 万条）](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>RegEx 分类

## <a name="aba-routing"></a>ABA 路由

### <a name="format"></a>格式

采用有格式或无格式模式的 9 位数字

### <a name="pattern"></a>模式

有格式：

- 以 0、1、2、3、6、7 或 8 开头的 4 位数字
- 1 个连字符
- 4 位数字
- 1 个连字符
- 1 位数字

无格式：以 0、1、2、3、6、7 或 8 开头的连续的 9 位数

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

## <a name="argentina-national-identity-dni-number"></a>阿根廷国民身份证 (DNI) 号码

### <a name="format"></a>格式

带或不带句点的 8 位数字

### <a name="pattern"></a>模式

8 位数字：

- 2 位数字
- （可选）1 个句点
- 3 位数字
- （可选）1 个句点
- 3 位数字

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

带或不带银行州分行号码的 6 到 10 位数字

### <a name="pattern"></a>模式

帐号是 6 到 10 位数字。

澳大利亚州分行号码：

- 3 位数字
- 1 个连字符
- 3 位数字

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

## <a name="australia-drivers-license-number"></a>澳大利亚驾照编号

### <a name="format"></a>格式
字母和数字共 9 位

### <a name="pattern"></a>模式
字母和数字共 9 位：

- 2 位数字或字母（不区分大小写）
- 2 位数字
- 5 位数字或字母（不区分大小写）

OR

- （可选）1 到 2 位字母，不区分大小写
- 4 到 9 位数字

OR

- 9 位数字或字母（不区分大小写）

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

## <a name="australian-medicare-number"></a>澳大利亚医疗保险号码

### <a name="format"></a>格式

10 到 11 位数字

### <a name="pattern"></a>模式

10 到 11 位数字：

- 第 1 位数字的范围为 2 到 6
- 第 9 位数字是校验数字
- 第 10 位数字是发行数字
- 第 11 位数字（可选）是个人号码

### <a name="keywords"></a>关键字

#### <a name="keyword_australia_medicare_number"></a>Keyword_Australia_Medicare_Number

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

## <a name="australia-passport-number"></a>澳大利亚护照号码

### <a name="format"></a>格式

以字母开头，后接 7 位数字

### <a name="pattern"></a>模式

以字母开头（不区分大小写），后接 7 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_passport"></a>Keyword\_passport

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

#### <a name="keyword_australia_passport_number"></a>Keyword\_australia\_passport\_number

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

## <a name="australia-tax-file-number"></a>澳大利亚税文件号

### <a name="format"></a>格式

8 到 9 位数字

### <a name="pattern"></a>模式

通常带有空格的 8 到 9 位数字，如下所示：

- 3 位数字
- （可选）1 个空格
- 3 位数字
- （可选）1 个空格
- 2 到 3 位数字，最后一位是校验数字

### <a name="keywords"></a>关键字

#### <a name="keyword_australia_tax_file_number"></a>Keyword\_australia\_tax\_file\_number

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

## <a name="belgium-national-number"></a>比利时国民身份证号码

### <a name="format"></a>格式

11 位数字加上可选分隔符

### <a name="pattern"></a>模式

11 位数字加上可选分隔符：

- 出生日期的格式为 YY.MM.DD，即 6 位数字和 2 个可选句点
- （可选）分隔符，可以是点、短划线或空格
- 3 位连续的数字（奇数表示男性，偶数表示女性）
- （可选）分隔符，可以是点、短划线或空格
- 2 个校验数字

### <a name="keywords"></a>关键字

#### <a name="keyword_belgium_national_number"></a>Keyword\_belgium\_national\_number

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

## <a name="brazil-cpf-number"></a>巴西 CPF 号码

### <a name="format"></a>格式

带或不带格式的 11 位数字，其中包括 1 个校验数字

### <a name="pattern"></a>模式

有格式：

- 3 位数字
- 1 个句点
- 3 位数字
- 1 个句点
- 3 位数字
- 1 个连字符
- 2 位数字，均为校验数字

未格式化：

- 11 位数字，最后 2 位为校验数字

### <a name="keywords"></a>关键字

#### <a name="keyword_brazil_cpf"></a>Keyword\_brazil\_cpf

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

## <a name="brazil-legal-entity-number-cnpj"></a>巴西法人号码 (CNPJ)

### <a name="format"></a>格式

14 位数字（包括注册号码、分支机构号码和校验数字）加上分隔符

### <a name="pattern"></a>模式

14 位数字加上分隔符：

- 2 位数字
- 1 个句点
- 3 位数字
- 1 个句点
- 3 位数字（前八位数为注册号码）
- 1 个正斜杠
- 表示分支机构号码的 4 位数字
- 1 个连字符
- 2 位数字，均为校验数字

### <a name="keywords"></a>关键字

#### <a name="keyword_brazil_cnpj"></a>Keyword\_brazil\_cnpj

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

## <a name="brazil-national-identification-card-rg"></a>巴西国民身份证号码 (RG)

### <a name="format"></a>格式

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>模式

:::no-loc text="Registro Geral (old format):":::

- 2 位数字
- 1 个句点
- 3 位数字
- 1 个句点
- 3 位数字
- 1 个连字符
- 1 位数字（为校验数字）

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 位
- 1 个连字符
- 1 位数字（为校验数字）

### <a name="keywords"></a>关键字

#### <a name="keyword_brazil_rg"></a>Keyword\_brazil\_rg

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

7 或 12 位数

### <a name="pattern"></a>模式

加拿大银行帐号为 7 或 12 位数。

加拿大银行帐户交易号码为：

- 5 位数字
- 1 个连字符
- 3 位数字或者
- 一个零 (0)
- 8 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_canada_bank_account_number"></a>Keyword\_canada\_bank\_account\_number

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

## <a name="canada-drivers-license-number"></a>加拿大驾照编号

### <a name="format"></a>格式

因省份而异

### <a name="pattern"></a>模式

包括亚伯达、不列颠哥伦比亚、马尼托巴、新不伦瑞克、纽芬兰/拉布拉多、新斯科舍、安大略、爱德华王子岛、魁北克和萨斯喀彻温的各种模式

### <a name="keywords"></a>关键字

#### <a name="keyword_province_name_drivers_license_name"></a>Keyword\_[province\_name]\_drivers\_license\_name

- 省的缩写，例如 AB
- 省的名称，例如 Alberta

#### <a name="keyword_canada_drivers_license"></a>Keyword\_canada\_drivers\_license

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

## <a name="canada-health-service-number"></a>加拿大医疗服务号码

### <a name="format"></a>格式

10 位

### <a name="pattern"></a>模式

10 位

### <a name="keywords"></a>关键字

#### <a name="keyword_canada_health_service_number"></a>Keyword\_canada\_health\_service\_number

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

## <a name="canada-passport-number"></a>加拿大护照号码

### <a name="format"></a>格式

2 位大写字母后接 6 位数字

### <a name="pattern"></a>模式

2 位大写字母后接 6 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_canada_passport_number"></a>Keyword\_canada\_passport\_number

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

#### <a name="keyword_passport"></a>Keyword\_passport

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

9 位数字

### <a name="pattern"></a>模式

9 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_canada_phin"></a>Keyword\_canada\_phin

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

#### <a name="keyword_canada_provinces"></a>Keyword\_canada\_provinces

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

## <a name="canada-social-insurance-number"></a>加拿大社会保险号码

### <a name="format"></a>格式

9 位数，带不带连字符或空格均可

### <a name="pattern"></a>模式

有格式：

- 3 位数字
- 1 个连字符或空格
- 3 位数字
- 1 个连字符或空格
- 3 位数字

无格式：9 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_sin"></a>Keyword\_sin

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

#### <a name="keyword_sin_collaborative"></a>Keyword\_sin\_collaborative

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

## <a name="chile-identity-card-number"></a>智利身份证号码

### <a name="format"></a>格式

7 到 8 位数加上分隔符，其中包括 1 位校验数字或字母

### <a name="pattern"></a>模式

7 到 8 位数加上分隔符：

- 1 到 2 位数字
- （可选）1 个句点
- 3 位数字
- （可选）1 个句点
- 3 位数字
- 1 个短划线
- 1 个数字或字母（不区分大小写），为校验数位

### <a name="keywords"></a>关键字

#### <a name="keyword_chile_id_card"></a>Keyword\_chile\_id\_card

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

## <a name="china-resident-identity-card-prc-number"></a>中国居民身份证 (PRC) 号码

### <a name="format"></a>格式

18 位数字

### <a name="pattern"></a>模式

18 位数字：

- 表示地址码的 6 位数字
- 表示出生日期、格式为 YYYYMMDD 的 8 位数字
- 表示顺序码的 3 位数字
- 1 位数字（为校验数字）

### <a name="keywords"></a>关键字

#### <a name="keyword_china_resident_id"></a>Keyword\_china\_resident\_id

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

有格式或无格式并且必须通过 Luhn 测试的 14 到 16 位数。

### <a name="pattern"></a>模式

一种复杂且可靠的模式，检测全球所有主要品牌的卡，包括维萨卡、万事达卡、发现卡、日财卡、美国运通卡等信用卡以及礼物卡和餐卡。

### <a name="keywords"></a>关键字

#### <a name="keyword_cc_verification"></a>Keyword\_cc\_verification

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

#### <a name="keyword_cc_name"></a>Keyword\_cc\_name

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

## <a name="croatia-drivers-license-number"></a>克罗地亚驾照编号

仅在欧盟的驾照编号敏感信息类型中提供此敏感信息类型实体。

### <a name="format"></a>格式

不带空格和分隔符的 8 位数字

### <a name="pattern"></a>模式

8 位数字

### <a name="keywords"></a>关键字

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

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

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Keywords\_croatia\_eu\_driver's\_license\_number

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>克罗地亚身份证号码

此敏感信息类型实体纳入了欧盟的国民身份证号码敏感信息类型，可作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

9 位数字

### <a name="pattern"></a>模式

连续的 9 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_croatia_id_card"></a>Keyword\_croatia\_id\_card

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

## <a name="croatia-personal-identification-oib-number"></a>克罗地亚个人身份 (OIB) 编号

### <a name="format"></a>格式

11 位数字

### <a name="pattern"></a>模式

11 位数字：

- 10 位
- 第 9 位是校验数字

### <a name="keywords"></a>关键字

#### <a name="keyword_croatia_oib_number"></a>Keyword\_croatia\_oib\_number

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

## <a name="denmark-personal-identification-number"></a>丹麦个人身份编号

### <a name="format"></a>格式

包含 1 个连字符的 10 位数

### <a name="pattern"></a>模式

10 位数：

- 表示出生日期、格式为 DDMMYY 的 6 位数字
- 1 个连字符
- 4 位数字，最后 1 位是校验数字

### <a name="keywords"></a>关键字

#### <a name="keyword_denmark_id"></a>Keyword\_denmark\_id

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

## <a name="eu-debit-card-number"></a>欧盟借记卡号码

### <a name="format"></a>格式

16 位数字

### <a name="pattern"></a>模式

复杂且可靠的模式

### <a name="keywords"></a>关键字

#### <a name="keyword_eu_debit_card"></a>Keyword\_eu\_debit\_card

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Keyword\_card\_terms\_dict

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

#### <a name="keyword_card_security_terms_dict"></a>Keyword\_card\_security\_terms\_dict

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

#### <a name="keyword_card_expiration_terms_dict"></a>Keyword\_card\_expiration\_terms\_dict

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

## <a name="eu-drivers-license-number"></a>欧盟驾照号码

以下是欧盟驾照编号敏感信息类型中的实体。

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

## <a name="eu-national-identification-number"></a>欧盟国家标识号

以下是欧盟国民身份证号码敏感信息类型中的实体。

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

## <a name="eu-passport-number"></a>欧盟护照号码

以下是欧盟护照号码敏感信息类型和欧盟护照号码包中的实体。

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

## <a name="eu-social-security-number-or-equivalent-identification"></a>欧盟社会安全号码或等效身份证号码

以下是欧盟社会安全号码或等效身份证号码敏感信息类型中的实体。

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

## <a name="eu-tax-identification-number"></a>欧盟税务识别号

以下是欧盟税务识别号敏感信息类型中的实体。

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



## <a name="finland-national-id"></a>芬兰国民身份证号码

### <a name="format"></a>格式

6 位数字加上 1 个表示世纪的字符、3 位数字和 1 个校验数字

### <a name="pattern"></a>模式

模式必须包含下面所有的项：

- 表示出生日期、格式为 DDMMYY 的 6 位数字
- 世纪标记（-、+ 或 a）
- 表示个人身份编号的 3 位数字
- 1 个校验数位（为数字或区分大小写的字母）

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

此敏感信息类型实体在欧盟护照号码敏感信息类型中提供，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

字母和数字共 9 位

### <a name="pattern"></a>模式

字母和数字共 9 位：

- 2 个字母（不区分大小写）
- 7 位数字

### <a name="keywords"></a>关键字

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common

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

#### <a name="keyword_finland_passport_number"></a>Keyword\_finland\_passport\_number

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>法国驾照编号

此敏感信息类型实体在欧盟驾照编号敏感信息类型中提供，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

12 位数

### <a name="pattern"></a>模式

12 位数，带有用于排除法国电话号码等类似模式的校验数字

### <a name="keywords"></a>关键字

#### <a name="keyword_french_drivers_license"></a>Keyword\_french\_drivers\_license

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

## <a name="france-national-id-card-cni"></a>法国国民身份证 (CNI)

### <a name="format"></a>格式

12 位数

### <a name="pattern"></a>模式

12 位数

### <a name="keywords"></a>关键字

#### <a name="keywords_france_eu_national_id_card"></a>Keywords\_france\_eu\_national\_id\_card

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

## <a name="france-passport-number"></a>法国护照号码

此敏感信息类型实体在欧盟护照号码敏感信息类型中提供，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

数字和字母共 9 位

### <a name="pattern"></a>模式

数字和字母共 9 位：

- 2 位数字
- 2 个字母（不区分大小写）
- 5 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_passport"></a>Keyword\_passport

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

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>法国社会安全号码 (INSEE) 或等效身份证号码

此敏感信息类型实体纳入了欧盟社会安全号码和等效身份证号码敏感信息类型，并可作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

15 位

### <a name="pattern"></a>模式

必须匹配以下两种模式之一：

- 13 位数字，后接 1 个空格，再接 2 位数字；或者
- 15 位连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_fr_insee"></a>Keyword\_fr\_insee

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

## <a name="germany-drivers-license-number"></a>德国驾照编号

此敏感信息类型实体纳入了欧盟驾照编号敏感信息类型，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

数字和字母共 11 位

### <a name="pattern"></a>模式

11 位数字和字母（不区分大小写）：

- 1 位数字或字母
- 2 位数字
- 6 位数字或字母
- 1 位数字
- 1 位数字或字母

### <a name="keywords"></a>关键字

#### <a name="keyword_german_drivers_license_number"></a>Keyword\_german\_drivers\_license\_number

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

## <a name="germany-identity-card-number"></a>德国身份证号码

### <a name="format"></a>格式

自 2010 年 11 月 1 日起：9 位字母和数字

1987 年 4 月1 日至 2010 年 10 月 31 日：10 位数字

### <a name="pattern"></a>模式

自 2010 年 11 月 1 日起：

- 1 个字母（不区分大小写）
- 8 位数字

1987 年 4 月1 日至 2010 年 10 月 31 日：

- 10 位

### <a name="keywords"></a>关键字

#### <a name="keyword_germany_id_card"></a>Keyword\_germany\_id\_card

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

## <a name="germany-passport-number"></a>德国护照号码

此敏感信息类型实体纳入了欧盟护照号码敏感信息类型，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

10 位数字或字母

### <a name="pattern"></a>模式

模式必须包含下面所有的项：

- 第 1 个字符是数字或此集（C、F、G、H、J、K）中的字母
- 3 位数字
- 5 位数字或 5 位来自此集（C、H、J-N、P、R、T、V-Z）中的字母
- 1 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_german_passport"></a>Keyword\_german\_passport

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

## <a name="greece-national-id-card"></a>希腊国民身份证号码

### <a name="format"></a>格式

7 到 8 个字母和数字，加上 1 个短划线

### <a name="pattern"></a>模式

7 个字母和数字（旧格式）：

- 1 个字母（希腊字母表中的任何字母）
- 1 个短划线
- 6 位数字

8 位字母和数字（新格式）：

- 2 个字母，希腊和拉丁字母表中均有其大写形式（ABEZHIKMNOPTYX）
- 1 个短划线
- 6 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_greece_id_card"></a>Keyword\_greece\_id\_card

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

## <a name="hong-kong-identity-card-hkid-number"></a>香港特别行政区身份证 (HKID) 号码

### <a name="format"></a>格式

8 到 9 位字母和数字，可选择在最后一个字符外加上括号

### <a name="pattern"></a>模式

字母和数字共 8 到 9 位：

- 1 到 2 个字母（不区分大小写）
- 6 位数字
- 表示校验数字的最后一个字符（任何数字或字母 A），可以选择用括号括起来。

### <a name="keywords"></a>关键字

#### <a name="keyword_hong_kong_id_card"></a>Keyword\_hong\_kong\_id\_card

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

#### <a name="ipv4"></a>IPv4：

复杂模式，构成 IPv4 地址有格式（有句点）和无格式（无句点）的版本

#### <a name="ipv6"></a>IPv6：

复杂模式，构成有格式的 IPv6 号码（包括冒号）

### <a name="pattern"></a>模式

### <a name="keywords"></a>关键字

#### <a name="keyword_ipaddress"></a>Keyword\_ipaddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>印度永久帐号 (PAN)

### <a name="format"></a>格式

字母和数字共 10 位

### <a name="pattern"></a>模式

字母和数字共 10 位：

- 3 个字母（不区分大小写）
- C、P、H、F、A、T、B、L、J、G 中的任一字母（不区分大小写）
- 1 个字母
- 四位数字
- 1 个字母（不区分大小写）

### <a name="keywords"></a>关键字

#### <a name="keyword_india_permanent_account_number"></a>Keyword\_india\_permanent\_account\_number

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>印度唯一身份 (Aadhaar) 号码

### <a name="format"></a>格式

包含可选空格或短划线的 12 位数字

### <a name="pattern"></a>模式

12 位数字：

- 1 位数字，不是 0 或 1
- 3 位数字
- （可选）1 个空格或短划线
- 四位数字
- （可选）1 个空格或短划线
- 最后 1 位是校验数字

### <a name="keywords"></a>关键字

#### <a name="keyword_india_aadhar"></a>Keyword\_india\_aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>印度尼西亚身份证 (KTP) 号码

### <a name="format"></a>格式

包含可选句点的 16 位数字

### <a name="pattern"></a>模式

16 位数字：

- 2 位数的省份代码
- 1 个句点（可选）
- 表示市县代码的 2 位数字
- 表示区的 2 位数字
- 1 个句点（可选）
- 表示出生日期、格式为 DDMMYY 的 6 位数字
- 1 个句点（可选）
- 四位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_indonesia_id_card"></a>Keyword\_indonesia\_id\_card

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>国际银行帐号 (IBAN)

### <a name="format"></a>格式

国家/地区代码（2 个字母），加上校验数字（两位数），再加上 :::no-loc text="bban"::: 号码（最多 30 个字符）

### <a name="pattern"></a>模式

模式必须包含下面所有的项：

- 表示国家/地区代码的 2 个字母
- 2 个校验数字（可选择后跟一个空格）
- 1 到 7 组 4 位字母或数字（可用空格分隔）
- 1 到 3 个字母或数字

每个国家/地区的格式略有不同。 IBAN 敏感信息类型包括以下 60 个国家/地区：

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>关键字

无

## <a name="ireland-personal-public-service-pps-number"></a>爱尔兰个人公共服务 (PPS) 号码

### <a name="format"></a>格式

旧格式（截止于 2012 年 12 月 31 日）：

- 7 位数字，后接 1 到 2 个字母

新格式（起始于 2013 年 1 月 1 日）：

- 7 位数字，后接 2 个字母

### <a name="pattern"></a>模式

旧格式（截止于 2012 年 12 月 31 日）：

- 7 位数字
- 1 到 2 个字母（不区分大小写）

新格式（起始于 2013 年 1 月 1 日）：

- 7 位数字
- 1 个字母（不区分大小写），为校验字母
- A 到 I 中一个字母（可选）或 &quot;W&quot;

### <a name="keywords"></a>关键字

#### <a name="keywords_ireland_eu_national_id_card"></a>Keywords\_ireland\_eu\_national\_id\_card

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

13 位数字

### <a name="pattern"></a>模式

有格式：

- 2 位数字
- 1 个短划线
- 3 位数字
- 1 个短划线
- 8 位数字

未格式化：

- 13 位连续的数字

### <a name="keywords"></a>关键字

#### <a name="keyword_israel_bank_account_number"></a>Keyword\_israel\_bank\_account\_number

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>以色列国民身份证号码

### <a name="format"></a>格式

9 位数字

### <a name="pattern"></a>模式

连续的 9 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_israel_national_id"></a>Keyword\_Israel\_National\_ID

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

## <a name="italy-drivers-license-number"></a>意大利驾照编号

此敏感信息类型实体纳入了欧盟驾照编号敏感信息类型，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

字母和数字共 10 位

### <a name="pattern"></a>模式

字母和数字共 10 位：

- 1 个字母（不区分大小写）
- 字母 A 或 V（不区分大小写）
- 7 位数字
- 1 个字母（不区分大小写）

### <a name="keywords"></a>关键字

#### <a name="keyword_italy_drivers_license_number"></a>Keyword\_italy\_drivers\_license\_number

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>日本银行帐号

### <a name="format"></a>格式

7 位或 8 位数字

### <a name="pattern"></a>模式

银行帐号：

- 7 位或 8 位数字
- 银行帐户分行代码：
- 4 位数字
- 1 个空格或短划线（可选）
- 3 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_jp_bank_account"></a>Keyword\_jp\_bank\_account

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

#### <a name="keyword_jp_bank_branch_code"></a>Keyword\_jp\_bank\_branch\_code

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>日本驾照编号

### <a name="format"></a>格式

12 位数

### <a name="pattern"></a>模式

12 位连续的数字

### <a name="keywords"></a>关键字

#### <a name="keyword_jp_drivers_license_number"></a>Keyword\_jp\_drivers\_license\_number

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

2 个字母，后接 7 位数字

### <a name="pattern"></a>模式

2 个字母（不区分大小写），后接 7 位数字

#### <a name="keyword_jp_passport"></a>Keyword\_jp\_passport

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

## <a name="japan-residence-card-number"></a>日本居住证号码

### <a name="format"></a>格式

字母和数字共 12 位

### <a name="pattern"></a>模式

字母和数字共 12 位：

- 2 个字母（不区分大小写）
- 8 位数字
- 2 个字母（不区分大小写）

### <a name="keywords"></a>关键字

#### <a name="keyword_jp_residence_card_number"></a>Keyword\_jp\_residence\_card\_number

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>日本居民注册号码

### <a name="format"></a>格式

11 位数字

### <a name="pattern"></a>模式

11 位连续的数字

### <a name="keywords"></a>关键字

#### <a name="keyword_jp_resident_registration_number"></a>Keyword\_jp\_resident\_registration\_number

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

## <a name="japan-social-insurance-number-sin"></a>日本社会保险号码 (SIN)

### <a name="format"></a>格式

7 到 12 位数字

### <a name="pattern"></a>模式

7 到 12 位数字：

- 4 位数字
- 1 个连字符（可选）
- 6 位数字或者
- 7 到 12 位的连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_jp_sin"></a>Keyword\_jp\_sin

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

## <a name="malaysia-identification-card-number"></a>马来西亚身份证号码

### <a name="format"></a>格式

包含可选连字符的 12 位数字

### <a name="pattern"></a>模式

12 位数字：

- 表示出生日期、格式为 YYMMDD 的 6 位数字
- 1 个短划线（可选）
- 表示出生地代码的 2 个字母
- 1 个短划线（可选）
- 3 个随机数字
- 表示性别码的 1 个数字

### <a name="keywords"></a>关键字

#### <a name="keyword_malaysia_id_card_number"></a>Keyword\_malaysia\_id\_card\_number

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

## <a name="netherlands-citizens-service-bsn-number"></a>荷兰公民服务 (BSN) 编号

### <a name="format"></a>格式

包含可选空格的 8 到 9 位数

### <a name="pattern"></a>模式

8 到 9 位数字：

- 3 位数字
- 1 个空格（可选）
- 3 位数字
- 1 个空格（可选）
- 2 到 3 位数字

### <a name="keywords"></a>关键字

#### <a name="keywords_netherlands_eu_national_id_card"></a>Keywords\_netherlands\_eu\_national\_id\_card

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

## <a name="new-zealand-ministry-of-health-number"></a>新西兰卫生部编号

### <a name="format"></a>格式

3 个字母、1 个空格（可选）和 4 位数字

### <a name="pattern"></a>模式

- 3 个字母（不区分大小写），I 和 O 除外
- 1 个空格（可选）
- 4 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_nz_terms"></a>Keyword\_nz\_terms

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

## <a name="norway-identification-number"></a>挪威身份证号码

### <a name="format"></a>格式

11 位数字

### <a name="pattern"></a>模式

11 位数字：

- 表示出生日期、格式为 DDMMYY 的 6 位数字
- 表示个人号码的 3 位数字
- 2 个校验数字

### <a name="keywords"></a>关键字

#### <a name="keyword_norway_id_number"></a>Keyword\_norway\_id\_number

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>菲律宾统一多用途身份证号码

### <a name="format"></a>格式

由连字符分隔的 12 位数字

### <a name="pattern"></a>模式

12 位数字：

- 4 位数字
- 1 个连字符
- 7 位数字
- 1 个连字符
- 1 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_philippines_id"></a>Keyword\_philippines\_id

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>波兰身份证号码

### <a name="format"></a>格式

3 个字母和 6 位数字

### <a name="pattern"></a>模式

3 个字母（不区分大小写），后接 6 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>波兰国民身份证 (PESEL) 号码

### <a name="format"></a>格式

11 位数字

### <a name="pattern"></a>模式

- 表示出生日期、格式为 YYMMDD 的 6 位数字
- 4 位数字
- 1 个校验数字

### <a name="keywords"></a>关键字

#### <a name="keyword_pesel_identification_number"></a>Keyword\_pesel\_identification\_number

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

## <a name="poland-passport-number"></a>波兰护照号码

此敏感信息类型实体纳入了欧盟护照号码敏感信息类型，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

2 个字母和 7 个数字

### <a name="pattern"></a>模式

2 个字母（不区分大小写），后接 7 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>葡萄牙公民卡编号

### <a name="format"></a>格式

8 位数字

### <a name="pattern"></a>模式

8 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_portugal_citizen_card"></a>Keyword\_portugal\_citizen\_card

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

## <a name="portugal-drivers-license-number"></a>葡萄牙驾照编号

仅在欧盟的驾照编号敏感信息类型中提供此敏感信息类型实体。

### <a name="format"></a>格式

两种模式：2 个字母，后接 5 到 8 位数字，并带有特殊字符

### <a name="pattern"></a>模式

模式 1：2 个字母，后接 5 或 6 位数字，并带有特殊字符：

- 2 个字母（不区分大小写）
- 连字符
- 5 或 6 位数字
- 1 个空格
- 1 位数字

模式 2：1 个字母，后跟 6 或 8 位数字，并带有特殊字符：

- 1 个字母（不区分大小写）
- 连字符
- 6 位或 8 位数字
- 1 个空格
- 1 位数字

### <a name="keywords"></a>关键字

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

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

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Keywords\_portugal\_eu\_driver's\_license\_number

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

10 位连续的数字

### <a name="keywords"></a>关键字

#### <a name="keyword_saudi_arabia_national_id"></a>Keyword\_saudi\_arabia\_national\_id

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>新加坡国家登记身份证 (NRIC) 号码

### <a name="format"></a>格式

字母和数字共 9 位

### <a name="pattern"></a>模式

- 字母和数字共 9 位：
- 字母 F、G、S 或 T（不区分大小写）
- 7 位数字
- 1 个校验字母

### <a name="keywords"></a>关键字

#### <a name="keyword_singapore_nric"></a>Keyword\_singapore\_nric

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

## <a name="south-africa-identification-number"></a>南非身份证号码

### <a name="format"></a>格式

可能包含空格的 13 位数字

### <a name="pattern"></a>模式

13 位数字：

- 表示出生日期、格式为 YYMMDD 的 6 位数字
- 4 位数字
- 表示公民身份的 1 位数字
- 数字 8 或 9
- 1 位表示校验和的数字

### <a name="keywords"></a>关键字

#### <a name="keyword_south_africa_identification_number"></a>Keyword\_south\_africa\_identification\_number

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>韩国居民注册号码

### <a name="format"></a>格式

包含连字符的 13 位数字

### <a name="pattern"></a>模式

13 位数字：

- 表示出生日期、格式为 YYMMDD 的 6 位数字
- 1 个连字符
- 根据世纪和性别确定的 1 位数字
- 表示出生区域的 4 位数字
- 用于与前面数字相同的人进行区分的 1 位数字
- 1 个校验数字。

### <a name="keywords"></a>关键字

#### <a name="keyword_south_korea_resident_number"></a>Keyword\_south\_korea\_resident\_number

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>西班牙社会安全号码 (SSN)

此敏感信息类型实体纳入了欧盟社会安全号码或等效身份证号码敏感信息类型，并可作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

11 到 12 位数字

### <a name="pattern"></a>模式

11 到 12 位数字：

- 2 位数字
- 1 个正斜杠（可选）
- 7 到 8 位数字
- 1 个正斜杠（可选）
- 2 位数字

### <a name="keywords"></a>关键字

无

## <a name="sweden-national-id"></a>瑞典国民身份证

### <a name="format"></a>格式

10 或 12 位数字和 1 个可选分隔符

### <a name="pattern"></a>模式

10 或 12 位数字和 1 个可选分隔符：

- 两位数字（可选）
- 日期格式为 YYMMDD 的 6 位数字
- 分隔符 - 或 +（可选）
- 4 位数字

### <a name="keywords"></a>关键字

#### <a name="keywords_swedish_national_identifier"></a>Keywords\_swedish\_national\_identifier

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

## <a name="sweden-passport-number"></a>瑞典护照号码

此敏感信息类型实体纳入了欧盟护照号码敏感信息类型，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

8 位数字

### <a name="pattern"></a>模式

8 位连续的数字

### <a name="keywords"></a>关键字

#### <a name="keyword_sweden_passport"></a>Keyword\_sweden\_passport

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

#### <a name="keyword_passport"></a>Keyword\_passport

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

4 个字母，后接 5 到 31 个字母或数字

### <a name="pattern"></a>模式

4 个字母，后接 5 到 31 个字母或数字：

- 表示银行代码的 4 个字母（不区分大小写）
- （可选）1 个空格
- 4 到 28 个字母或数字（基本银行帐号 (BBAN)）
- （可选）1 个空格
- 1 到 3 个字母或数字（BBAN 的剩余部分）

### <a name="keywords"></a>关键字

#### <a name="keyword_swift"></a>Keyword\_swift

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

## <a name="taiwan-national-identification-number"></a>台湾身份证号码

### <a name="format"></a>格式

1 个英文字母，后接 9 位数字

### <a name="pattern"></a>模式

1 个英文字母，后接 9 位数字：

- 1 个英文字母（不区分大小写）
- 数字 1 或 2
- 8 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_taiwan_national_id"></a>Keyword\_taiwan\_national\_id

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

- 电子护照号码：9 位数字
- 非电子护照号码：9 位数字

### <a name="pattern"></a>模式

电子护照号码：

- 字符 3
- 8 位数字

非电子护照号码：

- 9 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_taiwan_passport"></a>Keyword\_taiwan\_passport

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

## <a name="taiwan-resident-certificate-arctarc-number"></a>台湾居住证 (ARC/TARC) 号码

### <a name="format"></a>格式

字母和数字共 10 位

### <a name="pattern"></a>模式

字母和数字共 10 位：

- 2 个字母（不区分大小写）
- 8 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_taiwan_resident_certificate"></a>Keyword\_taiwan\_resident\_certificate

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

## <a name="uk-drivers-license-number"></a>英国 驾照编号

此敏感信息类型实体纳入了欧盟驾照编号敏感信息类型，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

字母和数字共 18 位，采用指定的格式

### <a name="pattern"></a>模式

字母和数字共 18 位：

- 5 个字母（不区分大小写），或者用数字 9 代替其中 1 个字母
- 1 位数字
- 表示出生日期，格式为 MMDDY 的 5 位数字（如果驾驶员为女性，则第 7 个字符加上 50，也就是说表示月份的两个数字为 51 到 62，而不是 01 到 12）
- 2 个字母（不区分大小写），或者用数字 9 代替其中 1 个字母
- 5 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_uk_drivers_license"></a>Keyword\_uk\_drivers\_license

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

## <a name="uk-electoral-roll-number"></a>英国 选举名册编号

### <a name="format"></a>格式

2 个字母，后接 1 到 4 位数字

### <a name="pattern"></a>模式

2 个字母（不区分大小写），后接 1 到 4 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_uk_electoral"></a>Keyword\_uk\_electoral

- 委员会提名
- 提名表单
- 选民登记册
- 选民名册

## <a name="uk-national-health-service-number"></a>英国 国家医疗服务号码

### <a name="format"></a>格式

由空格分隔的 10 到 17 位数字

### <a name="pattern"></a>模式

10 到 17 位数字：

- 3 或 10 位数字
- 1 个空格
- 3 位数字
- 1 个空格
- 4 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_uk_nhs_number"></a>Keyword\_uk\_nhs\_number

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Keyword\_uk\_nhs\_number1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Keyword\_uk\_nhs\_number\_dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>英国 国家保险号码 (NINO)

此敏感信息类型实体纳入了欧盟的国民身份证号码敏感信息类型，可作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

由空格或短划线分隔的 7 个或 9 个字符

### <a name="pattern"></a>模式

两种可能的模式：

- 2 个字母（有效 NINO 只在此前缀中使用此模式会验证的某些字母；不区分大小写）
- 6 位数字
- A、B、C 或 D（同前缀一样，在后缀中只允许使用这些特定的字符；不区分大小写）

OR

- 2 个字母
- 1 个空格或短划线
- 2 位数字
- 1 个空格或短划线
- 2 位数字
- 1 个空格或短划线
- 2 位数字
- 1 个空格或短划线
- A、B、C 或 D


### <a name="keywords"></a>关键字

#### <a name="keyword_uk_nino"></a>Keyword\_uk\_nino

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

此敏感信息类型仅供在以下场景中使用：

- 数据丢失防护策略
- 通信合规性策略
- 信息治理
- 记录管理
- Microsoft Cloud App Security

### <a name="format"></a>格式

不带空格和分隔符的 10 位数字

### <a name="pattern"></a>模式

10 位

### <a name="keywords"></a>关键字

#### <a name="keywords_uk_eu_tax_file_number"></a>Keywords\_uk\_eu\_tax\_file\_number

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

6 到 17 位数字

### <a name="pattern"></a>模式

6 到 17 位连续数字

### <a name="keywords"></a>关键字

#### <a name="keyword_usa_bank_account"></a>Keyword\_usa\_Bank\_Account

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

## <a name="us-drivers-license-number"></a>美国驾照编号

### <a name="format"></a>格式

取决于所在的州

### <a name="pattern"></a>模式

取决于所在的州，例如纽约州：

- 按 ddd 分组的 9 位数字与模式匹配。
- 格式为 ddddddddd 的 9 位数字与模式不匹配。

### <a name="keywords"></a>关键字

#### <a name="keyword_us_drivers_license_abbreviations"></a>Keyword\_us\_drivers\_license\_abbreviations

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

#### <a name="keyword_us_drivers_license"></a>Keyword\_us\_drivers\_license

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

#### <a name="keyword_state_name_drivers_license_name"></a>Keyword\_[state\_name]\_drivers\_license\_name

- 州名缩写（例如 &quot;NY&quot;）
- 州名（例如 &quot;New York&quot;）

## <a name="us-individual-taxpayer-identification-number-itin"></a>美国个人纳税人标识号 (ITIN)

### <a name="format"></a>格式

9 位数字，以 9 开头，7 或 8 作为第 4 位数字，可选择使用空格或短划线设置格式

### <a name="pattern"></a>模式

有格式：

- 数字 9
- 2 位数字
- 1 个空格或短划线
- 数字 7 或 8
- 1 位数字
- 1 个空格或短划线
- 4 位数字

无格式：

- 数字 9
- 2 位数字
- 数字 7 或 8
- 5 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_itin"></a>Keyword\_itin

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

## <a name="us-social-security-number-ssn"></a>美国社会安全号码 (SSN)

### <a name="format"></a>格式

9 位数字，可能为有格式或者无格式的模式

>[!Note]
> 如果是在 2011 年年中之前发行的，SSN 的格式非常严格，号码的某些部分必须在特定范围内才有效（但是没有校验和）。
>

### <a name="pattern"></a>模式

四个函数分别以四种不同模式查找 SSN：

- Func\_ssn 查找具有 2011 年前严格的格式，使用短划线或空格设置的 SSN（ddd-dd-dddd 或 ddd dd dddd）
- Func\_unformatted\_ssn 查找具有 2011 年前的严格格式，连续 9 位数字的无格式 SSN（ddddddddd）
- Func\_randomized\_formatted\_ssn 查找 2011 年之后使用短划线或空格设置格式的 SSN（ddd-dd-dddd 或 ddd dd dddd）
- Func\_randomized\_unformatted\_ssn 查找 2011 年之后连续 9 位数字的无格式 SSN（ddddddddd）

### <a name="keywords"></a>关键字

#### <a name="keyword_ssn"></a>Keyword\_ssn

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

## <a name="us--uk-passport-number"></a>美国/英国 护照号码

英国 护照号码敏感信息类型实体在欧盟护照号码敏感信息类型中提供，并作为独立的敏感信息类型实体提供。

### <a name="format"></a>格式

9 位数字

### <a name="pattern"></a>模式

连续的 9 位数字

### <a name="keywords"></a>关键字

#### <a name="keyword_passport"></a>Keyword\_passport

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
