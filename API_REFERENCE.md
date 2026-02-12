# eRepublik API - Reference Data

#erepublik #api #reference #countries #regions

[< Back to Table of Contents](API_TOC.md)

---

## Overview

Static reference tables for countries and regions in eRepublik. This data was extracted from the [`/en/main/travelData`](API_WORLD.md) endpoint response.

> **Data source:** `POST /en/main/travelData` (authenticated). Region original owners are determined from the `isConqueredFrom` field (for occupied regions) and `countryId` (for non-occupied regions).

---

## Countries

**Total: 74 countries** (ID range: 1–171)

| ID | Name | Permalink |
|----|------|-----------|
| 1 | Romania | Romania |
| 9 | Brazil | Brazil |
| 10 | Italy | Italy |
| 11 | France | France |
| 12 | Germany | Germany |
| 13 | Hungary | Hungary |
| 14 | China | China |
| 15 | Spain | Spain |
| 23 | Canada | Canada |
| 24 | USA | USA |
| 26 | Mexico | Mexico |
| 27 | Argentina | Argentina |
| 28 | Venezuela | Venezuela |
| 29 | United Kingdom | United-Kingdom |
| 30 | Switzerland | Switzerland |
| 31 | Netherlands | Netherlands |
| 32 | Belgium | Belgium |
| 33 | Austria | Austria |
| 34 | Czech Republic | Czech-Republic |
| 35 | Poland | Poland |
| 36 | Slovakia | Slovakia |
| 37 | Norway | Norway |
| 38 | Sweden | Sweden |
| 39 | Finland | Finland |
| 40 | Ukraine | Ukraine |
| 41 | Russia | Russia |
| 42 | Bulgaria | Bulgaria |
| 43 | Turkey | Turkey |
| 44 | Greece | Greece |
| 45 | Japan | Japan |
| 47 | South Korea | South-Korea |
| 48 | India | India |
| 49 | Indonesia | Indonesia |
| 50 | Australia | Australia |
| 51 | South Africa | South-Africa |
| 52 | Republic of Moldova | Republic-of-Moldova |
| 53 | Portugal | Portugal |
| 54 | Ireland | Ireland |
| 55 | Denmark | Denmark |
| 56 | Iran | Iran |
| 57 | Pakistan | Pakistan |
| 58 | Israel | Israel |
| 59 | Thailand | Thailand |
| 61 | Slovenia | Slovenia |
| 63 | Croatia | Croatia |
| 64 | Chile | Chile |
| 65 | Serbia | Serbia |
| 66 | Malaysia | Malaysia |
| 67 | Philippines | Philippines |
| 68 | Singapore | Singapore |
| 69 | Bosnia and Herzegovina | Bosnia-Herzegovina |
| 70 | Estonia | Estonia |
| 71 | Latvia | Latvia |
| 72 | Lithuania | Lithuania |
| 73 | North Korea | North-Korea |
| 74 | Uruguay | Uruguay |
| 75 | Paraguay | Paraguay |
| 76 | Bolivia | Bolivia |
| 77 | Peru | Peru |
| 78 | Colombia | Colombia |
| 79 | North Macedonia | North-Macedonia |
| 80 | Montenegro | Montenegro |
| 81 | Republic of China (Taiwan) | Republic-of-China-Taiwan |
| 82 | Cyprus | Cyprus |
| 83 | Belarus | Belarus |
| 84 | New Zealand | New-Zealand |
| 164 | Saudi Arabia | Saudi-Arabia |
| 165 | Egypt | Egypt |
| 166 | United Arab Emirates | United-Arab-Emirates |
| 167 | Albania | Albania |
| 168 | Georgia | Georgia |
| 169 | Armenia | Armenia |
| 170 | Nigeria | Nigeria |
| 171 | Cuba | Cuba |

> **ID gaps:** 2–8, 16–22, 25, 46, 60, 62, 85–163. These IDs were never assigned or belong to removed countries.

### Usage

- **Permalink** is used in URL paths: `/en/country/society/{permalink}`, `/en/main/region/{regionPermalink}`
- **ID** is used in API request/response payloads (e.g., `countryId`, `citizenshipCountryId`)
- Country flags: `//www.erepublik.net/images/flags/L/{permalink}.gif` (L/M/S/XL sizes)
- Country flags (PNG): `//www.erepublik.net/images/flags_png/XL/{permalink}.png`

---

## Regions

**Total: 574 regions** (ID range: 3–764)

The **Original Owner** is the country that a region natively belongs to. Regions may be currently controlled by a different country through conquest, but the original owner remains fixed.

