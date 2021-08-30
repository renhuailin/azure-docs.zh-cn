---
title: Microsoft Azure Maps 出行服务（预览版）中的出行（交通）功能覆盖范围
description: 了解 Azure Maps 出行服务（预览版）在哪些区域为公共交通功能（例如路线规划和营运告示）提供哪种覆盖程度。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: b2552b9ee4ba37ad6062229958557d81e871fbe5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733409"
---
# <a name="azure-maps-mobility-services-preview-coverage"></a>Azure Maps 出行服务（预览版）的覆盖范围

> [!IMPORTANT]
> Azure Maps 出行服务（预览版）已停用，在 2021 年 10 月 5 日之后将不再可用，也不再受支持。 所有其他 Azure Maps API 和服务不受此停用公告的影响。
> 有关详细信息，请参阅 [Azure Maps 出行服务（预览版）停用](https://azure.microsoft.com/updates/azure-maps-mobility-services-preview-retirement/)。

Azure Maps [出行服务](/rest/api/maps/mobility)缩短了具有公共交通功能（例如公交路线规划和搜索附近的公交站点）的应用程序的开发时间。 用户可以检索有关公交站点、线路和时刻表的详细信息。 通过出行服务，用户还可以检索停靠站点和线路的几何形状，接收到站、线路切换和服务区的提醒，以及实时的公交抵达时间和营运告示。 此外，出行服务还提供具有多式联运行程规划选项的路线规划功能。 多式联运行程规划在一个行程中融合了步行、骑行和公共交通选项。 用户还可以访问详细的多式联运分步行程。

Azure Maps 不能为所有城市和国家/地区提供同一级别的信息和准确度。 能否调用公共交通数据取决于所在的都市圈。 此外，地图数据不一定包括为都市圈提供服务的所有公交选项和营运公司。

下表提供了 Azure Maps 出行服务的覆盖范围信息。

| 符号 | 含义 |
|--------|---------|
| *      |在该国家/地区几乎完全覆盖。|

## <a name="americas"></a>美洲

| 国家/地区 |  城市（都市圈） |
|----------------|---------|
| 安提瓜和巴布达 | 安提瓜和巴布达* |
| 阿根廷       | <p>Azul、Bahía Blanca、Buenos Aires、Caleta Olivia、Catamarca、Chivilcoy、Comodoro Rivadavia、Concordia、Córdoba、Corrientes、General Pico、Gualeguaychu、La Rioja、Mar del Plata、Mendoza、Miramar、Necochea、Neuquén、Oberá、Olavarría、Paraná、Posadas、Rafaela、Rio Tercero、Rosario、Salta、San Carlos de Bariloche、San Luis、San Miguel de Tucumán、San Pedro、Santa Fe、Tandil、Ushuaia、Victoria、Viedma、Villa María</p>|
| 巴巴多斯       |  巴巴多斯* |
| 巴西         | <p>Angra dos Reis、Anápolis、Apucarana、Aracaju、Araraquara、Araxa、Araçatuba、Atibaia、Bage、Barretos、Bauru、Bebedouro、Belem、Belo Horizonte、Blumenau、Boa Vista、Botucatu、Brasilia、Caldas Novas、Campina Grande、Campinas、Campo Belo、Campo Grande、Caraguatatuba、Caratinga、Cascavel、Cataguases、Caxias、Leopoldina e Região、Catalão、Caxias do Sul、Chapecó、Cianorte、Conselheiro Lafaiete、Corumbá、Criciúma、Cruzeiro do Sul、Cuiabá、Curitiba、Curitibanos、Curvelo、Diamantina、Divinópolis、Dourados、Estrela、Feira de Santana、Fernando de Noronha、Florianópolis、Fortaleza、Foz do Iguaçu、Franca、Garanhuns、Goiania、Governador Valadares、Guarapuava、Imperatriz、Ipatinga、Irati、Itabira、Itabuna、Itajaí、Itajuba、Ituiutaba、Jaguarao、Jaraguá do Sul、Joao Pessoa、Joinville、Juazeiro do Norte、Juiz de Fora、Jundiaí、Lages、Lavras e Regiao、Lucas do Rio Verde、Londrina、Macapa、Macaé、Maceió、Mafra e Rio Negro、Manaus、Manhuacu、Maringá、Marília、Monte Carmelo、Montes Claros、Mossoró、Natal、Osorio、Ourinhos、Ouro Preto、Palmas、Paracatu、Paranaguá、Parnaíba、Passo Fundo、Passos、Pato Branco、Patos de Minas、Patrocínio、Pelotas、Picos、Piracicaba、Pirapora、Pocos de Caldas、Ponta Grossa、Porto Alegre、Porto Ferreira、Porto Seguro、Porto Velho、Praia Grande、Recife、Ribeirão Preto、Rio、Rio Branco、Rio Verde、Rondonópolis、Salinas、Salvador、Santa Cruz do Sul、Santa Maria、Santa Rita do Sapucaí、Santarem、Santiago del Estero、Santos、Sao Gabriel do Oeste、Sao Joao del Rei、Tiradentes e Regiao、Sao Jose do Rio Preto、Sao Mateus、Sao Paulo、Sorocaba、São Carlos、São Francisco do Sul、São José dos Campos、São Lourenço、São Luís、Taubaté、Telemaco Borba、Teofilo Otoni、Teresina、Toledo 、Três Lagoas、Tucurui、Ubatuba、Uberaba、Uberlândia、Ubá、Uruguaiana、Varginha、Vicosa、Videira & Fraiburgo、Vitória、Vitória da Conquista、Volta Redonda、Votuporanga </p>|
| 加拿大 | Banff (AB)、Brandon (MB)、Calgary (AB)、Chatham-Kent (ON)、Comox Valley (BC)、Cowichan Valley (BC)、Cranbrook (BC),Edmonton (AB)、Fort St. John、Fredericton (NB)、Greater Sudbury (ON)、Greater Vancouver (BC)、Halifax (NS)、Kamloops (BC)、Kelowna - Vernon (BC)、Kingston (ON)、London (ON)、Moncton (NB)、Montreal (QC)、Nanaimo (BC)、Ottawa (ON)、Prince George (BC)、Québec City (QC)、Red Deer (AB)、Regina (SK)、Rimouski (QC)、Saskatoon (SK)、Sherbrooke (QC)、Southwest British Columbia (BC)、Squamish (BC)、St. John's (NL)、Sunshine Coast、Thunder Bay (ON)、Toronto (ON)、Victoria (BC)、West Kootenay (BC)、Whistler (BC)、Windsor (ON)、Winnipeg (MB)、Woodstock</p>|
| 智利  | <p>Antofagasta、Arica、Aysén、Chillán、Concepción、Constitución、Copiapó、Curicó、Iquique、La Serena y Coquimbo、Linares、Los Angeles (Chile)、Los Lagos、Punta Arenas、Rancagua、Santiago、Talca、Temuco、Valdivia、Valparaíso、Viña del Mar</p>|
| 哥伦比亚 | <p>Barranquilla、Bogotá、Bucaramanga、Cali、Cartagena、Ibagué、Medellín、Pasto、Popayán、Santa Marta、Sincelejo、Valledupar</p>|  
| 哥斯达黎加 | San José|
| 多米尼加共和国 | Santo Domingo |
| 厄瓜多尔 | Cuenca、Guayaquil、Loja、Manta、Milagro|
| 萨尔瓦多 | San Salvador |
 | 危地马拉 | Ciudad de Guatemala (GT) |
| 墨西哥 | Acapulco、Aguascalientes、Cancun、Durango、Mexico City、Guadalajara、Lion、Merida、Monterrey、Puebla、Puerto Vallarta、Querétaro、San Luis Potosi、Tijuana、Torreon|
| 尼加拉瓜 | Managua |
| 巴拿马 | 巴拿马*|
| 秘鲁 | Cusco、Lima |
| 波多黎各 | San Juan |
| 苏里南 | Paramaribo |
| 乌拉圭 | Montevideo、Paysandu、Punta del Este、Salto |
| 美国 | <p>Albany (GA)、Albany (NY)、Albuquerque (NM)、Anchorage (AK)、Ann Arbor (MI)  Appleton-Oshkosh-Neenah (WI)、Asheville (NC)、Athens (GA)、Athens (OH)、Atlanta (GA)、Austin (TX)、Bakersfield (CA)、Baltimore (MD)、Bend-Redmond (OR)、Berkshire County (MA)、Birmingham (AL)、Bloomington (IN)、Boise (ID)、Boston (MA)、Boulder (CO)、Bowling Green (KY)、Brevard County (FL)、Buffalo (NY)、Butte (MT)、Cape Cod (MA)、Centre County (PA)、Champaign-Urbana (IL)、Charleston (SC)、Charleston (WV)、Charlotte (NC)、Charlottesville (VA)、Chattanooga (TN)、Cheyenne (WY)、Chicago (IL)、Cincinnati (OH)、Citrus County (FL)、Cleveland (OH)、Coachella Valley (CA)、Colorado Springs (CO)、Columbia (TN)、Columbia (SC)、Columbus (OH)、Corpus Christi (TX)、Dallas/Forth Worth (TX)、Dayton (OH)、Delaware、Denver (CO)、Des Moines (IA)、Detroit (MI)、Duluth (MN)、El Paso (TX)、Eugene (OR)、Fairbanks (AK)、Fargo (ND)、Fayetteville (NC)、Flagstaff (AZ)、Flint (MI) Fort Collins (CO)、Fort Wayne (IN)、Fresno (CA)、Gainesville (FL)、Grand Forks (ND)、Grand Rapids (MI)、Green Bay (WI)、Greensboro (NC)、Greenville (SC)、Gunnison (CO)、Hampton Roads (VA)、Hanford (CA)、Hartford (CT)、Hernando County (FL)、Hinesville (GA)、Honolulu (HI)、Houston (TX)、Humboldt County (CA)、Huntsville (AL)、Indianapolis (IN)、Ithaca (NY)、Jackson (MS)、Jackson (TN)、Jacksonville - St. John's County (FL)、Johnson city (TN)、Jonesboro (AR)、Joplin (MO)、Juneau (AK)、Kalamazoo (MI)、Kalispell (MT)、Kansas City (MO)、Kauai (HI)、Ketchum (ID)、Knoxville (TN)、Lafayette (IN)、Lancaster (PA)、Lansing (MI)、Laredo (TX)、Las Vegas (NV)、Lawrence (KS)、Lee County (FL)、Lexington (KY)、Lincoln County (OR)、Little Rock (AR)、Los Angeles (CA)、Louisville (KY)、Lubbock (TX)、Madison (WI)、Manchester (NH)、McAllen (TX)、Memphis (TN)、Miami (FL)、Milwaukee-Waukesha (WI)、Minneapolis-St. Paul (MN)、Missoula (MT)、Modesto (USA)、Moline (IL)、Monroe County (PA)、Montgomery (AL)、Morgantown (WV)、Nashville (TN)、Navajo Nation)、New Haven (CT)、New Orleans (LA)、NYC-NJ Area(NY)、Ocala (FL)、Okaloosa County (FL)、Oklahoma City (OK)、Omaha (NE)、Orlando (FL)、Palm Desert (CA)、Panama City (FL)、Pensacola (FL)、Peoria (IL)、Philadelphia (PA)、Phoenix (AZ)、Pittsburgh (PA)、Portland (ME)、Portland (OR)、Racine (WI)、Raleigh (NC)、Redding (CA)、Reno & Lake Tahoe (NV)、Richmond (VA)、Roanoke Valley (VA - Lynchburg)、Rochester (NY)、Rockford (IL)、Rocky Mount (NC)、Rocky Mountain National Park (CO)、Rogue Valley (OR)、Roseburg (OR)、Roseville (CA)、Sacramento (CA)、Salem (OR)、Salt Lake City (UT)、San Antonio (TX)、San Diego (CA)、San Luis Obispo (CA)、Santa Barbara (CA)、Santa Fe (NM)、Sarasota (FL)、Savannah (GA)、Seacoast Region (NH)、Seattle-Tacoma-Bellevue (WA)、SF Bay Area (CA)、SF-San Jose Area (CA)、Sioux City (IA)、Sioux Falls (SD)、Sitka (AK)、Spokane (WA)、Springfield (MA)、South Bend (IN)、Springfield (IL)、Springfield (Mass)、St. George (UT)、St. Louis (MO)、Stockton (CA)、Syracuse-Utica (NY)、Tallahassee (FL)、Tampa-St. Petersburg (FL)、Terre Haute (IN)、Toledo (OH)、Topeka (KS)、Traverse City (MI)、Tucson (AZ)、Tulsa (OK)、Vermont、Victorville (CA)、Volusia County (FL)、Waco (TX)、Washington (DC)、Waterbury (CT)、Wichita (KS)、Wichita Falls (TX) Wilmington (NC)、Yakima (WA)、Youngstown (OH)、York County (PA)、Yuma County (AZ)</p>|
| \+ 美属维尔京群岛 | 美属维尔京群岛* |
| 委内瑞拉 | Caracas |

## <a name="asia-pacific"></a>亚太区

| 国家/地区 |  城市（都市圈） |
|--------|---------|
| 澳大利亚 | <p>Adelaide、Alice Springs、Bowen、Brisbane、Bundaberg QLD、Burnie、Cairns、Canberra、Darwin、Gladstone、Hobart、Innisfail、Launceston、Mackay、Magnetic Island、Maryborough-Hervey Bay、Melbourne、New South Wales、Perth、RockHampton、South East Queensland、Sydney、Toowoomba、Townsville、Victoria、Warwick、Yeppoon</p> |
| 文莱 | Bandar Seri Begawan |
| 中国 | <p> 长春、长沙、成都、重庆、大连、大同、东莞、杭州、哈尔滨、江阴、济南、南京、南通、宁波、平顶山、青岛、沈阳、苏州、唐山、天津、潍坊、武汉、无锡、烟台、宜兴、珠海、上海、北京、广州、深圳、郑州</P>|
| 香港特别行政区 | 香港特别行政区*|
| 澳门特别行政区 | 澳门特别行政区*|
| 马尔代夫 | 男 |
| 印度 | Ahmedabad、Bengaluru、Delhi、Hyderabad、Mumbai、Mysuru、Pune|
| 印度尼西亚 | Bandung、Banjarmasin、Banyuwangi、Batam、Denpasar、Jakarta、Kediri、Malang、Palembang、Semarang、Surabaya、Surakarta、Yogyakarta |
| 日本 | Hokkaido、Shizuoka Prefecture、Tokyo、Wakkanai、Yamanashi Prefecture |
| 马来西亚 | Ipoh、Johar Bahru、Kuala Lumpur、Kuantan、Penang |
| 新西兰 | Auckland、Christchurch、Dunedin、Queenstown、Timaru、Wellington|
| 菲律宾 | 马尼拉 |
| 新加坡 | 新加坡* |
| 韩国 | 釜山、首尔 |
| 台湾 | 彰化县、台北 |
| 泰国 | Bangkok、Chiang Mai |
| 乌兹别克斯坦 | Samarkand |
| 越南 | Hanoi、Ho Chi Minh City |

## <a name="europe"></a>欧洲

| 国家/地区 |  城市（都市圈） |
|----------------|---------|
| 安道尔        | Andorra la Vella |
| 奥地利        | 维也纳 |
| 白俄罗斯        | Gomel、Grodno、Polotsk & Novopolotsk、Zhlobin、Vileyka、Maladziečna、Minsk、Rechytsa |
| 比利时        | 比利时* |
| 玻利维亚        | La Paz、Santa Cruz de la Sierra |
| 波斯尼亚和黑塞哥维那 | Sarajevo |
| 保加利亚       | <p>Balchik、Blagoevgrad、Burgas、Dobrich、Gabrovo、Haskovo、Kardzhali、Lovech、Nessebar、Pazardzhik、Pernik、Pleven、Plovdiv、Ruse、Shumen、Sliven、Stara Zagora、Vratsa、Yambol、Varna、Veliko、Sofia</P> |
| 克罗地亚 | Crikvenica、Dubrovnik、Rijeka、Slovanski Brod、Zagreb |
| 塞浦路斯 | Larnaca、Limassol、Nicosia |
| 捷克共和国 | Brno、Jablonec、Karlovy Vary、Liberec、Ostrava、Prague |
| 丹麦   | 丹麦* |
| 爱沙尼亚   | 爱沙尼亚* |
| 芬兰   | Hämeenlinna、Helsinki、Joensuu、Jyväskylä、Kajaani、Kouvola - Kotka、Kuopio、Lappeenranta、Mikkeli、Oulu、Pori、Rovaniemi、Seinäjoki、Tampere、Turku、Vaasa|
| 法国    | <p>Amberieu-en-Bugey、Amiens、Angers、Annecy、Annonay、Arras、Aubenas、Bayonne、Besançon、Blois、Bordeaux、Boulogne sur Mer、Brest、Briançon、cannes、Châlons-en-Champagne、Chartres、Clermont-Ferrand、Colmar、Côte d’Azur、Dax、Dijon、Grenoble、Haguenau、La Rochelle、Le Mans、Lens、Lille、Lorient、Lyon、MACS、Marseille & Provence、Metz、Millau、Mont-de-Marsan、Montpellier、Mulhouse、Nancy、Nantes、Nice、Nice Côte d’Azur、Nimes、Normandy、Nyons、Paris、Poitiers、Privas、Quimper、Rennes、Saint Malo、Saint-Étienne、Saint-Nazaire、Saintes、Sarrebourg、Sete、Strasbourg、Tarbes、Toulouse、Tours</P> |
| +法属圭亚那 | Cayenne |
| +新喀里多尼亚 | Nouméa  |
| 格鲁吉亚 | Tbilisi |
| 德国 | <p>Berlin、Brandenburg、Bremen & Niedersachsen、Cologne、Eisenach、Frankfurt、Hamburg、Karlsruhe、Mainz、München - Munich、Rhein-Neckar Region、Rhein-Ruhr Region、Stuttgart、Titisee-Neustadt、Ulm</P> |
| 希腊 | <p>Agrinio、Aigio、Athens、Arta、Amorgos、Chania、Corfu、Chios Kos、Heraklion、Ioannina、Kavala、Kalamata、Khios、Komotini、Kos、Larissa、Meganisi、Milos、Mykonos、Patra、Rethimno、Rhodes、Santorini、Serres、Syros、Tinos、Thessaloniki、Veria、Volos、Xanthi </P> |
| 匈牙利 | Budapest、Gyor、Miskolc、Nograd County、Pesc、Szeged、Székesfehérvár |
| 冰岛 | 冰岛* |
| 爱尔兰 | 爱尔兰* |
| 意大利   | <p>Agrigento、Alessandria、Ancona、Bari、Bologna - Bologne、Cagliari - Sardinia、Campobasso、Catania e Messina、Cosenza、Crema、Cremona、Crotone、Cuneo、Firenze - Florence、Foggia、Genova - Genoa、Iglesias、La Spezia、Lecce、Matera、Milano - Milan、Napoli - Naples、Padova、Palermo、Parma、Perugia、Pescara、Pisa、Potenza、Roma - Rome、Siena e Grosseto、Siracusa - Syracuse、Taranto、Torino - Turin、Trento、Trieste、Udine、Venezia - Venice </p> |
| 拉脱维亚 | Rīga |
| 列支敦士登 | 列支敦士登* |
| 立陶宛 | Druskininkai、Kauno、Klaipėda、Panevėžys、Vilnius |
| 卢森堡 | 卢森堡* |
| 摩尔多瓦 | Chisinau |
| 黑山 | Podgorica |
| 荷兰 | 荷兰* |
| 挪威 | 挪威* |
| 波兰 | <p>Wrocław、Białystok、Bydgoszcz、Elbląg、Elk、Gorzow、Kętrzyna、Krakow、Leszno、Lodz、Lublin、Mrągowo、Olsztyn、Poznań、Rzeszów、Sanok、Starachowice、Świonujście、Szczecin、Tricity、Warsaw、Wodzisław Śląski、Wrocław、Zakopane</p> |
| 葡萄牙 | Bragança、Coimbra、Funchal、Leiria、Lisboa、Portimao、Porto|
| 马耳他 | 马耳他* |
| 罗马尼亚 | <p>Alba Iulia、Arad、Bistrița、Brăila、Braşov、Bucharest、Buzau、Cluj Napoca、Constanța、Craiova、Deva、Focșani、Galati、Iaşi、Miercurea Ciuc、Oradea、Piatra Neamt、Pitești、Ploieşti、Reșița、Satu Mare、Sibiu、Suceava、Targu Mures、Timisoara、Tulcea、Zalau</p> | 
| 俄罗斯  | Kaliningrad、Rostov-on-Don、Volgograd、Yekaterinburg、Kazan、Kirov、Krasnodar、Moscow、Nalchik、Nizhny Novgorod、Novosibirsk、Noyabrsk、Omsk、Oryol、Perm、St Petersburg、Pyatigorsk、Tver、Tomsk |
| 塞尔维亚  | Beograd、Kragujevac、Nis、Novi Sad、Valjevo、Subotica | 
| 斯洛伐克 | Banská Bystrica、Bratislava、Kosice、Presov、Prievidza、Ruzomberok a Liptovsky Mikulas、Stará Ľubovňa、Trencin |
| 斯洛文尼亚 | Koper、Ljubljana |
| 西班牙    | <p>Albacete、Corunna、Alicante、Almería、Asturias、Avila、Badajoz、Bay of Cadiz、Barcelona、Bilbao、Burgos、Caceres、Campo de Gibraltar、Castellon de la Plana、Ceuta、Ciudad Real、Cordoba、Cuenca、El Hierro、Ferrol、Fuerteventura、Gran Canaria、Granada、Huelva、Huesca、Ibiza、Jaén - Úbeda、La Gomera、La Palma、Lanzarote、León、Lleida、Logroño、Lugo、Madrid、Malaga、Mallorca - Majorca、Melilla、Menorca、Merida、Murcia、Ourense、Palencia、Pamplona、Salamanca、San Sebastian、Santander、Santiago de Compostela、Segovia、Seville、Soria、Tarragona - Reus、Tenerife、Toledo、Valencia、Valladolid、Vigo、Vitoria-Gasteiz、Zaragoza - Saragossa</p> |
| 瑞典 | Goteborg/Gothenburg/Jonkoping、Malmö kommun - Malmö、Norrköping och Linköping、Stockholm、Sundsvall |
| 瑞士 | Basel、Geneva、Yverdon-les-Bains、Zurich | 
| 土耳其 | Adana-Mersin、Ankara、Antalya、Balıkesir、Bilecik、Bolu、Bursa、Çorum、Denizli、Duzce、Edirne、Elazig、Eskisehir、Istanbul、Izmir-Aydin、Kahramanmaras、Kayseri、Konya、Malatya、Muğla、Samsun、Şanlıurfa、Trabzon |
| 英国 | East Anglia、East Midlands、London and South East、North East、North West、Northern Ireland、Scotland、South West、Wales、West Midlands、Yorkshire |
| 乌克兰 | Kharkiv、Zhytomyr、Kyiv、Lviv、Chernivtsi |

## <a name="middle-east-and-africa"></a>中东和非洲

| 国家/地区 |  城市（都市圈） |
|---------|---------|
| 巴林 | 巴林* |
| 布基纳法索 | Ouagadougou |
| 刚果 | Kinshasa |
| 埃及 | 开罗    |
| 以色列| 以色列*  |
| 肯尼亚 | Nairobi  |
| 马达加斯加 | Antananarivo |
| 摩洛哥 | Casablanca、Essaouira、Khouribga、Tétouan|
| 卡塔尔| Doha|
| 沙特阿拉伯 | Thuwal |
| 塞内加尔 | Dakar |
| 南非 | 开普敦 |
| 突尼斯 | Kairouan |
| 阿拉伯联合酋长国  | Abu Dhabi、Dubai |


## <a name="next-steps"></a>后续步骤

了解如何使用出行服务（预览版）来请求交通数据：

> [!div class="nextstepaction"]
> [如何请求交通数据](how-to-request-transit-data.md)

了解如何使用出行服务（预览版）请求实时数据：

> [!div class="nextstepaction"]
> [如何请求实时数据](how-to-request-real-time-data.md)

浏览 Azure Maps 出行服务（预览版）API 文档

> [!div class="nextstepaction"]
> [出行服务 API 文档](/rest/api/maps/mobility)