| ID | Name | Permalink | Owner ID | Original Owner |
|----|------|-----------|----------|----------------|
| 3 | Dobrogea | Dobrogea | 1 | Romania |
| 5 | Muntenia | Muntenia | 1 | Romania |
| 9 | Oltenia | Oltenia | 1 | Romania |
| 11 | Banat | Banat | 1 | Romania |
| 35 | Transilvania | Transilvania | 1 | Romania |
| 36 | Crisana | Crisana | 1 | Romania |
| 37 | Moldova | Moldova | 1 | Romania |
| 38 | Maramures | Maramures | 1 | Romania |
| 39 | Bucovina | Bucovina | 1 | Romania |
| 40 | Alabama | Alabama | 24 | USA |
| 41 | Alaska | Alaska | 24 | USA |
| 42 | Arizona | Arizona | 24 | USA |
| 43 | Arkansas | Arkansas | 24 | USA |
| 44 | California | California | 24 | USA |
| 45 | Colorado | Colorado | 24 | USA |
| 46 | Connecticut | Connecticut | 24 | USA |
| 47 | Delaware | Delaware | 24 | USA |
| 48 | Florida | Florida | 24 | USA |
| 49 | Georgia | Georgia | 24 | USA |
| 50 | Hawaii | Hawaii | 24 | USA |
| 51 | Idaho | Idaho | 24 | USA |
| 52 | Illinois | Illinois | 24 | USA |
| 53 | Indiana | Indiana | 24 | USA |
| 54 | Iowa | Iowa | 24 | USA |
| 55 | Kansas | Kansas | 24 | USA |
| 56 | Kentucky | Kentucky | 24 | USA |
| 57 | Louisiana | Louisiana | 24 | USA |
| 58 | Maine | Maine | 24 | USA |
| 59 | Maryland | Maryland | 24 | USA |
| 60 | Massachusetts | Massachusetts | 24 | USA |
| 61 | Michigan | Michigan | 24 | USA |
| 62 | Minnesota | Minnesota | 24 | USA |
| 63 | Mississippi | Mississippi | 24 | USA |
| 64 | Missouri | Missouri | 24 | USA |
| 65 | Montana | Montana | 24 | USA |
| 66 | Nebraska | Nebraska | 24 | USA |
| 67 | Nevada | Nevada | 24 | USA |
| 68 | New Hampshire | New-Hampshire | 24 | USA |
| 69 | New Jersey | New-Jersey | 24 | USA |
| 70 | New Mexico | New-Mexico | 24 | USA |
| 71 | New York | New-York | 24 | USA |
| 72 | North Carolina | North-Carolina | 24 | USA |
| 73 | North Dakota | North-Dakota | 24 | USA |
| 74 | Ohio | Ohio | 24 | USA |
| 75 | Oklahoma | Oklahoma | 24 | USA |
| 76 | Oregon | Oregon | 24 | USA |
| 77 | Pennsylvania | Pennsylvania | 24 | USA |
| 78 | Rhode Island | Rhode-Island | 24 | USA |
| 79 | South Carolina | South-Carolina | 24 | USA |
| 80 | South Dakota | South-Dakota | 24 | USA |
| 81 | Tennessee | Tennessee | 24 | USA |
| 82 | Texas | Texas | 24 | USA |
| 83 | Utah | Utah | 24 | USA |
| 84 | Vermont | Vermont | 24 | USA |
| 85 | Virginia | Virginia | 24 | USA |
| 86 | Washington | Washington | 24 | USA |
| 87 | West Virginia | West-Virginia | 24 | USA |
| 88 | Wisconsin | Wisconsin | 24 | USA |
| 89 | Wyoming | Wyoming | 24 | USA |
| 90 | District of Columbia | District-of-Columbia | 24 | USA |
| 91 | Northern Basarabia | Northern-Basarabia | 52 | Republic of Moldova |
| 92 | Chisinau | Chisinau | 52 | Republic of Moldova |
| 93 | Southern Basarabia | Southern-Basarabia | 52 | Republic of Moldova |
| 94 | Transnistria | Transnistria | 52 | Republic of Moldova |
| 95 | Ontario | Ontario | 23 | Canada |
| 96 | Prince Edward Island | Prince-Edward-Island | 23 | Canada |
| 97 | Alberta | Alberta | 23 | Canada |
| 98 | New Brunswick | New-Brunswick | 23 | Canada |
| 99 | Nova Scotia | Nova-Scotia | 23 | Canada |
| 100 | Quebec | Quebec | 23 | Canada |
| 101 | Saskatchewan | Saskatchewan | 23 | Canada |
| 102 | Newfoundland and Labrador | Newfoundland | 23 | Canada |
| 103 | British Columbia | British-Columbia | 23 | Canada |
| 104 | Yukon | Yukon | 23 | Canada |
| 105 | Manitoba | Manitoba | 23 | Canada |
| 106 | Northwest Territories | Northwest-Territories | 23 | Canada |
| 107 | Nunavut | Nunavut | 23 | Canada |
| 108 | Western Transdanubia | Western-Transdanubia | 13 | Hungary |
| 109 | Southern Transdanubia | Southern-Transdanubia | 13 | Hungary |
| 110 | Central Transdanubia | Central-Transdanubia | 13 | Hungary |
| 111 | Central Hungary | Central-Hungary | 13 | Hungary |
| 112 | Northern Hungary | Northern-Hungary | 13 | Hungary |
| 113 | Northern Great Plain | Northern-Great-Plain | 13 | Hungary |
| 114 | Southern Great Plain | Southern-Great-Plain | 13 | Hungary |
| 115 | Valley of Mexico | Valley-of-Mexico | 26 | Mexico |
| 116 | Baja | Baja | 26 | Mexico |
| 117 | Northwest of Mexico | Northwest-of-Mexico | 26 | Mexico |
| 118 | Pacific Coast of Mexico | Pacific-Coast-of-Mexico | 26 | Mexico |
| 119 | Oaxaca | Oaxaca | 26 | Mexico |
| 120 | Gulf of Mexico | Gulf-of-Mexico | 26 | Mexico |
| 121 | Southeast of Mexico | Southeast-of-Mexico | 26 | Mexico |
| 122 | Northeast of Mexico | Northeast-of-Mexico | 26 | Mexico |
| 123 | Venezuelan Andean | Venezuelan-Andean | 28 | Venezuela |
| 124 | Venezuelan Capital | Venezuelan-Capital | 28 | Venezuela |
| 125 | Central Venezuela | Central-Venezuela | 28 | Venezuela |
| 126 | Central Western Venezuela | Central-Western-Venezuela | 28 | Venezuela |
| 127 | Guayana | Guayana | 28 | Venezuela |
| 129 | Llanos | Llanos | 28 | Venezuela |
| 130 | North Eastern Venezuela | North-Eastern-Venezuela | 28 | Venezuela |
| 131 | Zulian | Zulian | 28 | Venezuela |
| 132 | Subcarpathia | Subcarpathia | 40 | Ukraine |
| 133 | Galicia and Lodomeria | Galicia-and-Lodomeria | 40 | Ukraine |
| 134 | Volhynia | Volhynia | 40 | Ukraine |
| 135 | Polisia | Polisia | 40 | Ukraine |
| 136 | Podolia | Podolia | 40 | Ukraine |
| 137 | Bukovyna | Bukovyna | 40 | Ukraine |
| 138 | Dnipro | Dnipro | 40 | Ukraine |
| 139 | Siveria | Siveria | 40 | Ukraine |
| 140 | Bassarabia | Bassarabia | 40 | Ukraine |
| 141 | Zaporozhia | Zaporozhia | 40 | Ukraine |
| 142 | Sloboda | Sloboda | 40 | Ukraine |
| 143 | Donbas | Donbas | 40 | Ukraine |
| 144 | Taurida | Taurida | 40 | Ukraine |
| 146 | Center West of Brazil | Center-West-of-Brazil | 9 | Brazil |
| 147 | North of Brazil | North-of-Brazil | 9 | Brazil |
| 148 | Northeast of Brazil | Northeast-of-Brazil | 9 | Brazil |
| 149 | Southeast of Brazil | Southeast-of-Brazil | 9 | Brazil |
| 150 | Parana and Santa Catarina | Parana-and-Santa-Catarina | 9 | Brazil |
| 151 | Pampas | Pampas | 27 | Argentina |
| 152 | Argentine Northwest | Argentine-Northwest | 27 | Argentina |
| 153 | South East Chaco | South-East-Chaco | 27 | Argentina |
| 154 | Mesopotamia | Mesopotamia | 27 | Argentina |
| 155 | Cuyo | Cuyo | 27 | Argentina |
| 156 | Patagonia | Patagonia | 27 | Argentina |
| 157 | Lisboa | Lisboa | 53 | Portugal |
| 158 | Norte | Norte | 53 | Portugal |
| 159 | Centro | Centro | 53 | Portugal |
| 160 | Alentejo | Alentejo | 53 | Portugal |
| 161 | Algarve | Algarve | 53 | Portugal |
| 162 | Azores | Azores | 53 | Portugal |
| 163 | Madeira | Madeira | 53 | Portugal |
| 166 | Madrid | Madrid | 15 | Spain |
| 167 | Andalucia | Andalucia | 15 | Spain |
| 168 | Aragon | Aragon | 15 | Spain |
| 169 | Asturias | Asturias | 15 | Spain |
| 170 | Basque Country | Basque-Country | 15 | Spain |
| 171 | Cantabria | Cantabria | 15 | Spain |
| 173 | Castilla y Leon | Castilla-Leon | 15 | Spain |
| 174 | Catalonia | Catalonia | 15 | Spain |
| 175 | Extremadura | Extremadura | 15 | Spain |
| 176 | Galicia | Galicia-Spain | 15 | Spain |
| 177 | Murcia | Murcia | 15 | Spain |
| 178 | Navarra | Navarra | 15 | Spain |
| 179 | La Rioja | La-Rioja | 15 | Spain |
| 180 | Valencian Community | Valencian | 15 | Spain |
| 181 | Castilla La Mancha | Castilla-La-Mancha | 15 | Spain |
| 183 | Canary Islands | Canary-Islands | 15 | Spain |
| 184 | Balearic Islands | Balearic-Islands | 15 | Spain |
| 185 | Alsace | Alsace | 11 | France |
| 186 | Aquitaine | Aquitaine | 11 | France |
| 187 | Auvergne | Auvergne | 11 | France |
| 188 | Brittany | Brittany | 11 | France |
| 189 | Burgundy | Burgundy | 11 | France |
| 190 | Loire Valley | Loire-Valley | 11 | France |
| 191 | Champagne Ardenne | Champagne-Ardenne | 11 | France |
| 192 | Corsica | Corsica | 11 | France |
| 193 | Franche-comte | Franche-comte | 11 | France |
| 194 | Languedoc Roussillon | Languedoc-Roussillon | 11 | France |
| 195 | Limousin | Limousin | 11 | France |
| 196 | Lorraine | Lorraine | 11 | France |
| 197 | Lower Normandy | Lower-Normandy | 11 | France |
| 198 | Midi-Pyrenees | Midi-Pyrenees | 11 | France |
| 199 | Paris Isle of France | Paris-Isle-of-France | 11 | France |
| 200 | Pays de la Loire | Pays-de-la-Loire | 11 | France |
| 201 | Picardy | Picardy | 11 | France |
| 202 | Poitou Charentes | Poitou-Charentes | 11 | France |
| 203 | Provence Alpes Azur | Provence-Alpes-Azur | 11 | France |
| 204 | Rhone Alps | Rhone-Alps | 11 | France |
| 205 | Upper Normandy | Upper-Normandy | 11 | France |
| 207 | North Calais | North-Calais | 11 | France |
| 208 | Dublin | Dublin | 54 | Ireland |
| 209 | Cork | Cork | 54 | Ireland |
| 210 | Shannon | Shannon | 54 | Ireland |
| 212 | Mayo | Mayo | 54 | Ireland |
| 213 | Wexford | Wexford | 54 | Ireland |
| 215 | Louth | Louth | 54 | Ireland |
| 216 | London | London | 29 | United Kingdom |
| 217 | Scotland | Scotland | 29 | United Kingdom |
| 218 | Wales | Wales | 29 | United Kingdom |
| 219 | Northern Ireland | Northern-Ireland | 29 | United Kingdom |
| 220 | South East of England | South-East-of-England | 29 | United Kingdom |
| 221 | South West of England | South-West-of-England | 29 | United Kingdom |
| 222 | East Midlands | East-Midlands | 29 | United Kingdom |
| 223 | West Midlands | West-Midlands | 29 | United Kingdom |
| 224 | East of England | East-of-England | 29 | United Kingdom |
| 225 | Yorkshire & Humberside | Yorkshire-Humberside | 29 | United Kingdom |
| 226 | North East of England | North-East-of-England | 29 | United Kingdom |
| 227 | North West of England | North-West-of-England | 29 | United Kingdom |
| 228 | Brussels | Brussels | 32 | Belgium |
| 229 | Flanders | Flanders | 32 | Belgium |
| 230 | Wallonia | Wallonia | 32 | Belgium |
| 231 | Hovedstaden | Hovedstaden | 55 | Denmark |
| 232 | Midtjylland | Midtjylland | 55 | Denmark |
| 233 | Nordjylland | Nordjylland | 55 | Denmark |
| 235 | Sjaelland | Sjaelland | 55 | Denmark |
| 236 | Syddanmark | Syddanmark | 55 | Denmark |
| 237 | Southern Finland | Southern-Finland | 39 | Finland |
| 238 | Western Finland | Western-Finland | 39 | Finland |
| 239 | Eastern Finland | Eastern-Finland | 39 | Finland |
| 240 | Oulu | Oulu | 39 | Finland |
| 241 | Lapland | Lapland | 39 | Finland |
| 242 | Aland | Aland | 39 | Finland |
| 243 | Baden-Wurttemberg | Baden-Wurttemberg | 12 | Germany |
| 244 | Bavaria | Bavaria | 12 | Germany |
| 246 | Brandenburg and Berlin | Brandenburg-and-Berlin | 12 | Germany |
| 249 | Hesse | Hesse | 12 | Germany |
| 250 | Mecklenburg-Western Pomerania | Mecklenburg | 12 | Germany |
| 251 | Lower Saxony and Bremen | Lower-Saxony-and-Bremen | 12 | Germany |
| 252 | North Rhine-Westphalia | North-Rhine-Westphalia | 12 | Germany |
| 253 | Rhineland-Palatinate | Rhineland-Palatinate | 12 | Germany |
| 254 | Saarland | Saarland | 12 | Germany |
| 255 | Saxony | Saxony | 12 | Germany |
| 256 | Saxony-Anhalt | Saxony-Anhalt | 12 | Germany |
| 257 | Schleswig-Holstein and Hamburg | Schleswig-Holstein-and-Hamburg | 12 | Germany |
| 258 | Thuringia | Thuringia | 12 | Germany |
| 259 | Abruzzo | Abruzzo | 10 | Italy |
| 260 | Aosta Valley | Aosta-Valley | 10 | Italy |
| 261 | Apulia | Apulia | 10 | Italy |
| 262 | Basilicata | Basilicata | 10 | Italy |
| 263 | Calabria | Calabria | 10 | Italy |
| 264 | Campania | Campania | 10 | Italy |
| 265 | Emilia-Romagna | Emilia-Romagna | 10 | Italy |
| 266 | Friuli-Venezia Giulia | Friuli-Venezia-Giulia | 10 | Italy |
| 267 | Lazio | Lazio | 10 | Italy |
| 268 | Liguria | Liguria | 10 | Italy |
| 269 | Lombardy | Lombardy | 10 | Italy |
| 270 | Marche | Marche | 10 | Italy |
| 271 | Molise | Molise | 10 | Italy |
| 272 | Piedmont | Piedmont | 10 | Italy |
| 273 | Sardinia | Sardinia | 10 | Italy |
| 274 | Sicily | Sicily | 10 | Italy |
| 275 | Trentino-South Tyrol | Trentino-South-Tyrol | 10 | Italy |
| 276 | Tuscany | Tuscany | 10 | Italy |
| 277 | Umbria | Umbria | 10 | Italy |
| 278 | Veneto | Veneto | 10 | Italy |
| 291 | Nord-Norge | Nord-Norge | 37 | Norway |
| 292 | Sorlandet | Sorlandet | 37 | Norway |
| 293 | Trondelag | Trondelag | 37 | Norway |
| 294 | Vestlandet | Vestlandet | 37 | Norway |
| 295 | Ostlandet | Ostlandet | 37 | Norway |
| 306 | Pomerania | Pomerania | 35 | Poland |
| 307 | Silesia | Silesia | 35 | Poland |
| 312 | Bratislava | Bratislava | 36 | Slovakia |
| 315 | Western Slovakia | Western-Slovakia | 36 | Slovakia |
| 316 | Central Slovakia | Central-Slovakia | 36 | Slovakia |
| 319 | Eastern Slovakia | Eastern-Slovakia | 36 | Slovakia |
| 320 | Svealand | Svealand | 38 | Sweden |
| 321 | Norrland and Sameland | Norrland-Sameland | 38 | Sweden |
| 322 | Jamtland Harjedalen | Jamtland-Harjedalen | 38 | Sweden |
| 323 | Bohus | Bohus | 38 | Sweden |
| 324 | Scania | Scania | 38 | Sweden |
| 325 | Gotaland | Gotaland | 38 | Sweden |
| 326 | Smaland | Smaland | 38 | Sweden |
| 328 | New South Wales | New-South-Wales | 50 | Australia |
| 329 | Queensland | Queensland | 50 | Australia |
| 330 | South Australia | South-Australia | 50 | Australia |
| 331 | Tasmania | Tasmania | 50 | Australia |
| 332 | Victoria | Victoria | 50 | Australia |
| 333 | Western Australia | Western-Australia | 50 | Australia |
| 334 | Northern Territory | Northern-Territory | 50 | Australia |
| 336 | Deutschschweiz | Deutschschweiz | 30 | Switzerland |
| 337 | Romandie | Romandie | 30 | Switzerland |
| 338 | Svizzera italiana | Svizzera-italiana | 30 | Switzerland |
| 339 | Graubunden | Graubunden | 30 | Switzerland |
| 340 | Burgenland | Burgenland | 33 | Austria |
| 341 | Carinthia | Carinthia | 33 | Austria |
| 342 | Lower Austria | Lower-Austria | 33 | Austria |
| 343 | Upper Austria | Upper-Austria | 33 | Austria |
| 344 | Salzburg | Salzburg | 33 | Austria |
| 345 | Styria | Styria | 33 | Austria |
| 346 | Tyrol | Tyrol | 33 | Austria |
| 347 | Vorarlberg | Vorarlberg | 33 | Austria |
| 349 | Burgas | Burgas | 42 | Bulgaria |
| 352 | Vidin | Vidin | 42 | Bulgaria |
| 353 | Plovdiv | Plovdiv | 42 | Bulgaria |
| 355 | Sofia | Sofia | 42 | Bulgaria |
| 356 | Varna | Varna | 42 | Bulgaria |
| 358 | Ruse | Ruse | 42 | Bulgaria |
| 361 | Anhui | Anhui | 14 | China |
| 362 | Fujian | Fujian | 14 | China |
| 363 | Gansu | Gansu | 14 | China |
| 364 | Guangdong | Guangdong | 14 | China |
| 368 | Heilongjiang | Heilongjiang | 14 | China |
| 370 | Hubei | Hubei | 14 | China |
| 371 | Hunan | Hunan | 14 | China |
| 372 | Jiangsu | Jiangsu | 14 | China |
| 373 | Jiangxi | Jiangxi | 14 | China |
| 375 | Liaoning | Liaoning | 14 | China |
| 377 | Shaanxi | Shaanxi | 14 | China |
| 378 | Shandong | Shandong | 14 | China |
| 379 | Shanxi | Shanxi | 14 | China |
| 380 | Sichuan | Sichuan | 14 | China |
| 381 | Yunnan | Yunnan | 14 | China |
| 382 | Zhejiang | Zhejiang | 14 | China |
| 384 | Guizhou | Guizhou | 14 | China |
| 385 | Hainan | Hainan | 14 | China |
| 386 | Henan | Henan | 14 | China |
| 387 | Jilin | Jilin | 14 | China |
| 389 | Qinghai | Qinghai | 14 | China |
| 390 | Guangxi | Guangxi | 14 | China |
| 391 | Inner Mongolia | Inner-Mongolia | 14 | China |
| 392 | Ningxia | Ningxia | 14 | China |
| 393 | Xinjiang | Xinjiang | 14 | China |
| 394 | Tibet | Tibet | 14 | China |
| 395 | Beijing | Beijing | 14 | China |
| 396 | Chongqing | Chongqing | 14 | China |
| 397 | Shanghai | Shanghai | 14 | China |
| 413 | Thrace | Thrace | 44 | Greece |
| 414 | Macedonia | Macedonia | 44 | Greece |
| 415 | Thessaly | Thessaly | 44 | Greece |
| 416 | Epirus | Epirus | 44 | Greece |
| 417 | Central Greece | Central-Greece | 44 | Greece |
| 418 | Attica | Attica | 44 | Greece |
| 419 | Peloponnese | Peloponnese | 44 | Greece |
| 420 | Aegean Islands | Aegean-Islands | 44 | Greece |
| 421 | Ionian Islands | Ionian-Islands | 44 | Greece |
| 422 | Crete | Crete | 44 | Greece |
| 423 | Mazuria | Mazuria | 35 | Poland |
| 424 | Mazovia | Mazovia | 35 | Poland |
| 425 | Little Poland | Little-Poland | 35 | Poland |
| 426 | Great Poland | Great-Poland | 35 | Poland |
| 437 | Southern Bohemia | Southern-Bohemia | 34 | Czech Republic |
| 440 | Moravia | Moravia | 34 | Czech Republic |
| 442 | Northern Bohemia | Northern-Bohemia | 34 | Czech Republic |
| 443 | Northern India | Northern-India | 48 | India |
| 445 | Uttar Pradesh | Uttar-Pradesh | 48 | India |
| 446 | Rajasthan | Rajasthan | 48 | India |
| 447 | Madhya Pradesh | Madhya-Pradesh | 48 | India |
| 448 | Gujarat | Gujarat | 48 | India |
| 449 | Maharashtra | Maharashtra | 48 | India |
| 450 | Andhra Pradesh | Andhra-Pradesh | 48 | India |
| 451 | Karnataka | Karnataka | 48 | India |
| 452 | Tamil Nadu | Tamil-Nadu | 48 | India |
| 453 | Kerala | Kerala | 48 | India |
| 454 | Orissa | Orissa | 48 | India |
| 455 | Chhattisgarh | Chhattisgarh | 48 | India |
| 456 | Jharkhand | Jharkhand | 48 | India |
| 457 | West Bengal | West-Bengal | 48 | India |
| 458 | Bihar | Bihar | 48 | India |
| 459 | North Eastern India | North-Eastern-India | 48 | India |
| 460 | Sumatra | Sumatra | 49 | Indonesia |
| 461 | Java | Java | 49 | Indonesia |
| 462 | Kalimantan | Kalimantan | 49 | Indonesia |
| 463 | Lesser Sunda Islands | Lesser-Sunda-Islands | 49 | Indonesia |
| 464 | Sulawesi | Sulawesi | 49 | Indonesia |
| 465 | Maluku islands | Maluku-islands | 49 | Indonesia |
| 466 | Papua | Papua | 49 | Indonesia |
| 467 | Jerusalem district | Jerusalem-district | 58 | Israel |
| 468 | Nazareth North District | Nazareth-North-District | 58 | Israel |
| 469 | Haifa district | Haifa-district | 58 | Israel |
| 470 | Tel Aviv Center District | Tel-Aviv-Center-District | 58 | Israel |
| 471 | Beersheba South District | Beersheba-South-District | 58 | Israel |
| 472 | Kerman Province | Kerman-Province | 56 | Iran |
| 473 | Sistan and Baluchistan | Sistan-Baluchistan | 56 | Iran |
| 474 | South Khorasan | South-Khorasan | 56 | Iran |
| 475 | Razavi Khorasan | Razavi-Khorasan | 56 | Iran |
| 476 | Yazd | Yazd | 56 | Iran |
| 477 | Semnan | Semnan | 56 | Iran |
| 478 | Esfahan | Esfahan | 56 | Iran |
| 479 | Fars | Fars | 56 | Iran |
| 480 | Hormozgan | Hormozgan | 56 | Iran |
| 481 | Southwestern Iran | Southwestern-Iran | 56 | Iran |
| 482 | Northwestern Iran | Northwestern-Iran | 56 | Iran |
| 483 | Mazandaran and Golistan | Mazandaran-and-Golistan | 56 | Iran |
| 484 | Hokkaido | Hokkaido | 45 | Japan |
| 485 | Tohoku | Tohoku | 45 | Japan |
| 486 | Kanto | Kanto | 45 | Japan |
| 487 | Chubu | Chubu | 45 | Japan |
| 488 | Kinki | Kinki | 45 | Japan |
| 489 | Chugoku | Chugoku | 45 | Japan |
| 490 | Shikoku | Shikoku | 45 | Japan |
| 491 | Kyushu | Kyushu | 45 | Japan |
| 492 | Balochistan | Balochistan | 57 | Pakistan |
| 493 | North-West Frontier Province | North-West-Frontier | 57 | Pakistan |
| 494 | Punjab | Punjab | 57 | Pakistan |
| 495 | Sindh | Sindh | 57 | Pakistan |
| 497 | Eastern Cape | Eastern-Cape | 51 | South Africa |
| 498 | Free State | Free-State | 51 | South Africa |
| 499 | Gauteng | Gauteng | 51 | South Africa |
| 500 | KwaZulu Natal | KwaZulu-Natal | 51 | South Africa |
| 501 | Limpopo | Limpopo | 51 | South Africa |
| 502 | Mpumalanga | Mpumalanga | 51 | South Africa |
| 503 | North West Province | North-West-Province | 51 | South Africa |
| 504 | Northern Cape | Northern-Cape | 51 | South Africa |
| 505 | Western Cape | Western-Cape | 51 | South Africa |
| 507 | Central Thailand | Central-Thailand | 59 | Thailand |
| 508 | Northern Thailand | Northern-Thailand | 59 | Thailand |
| 509 | Eastern Thailand | Eastern-Thailand | 59 | Thailand |
| 510 | Southern Thailand | Southern-Thailand | 59 | Thailand |
| 511 | North-Eastern Thailand | North-Eastern-Thailand | 59 | Thailand |
| 512 | Aegean Coast of Turkey | Aegean-Coast-of-Turkey | 43 | Turkey |
| 513 | Black Sea Coast of Turkey | Black-Sea-Coast-of-Turkey | 43 | Turkey |
| 514 | Central Anatolia | Central-Anatolia | 43 | Turkey |
| 515 | Eastern Anatolia | Eastern-Anatolia | 43 | Turkey |
| 516 | Marmara | Marmara | 43 | Turkey |
| 517 | Mediterranean Coast of Turkey | Mediterranean-Coast-of-Turkey | 43 | Turkey |
| 518 | Southeastern Anatolia | Southeastern-Anatolia | 43 | Turkey |
| 519 | Gyeonggi-do | Gyeonggi | 47 | South Korea |
| 520 | Gangwon-do | Gangwon | 47 | South Korea |
| 521 | Chungcheongbuk-do | Chungcheongbuk | 47 | South Korea |
| 522 | Chungcheongnam-do | Chungcheongnam | 47 | South Korea |
| 523 | Jeollabuk-do | Jeollabuk | 47 | South Korea |
| 524 | Jeollanam-do | Jeollanam | 47 | South Korea |
| 525 | Gyeongsangbuk-do | Gyeongsangbuk | 47 | South Korea |
| 526 | Gyeongsangnam-do | Gyeongsangnam | 47 | South Korea |
| 527 | Jeju | Jeju | 47 | South Korea |
| 528 | Western Netherlands | Western-Netherlands | 31 | Netherlands |
| 529 | Southern Netherlands | Southern-Netherlands | 31 | Netherlands |
| 530 | Eastern Netherlands | Eastern-Netherlands | 31 | Netherlands |
| 531 | Northern Netherlands | Northern-Netherlands | 31 | Netherlands |
| 532 | Moscow and Central Russia | Moscow-and-Central-Russia | 41 | Russia |
| 533 | Central Black Earth | Central-Black-Earth | 41 | Russia |
| 534 | Eastern Siberia | Eastern-Siberia | 41 | Russia |
| 535 | Far Eastern Russia | Far-Eastern-Russia | 41 | Russia |
| 536 | Northern Russia | Northern-Russia | 41 | Russia |
| 537 | North Caucasus | North-Caucasus | 41 | Russia |
| 538 | Leningrad Oblast | Leningrad-Oblast | 41 | Russia |
| 540 | Urals | Urals | 41 | Russia |
| 541 | Volga Vyatka | Volga-Vyatka | 41 | Russia |
| 542 | Western Siberia | Western-Siberia | 41 | Russia |
| 543 | Kaliningrad | Kaliningrad | 41 | Russia |
| 544 | Volga | Volga | 41 | Russia |
| 549 | Gotland | Gotland | 38 | Sweden |
| 561 | Jammu and Kashmir | Jammu-Kashmir | 48 | India |
| 562 | Svalbard & Jan Mayen | Svalbard-Jan-Mayen | 37 | Norway |
| 571 | Slovenian Littoral | Slovenian-Littoral | 61 | Slovenia |
| 581 | Inner Carniola | Inner-Carniola | 61 | Slovenia |
| 591 | Upper Carniola | Upper-Carniola | 61 | Slovenia |
| 601 | Styria and Carinthia | Styria-Carinthia | 61 | Slovenia |
| 611 | Lower Carniola | Lower-Carniola | 61 | Slovenia |
| 621 | Prekmurje | Prekmurje | 61 | Slovenia |
| 622 | Slavonia | Slavonia | 63 | Croatia |
| 623 | Central Croatia | Central-Croatia | 63 | Croatia |
| 624 | Northwest Croatia | Northwest-Croatia | 63 | Croatia |
| 625 | Lika and Gorski Kotar | Lika-Gorski-Kotar | 63 | Croatia |
| 626 | Istria and Kvarner | Istria-Kvarner | 63 | Croatia |
| 627 | North Dalmatia | North-Dalmatia | 63 | Croatia |
| 628 | South Dalmatia | South-Dalmatia | 63 | Croatia |
| 629 | Norte Grande | Norte-Grande | 64 | Chile |
| 630 | Norte Chico | Norte-Chico | 64 | Chile |
| 631 | Zona Central | Zona-Central | 64 | Chile |
| 632 | Zona Sur | Zona-Sur | 64 | Chile |
| 633 | Zona Austral | Zona-Austral | 64 | Chile |
| 634 | Vojvodina | Vojvodina | 65 | Serbia |
| 635 | Belgrade | Belgrade | 65 | Serbia |
| 636 | Sumadija | Sumadija | 65 | Serbia |
| 637 | Eastern Serbia | Eastern-Serbia | 65 | Serbia |
| 638 | Western Serbia | Western-Serbia | 65 | Serbia |
| 639 | Raska | Raska | 65 | Serbia |
| 640 | Southern Serbia | Southern-Serbia | 65 | Serbia |
| 641 | Sabah | Sabah | 66 | Malaysia |
| 642 | Sarawak | Sarawak | 66 | Malaysia |
| 643 | Peninsular Malaysia | Peninsular-Malaysia | 66 | Malaysia |
| 644 | Luzon | Luzon | 67 | Philippines |
| 645 | Visayas | Visayas | 67 | Philippines |
| 646 | Mindanao | Mindanao | 67 | Philippines |
| 647 | Palawan | Palawan | 67 | Philippines |
| 648 | Singapore City | Singapore-City | 68 | Singapore |
| 649 | West Srpska Republic | West-Srpska-Republic | 69 | Bosnia and Herzegovina |
| 650 | East Srpska Republic | East-Srpska-Republic | 69 | Bosnia and Herzegovina |
| 651 | Brcko District | Brcko-District | 69 | Bosnia and Herzegovina |
| 652 | Federation of BiH | Federation-of-BiH | 69 | Bosnia and Herzegovina |
| 653 | Rio Grande do Sul | Rio-Grande-do-Sul | 9 | Brazil |
| 654 | Pohja-Eesti | Pohja-Eesti | 70 | Estonia |
| 655 | Kirde-Eesti | Kirde-Eesti | 70 | Estonia |
| 656 | Kesk-Eesti | Kesk-Eesti | 70 | Estonia |
| 657 | Laane-Eesti | Laane-Eesti | 70 | Estonia |
| 658 | Louna-Eesti | Louna-Eesti | 70 | Estonia |
| 659 | Vidzeme | Vidzeme | 71 | Latvia |
| 660 | Latgale | Latgale | 71 | Latvia |
| 661 | Zemgale | Zemgale | 71 | Latvia |
| 662 | Kurzeme | Kurzeme | 71 | Latvia |
| 663 | Lithuania Minor | Lithuania-Minor | 72 | Lithuania |
| 664 | Samogitia | Samogitia | 72 | Lithuania |
| 665 | Lithuanian Highland | Lithuanian-Highland | 72 | Lithuania |
| 666 | Dainava | Dainava | 72 | Lithuania |
| 667 | Sudovia | Sudovia | 72 | Lithuania |
| 668 | Chagang | Chagang | 73 | North Korea |
| 669 | Pyongan | Pyongan | 73 | North Korea |
| 670 | Hwangae | Hwangae | 73 | North Korea |
| 671 | Kangwon | Kangwon | 73 | North Korea |
| 672 | Hamgyong | Hamgyong | 73 | North Korea |
| 673 | Ryanggang | Ryanggang | 73 | North Korea |
| 674 | Charrua | Charrua | 74 | Uruguay |
| 675 | Paranena | Paranena | 75 | Paraguay |
| 676 | Central East Chaco | Central-East-Chaco | 75 | Paraguay |
| 677 | Chuquisaca and Tarija | Chuquisaca-and-Tarija | 76 | Bolivia |
| 678 | Beni and Cochabamba | Beni-and-Cochabamba | 76 | Bolivia |
| 679 | Santa Cruz | Santa-Cruz | 76 | Bolivia |
| 680 | Bolivian Altiplano | Bolivian-Altiplano | 76 | Bolivia |
| 681 | Pando | Pando | 76 | Bolivia |
| 682 | Great Andes | Great-Andes | 77 | Peru |
| 683 | Mid Andes | Mid-Andes | 77 | Peru |
| 684 | Low Andes | Low-Andes | 77 | Peru |
| 685 | Chimor | Chimor | 77 | Peru |
| 686 | Northern Low Amazon | Northern-Low-Amazon | 77 | Peru |
| 687 | Southern Low Amazon | Southern-Low-Amazon | 77 | Peru |
| 688 | Lima | Lima | 77 | Peru |
| 689 | Amazonica | Amazonica | 78 | Colombia |
| 690 | Andina | Andina | 78 | Colombia |
| 691 | Caribe e Insular | Caribe-e-Insular | 78 | Colombia |
| 692 | Orinoquia | Orinoquia | 78 | Colombia |
| 693 | Pacifica | Pacifica | 78 | Colombia |
| 694 | Cundiboyacense | Cundiboyacense | 78 | Colombia |
| 695 | Povardarie | Povardarie | 79 | North Macedonia |
| 696 | Western Macedonia | Western-Macedonia | 79 | North Macedonia |
| 697 | Eastern Macedonia | Eastern-Macedonia | 79 | North Macedonia |
| 698 | North Montenegrin Mountains | North-Montenegrin-Mountains | 80 | Montenegro |
| 699 | Central Montenegro | Central-Montenegro | 80 | Montenegro |
| 700 | Montenegrin Coast | Montenegrin-Coast | 80 | Montenegro |
| 701 | Northern Taiwan | Northern-Taiwan | 81 | Republic of China (Taiwan) |
| 702 | Central Taiwan | Central-Taiwan | 81 | Republic of China (Taiwan) |
| 703 | Eastern Taiwan | Eastern-Taiwan | 81 | Republic of China (Taiwan) |
| 704 | Southern Taiwan | Southern-Taiwan | 81 | Republic of China (Taiwan) |
| 705 | Southern Cyprus | Southern-Cyprus | 82 | Cyprus |
| 706 | Northern Cyprus | Northern-Cyprus | 82 | Cyprus |
| 707 | Brestskaya | Brestskaya | 83 | Belarus |
| 708 | Homelskaya | Homelskaya | 83 | Belarus |
| 709 | Hrodzienskaya | Hrodzienskaya | 83 | Belarus |
| 710 | Minskaya | Minskaya | 83 | Belarus |
| 711 | Mahilyowskaya | Mahilyowskaya | 83 | Belarus |
| 712 | Vitsebskaya | Vitsebskaya | 83 | Belarus |
| 713 | Auckland | Auckland | 84 | New Zealand |
| 714 | Wellington | Wellington | 84 | New Zealand |
| 715 | Canterbury | Canterbury | 84 | New Zealand |
| 716 | Otago | Otago | 84 | New Zealand |
| 717 | Al Riyadh | Al-Riyadh | 164 | Saudi Arabia |
| 718 | Al Bahah | Al-Bahah | 164 | Saudi Arabia |
| 719 | Northern Borders | Northern-Borders | 164 | Saudi Arabia |
| 720 | Al Jawf | Al-Jawf | 164 | Saudi Arabia |
| 721 | Al Madinah | Al-Madinah | 164 | Saudi Arabia |
| 722 | Al Qasim | Al-Qasim | 164 | Saudi Arabia |
| 723 | Ha'il | Ha-il | 164 | Saudi Arabia |
| 724 | Asir | Asir | 164 | Saudi Arabia |
| 725 | Eastern Province | Eastern-Province | 164 | Saudi Arabia |
| 726 | Tabuk | Tabuk | 164 | Saudi Arabia |
| 727 | Najran | Najran | 164 | Saudi Arabia |
| 728 | Makkah | Makkah | 164 | Saudi Arabia |
| 729 | Jizan | Jizan | 164 | Saudi Arabia |
| 730 | Sinai | Sinai | 165 | Egypt |
| 731 | Lower Egypt | Lower-Egypt | 165 | Egypt |
| 732 | Western Desert | Western-Desert | 165 | Egypt |
| 733 | Middle Egypt | Middle-Egypt | 165 | Egypt |
| 734 | Upper Egypt | Upper-Egypt | 165 | Egypt |
| 735 | Red Sea Coast | Red-Sea-Coast | 165 | Egypt |
| 736 | Abu Dhabi | Abu-Dhabi | 166 | United Arab Emirates |
| 737 | Dubai | Dubai | 166 | United Arab Emirates |
| 738 | Sharjah | Sharjah | 166 | United Arab Emirates |
| 739 | Ajman | Ajman | 166 | United Arab Emirates |
| 740 | Ras al-Khaimah | Ras-al-Khaimah | 166 | United Arab Emirates |
| 741 | Umm al Quwain | Umm-al-Quwain | 166 | United Arab Emirates |
| 742 | Fujairah | Fujairah | 166 | United Arab Emirates |
| 743 | Kosovo | Kosovo | 65 | Serbia |
| 744 | Tirana | Tirana | 167 | Albania |
| 745 | Albanian Coast | Albanian-Coast | 167 | Albania |
| 746 | Southeastern Albania | Southeastern-Albania | 167 | Albania |
| 747 | Abkhazia | Abkhazia | 168 | Georgia |
| 748 | West Georgia | West-Georgia | 168 | Georgia |
| 749 | Lower Kartli | Lower-Kartli | 168 | Georgia |
| 750 | Inner Kartli | Inner-Kartli | 168 | Georgia |
| 751 | Kakheti | Kakheti | 168 | Georgia |
| 752 | Northern Armenia | Northern-Armenia | 169 | Armenia |
| 753 | Central Armenia | Central-Armenia | 169 | Armenia |
| 754 | Syunik | Syunik | 169 | Armenia |
| 755 | Gegharkunik | Gegharkunik | 169 | Armenia |
| 756 | North West States | North-West-States | 170 | Nigeria |
| 757 | North East States | North-East-States | 170 | Nigeria |
| 758 | North Central States | North-Central-States | 170 | Nigeria |
| 759 | South West States | South-West-States | 170 | Nigeria |
| 760 | South South States | South-South-States | 170 | Nigeria |
| 761 | South East States | South-East-States | 170 | Nigeria |
| 762 | Western Cuba | Western-Cuba | 171 | Cuba |
| 763 | Las Villas | Las-Villas | 171 | Cuba |
| 764 | Oriente | Oriente | 171 | Cuba |

> **Note:** Region IDs are non-sequential with gaps throughout the range. The `travelData` endpoint returns 573 regions (excluding the citizen's current region); region 663 (Lithuania Minor) was added separately. Total: **574 regions**.

### Usage

- **Region page:** `/en/main/region/{permalink}`
- **Region ID** is used in travel requests (`regionId` parameter), battle data, and citizen location
- Regions can change hands through wars — use `isConquered`, `isConqueredFrom`, and `isConqueredBy` fields from travelData to determine current status

---

## Regions by Country

Quick reference showing how many regions originally belong to each country:

| Country | ID | Region Count |
|---------|-----|-------------|
| Romania | 1 | 9 |
| Brazil | 9 | 6 |
| Italy | 10 | 20 |
| France | 11 | 22 |
| Germany | 12 | 13 |
| Hungary | 13 | 7 |
| China | 14 | 29 |
| Spain | 15 | 17 |
| Canada | 23 | 13 |
| USA | 24 | 51 |
| Mexico | 26 | 8 |
| Argentina | 27 | 6 |
| Venezuela | 28 | 8 |
| United Kingdom | 29 | 12 |
| Switzerland | 30 | 4 |
| Netherlands | 31 | 4 |
| Belgium | 32 | 3 |
| Austria | 33 | 8 |
| Czech Republic | 34 | 3 |
| Poland | 35 | 6 |
| Slovakia | 36 | 4 |
| Norway | 37 | 6 |
| Sweden | 38 | 8 |
| Finland | 39 | 6 |
| Ukraine | 40 | 13 |
| Russia | 41 | 12 |
| Bulgaria | 42 | 6 |
| Turkey | 43 | 7 |
| Greece | 44 | 10 |
| Japan | 45 | 8 |
| South Korea | 47 | 9 |
| India | 48 | 17 |
| Indonesia | 49 | 7 |
| Australia | 50 | 7 |
| South Africa | 51 | 9 |
| Republic of Moldova | 52 | 4 |
| Portugal | 53 | 7 |
| Ireland | 54 | 6 |
| Denmark | 55 | 5 |
| Iran | 56 | 12 |
| Pakistan | 57 | 4 |
| Israel | 58 | 5 |
| Thailand | 59 | 5 |
| Slovenia | 61 | 6 |
| Croatia | 63 | 7 |
| Chile | 64 | 5 |
| Serbia | 65 | 8 |
| Malaysia | 66 | 3 |
| Philippines | 67 | 4 |
| Singapore | 68 | 1 |
| Bosnia and Herzegovina | 69 | 4 |
| Estonia | 70 | 5 |
| Latvia | 71 | 4 |
| Lithuania | 72 | 5 |
| North Korea | 73 | 6 |
| Uruguay | 74 | 1 |
| Paraguay | 75 | 2 |
| Bolivia | 76 | 5 |
| Peru | 77 | 7 |
| Colombia | 78 | 6 |
| North Macedonia | 79 | 3 |
| Montenegro | 80 | 3 |
| Republic of China (Taiwan) | 81 | 4 |
| Cyprus | 82 | 2 |
| Belarus | 83 | 6 |
| New Zealand | 84 | 4 |
| Saudi Arabia | 164 | 13 |
| Egypt | 165 | 6 |
| United Arab Emirates | 166 | 7 |
| Albania | 167 | 3 |
| Georgia | 168 | 5 |
| Armenia | 169 | 4 |
| Nigeria | 170 | 6 |
| Cuba | 171 | 3 |

---

## Related

- [API Table of Contents](API_TOC.md)
- [World & Travel API](API_WORLD.md) — travelData endpoint documentation
- [Country API](API_COUNTRY.md) — country page endpoints
