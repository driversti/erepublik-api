# ID tables

[API docs](../README.md) › Basics › ID tables

Numeric IDs and permalinks of countries and regions, as used in paths and payloads. Countries and original owners rarely change; capitals and population do.

| Table | Rows | Last compared with the game |
|-------|------|-----------------------------|
| [Countries](#countries) | 74 | 2026-11-27: IDs, names and permalinks match `erepublik.info.countries` |
| [Regions and cities](#regions-and-cities) | 574 | 2026-11-27: IDs, region names, original owners, cities and coordinates match `map-data`; capitals updated |
| [Regions by country](#regions-by-country) | — | Derived from original owners |

---

## Where the data comes from

| Source | Gives |
|--------|-------|
| `erepublik.info.countries` (JavaScript global on game pages) | `id`, `name`, `permalink`, `isEmpire`, `isDictatorship` |
| [`GET /en/main/map-data`](../world/map.md) | Per region: `region`, `city`, `original_country`, `current_country`, `resources` and battle info |
| [`POST /en/main/travelData`](../world/travel.md) | Source of the older region tables (`countryId`, `isConqueredFrom`) |

The global also holds entries that are not countries: `0` and `overall` ("Overall") and `1000` ("blank"). Filter them out.

## Using the IDs

- **ID** is used in request and response fields such as `countryId` and `citizenshipCountryId`.
- **Permalink** is used in paths, for example `/en/country/society/{permalink}` and `/en/main/region/{permalink}`.
- Flags: `//www.erepublik.net/images/flags/{size}/{permalink}.gif` (`S`, `M`, `L`, `XL`) and `//www.erepublik.net/images/flags_png/{size}/{permalink}.png`.
- An **original owner** is the country a region natively belongs to. Conquest changes who controls it, not the original owner.

---

## Countries

74 countries, ID range 1–171. IDs 2–8, 16–22, 25, 46, 60, 62 and 85–163 are not used.

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

---

## Regions and cities

574 regions, ID range 3–764. ★ marks a capital as reported by `map-data` on 2026-11-27 (61 regions). Population was captured on 2026-06-14. Coordinates are the city's latitude and longitude.

<details>
<summary>All 574 regions</summary>

| ID | Region | Permalink | Owner ID | Original owner | City | Cap | Pop | Coordinates (lat, lon) |
|----|--------|-----------|----------|----------------|------|-----|-----|------------------------|
| 3 | Dobrogea | Dobrogea | 1 | Romania | Constanta |  | 98 | 44.173333, 28.638333 |
| 5 | Muntenia | Muntenia | 1 | Romania | Bucharest |  | 106 | 44.437711, 26.097367 |
| 9 | Oltenia | Oltenia | 1 | Romania | Craiova |  | 129 | 44.32476, 23.813471 |
| 11 | Banat | Banat | 1 | Romania | Timisoara |  | 106 | 45.755539, 21.237499 |
| 35 | Transilvania | Transilvania | 1 | Romania | Cluj-Napoca |  | 135 | 46.777248, 23.59989 |
| 36 | Crisana | Crisana | 1 | Romania | Oradea |  | 116 | 47.072222, 21.921111 |
| 37 | Moldova | Moldova | 1 | Romania | Iasi |  | 172 | 47.156944, 27.590278 |
| 38 | Maramures | Maramures | 1 | Romania | Baia Mare |  | 39 | 47.666667, 23.583333 |
| 39 | Bucovina | Bucovina | 1 | Romania | Botosani |  | 133 | 47.746231, 26.666809 |
| 40 | Alabama | Alabama | 24 | USA | Montgomery |  | 15 | 32.366805, -86.299969 |
| 41 | Alaska | Alaska | 24 | USA | Juneau |  | 15 | 58.301944, -134.419722 |
| 42 | Arizona | Arizona | 24 | USA | Phoenix |  | 33 | 33.448377, -112.074037 |
| 43 | Arkansas | Arkansas | 24 | USA | Little Rock |  | 9 | 34.746481, -92.289595 |
| 44 | California | California | 24 | USA | Sacramento |  | 70 | 38.581572, -121.4944 |
| 45 | Colorado | Colorado | 24 | USA | Denver |  | 23 | 39.737567, -104.984718 |
| 46 | Connecticut | Connecticut | 24 | USA | Hartford |  | 7 | 41.763711, -72.685093 |
| 47 | Delaware | Delaware | 24 | USA | Dover |  | 15 | 39.158168, -75.524368 |
| 48 | Florida | Florida | 24 | USA | Tallahassee |  | 29 | 30.438256, -84.280733 |
| 49 | Georgia | Georgia | 24 | USA | Atlanta |  | 14 | 33.748995, -84.387982 |
| 50 | Hawaii | Hawaii | 24 | USA | Honolulu |  | 63 | 21.306944, -157.858333 |
| 51 | Idaho | Idaho | 24 | USA | Boise |  | 13 | 43.613739, -116.237651 |
| 52 | Illinois | Illinois | 24 | USA | Springfield |  | 8 | 39.80172, -89.64371 |
| 53 | Indiana | Indiana | 24 | USA | Indianapolis |  | 8 | 39.768516, -86.158074 |
| 54 | Iowa | Iowa | 24 | USA | Des Moines |  | 8 | 41.600545, -93.609106 |
| 55 | Kansas | Kansas | 24 | USA | Topeka |  | 9 | 39.055824, -95.689018 |
| 56 | Kentucky | Kentucky | 24 | USA | Frankfort | ★ | 131 | 38.200906, -84.873284 |
| 57 | Louisiana | Louisiana | 24 | USA | Baton Rouge |  | 10 | 30.458283, -91.14032 |
| 58 | Maine | Maine | 24 | USA | Augusta |  | 16 | 44.31062, -69.77949 |
| 59 | Maryland | Maryland | 24 | USA | Annapolis |  | 10 | 38.978445, -76.492183 |
| 60 | Massachusetts | Massachusetts | 24 | USA | Boston |  | 10 | 42.358431, -71.059773 |
| 61 | Michigan | Michigan | 24 | USA | Lansing |  | 12 | 42.732535, -84.555535 |
| 62 | Minnesota | Minnesota | 24 | USA | Saint Paul |  | 15 | 44.953703, -93.089958 |
| 63 | Mississippi | Mississippi | 24 | USA | Jackson |  | 11 | 32.298757, -90.18481 |
| 64 | Missouri | Missouri | 24 | USA | Jefferson City |  | 10 | 38.576702, -92.173516 |
| 65 | Montana | Montana | 24 | USA | Helena |  | 10 | 46.595806, -112.027031 |
| 66 | Nebraska | Nebraska | 24 | USA | Lincoln |  | 23 | 40.806862, -96.681679 |
| 67 | Nevada | Nevada | 24 | USA | Carson City |  | 11 | 39.163798, -119.767403 |
| 68 | New Hampshire | New-Hampshire | 24 | USA | Concord |  | 8 | 43.20814, -71.53757 |
| 69 | New Jersey | New-Jersey | 24 | USA | Trenton |  | 5 | 40.217053, -74.742938 |
| 70 | New Mexico | New-Mexico | 24 | USA | Santa Fe |  | 14 | 35.686975, -105.937799 |
| 71 | New York | New-York | 24 | USA | Albany |  | 12 | 42.652579, -73.756232 |
| 72 | North Carolina | North-Carolina | 24 | USA | Raleigh |  | 6 | 35.772096, -78.638615 |
| 73 | North Dakota | North-Dakota | 24 | USA | Bismarck |  | 25 | 46.808327, -100.783739 |
| 74 | Ohio | Ohio | 24 | USA | Columbus |  | 12 | 39.961176, -82.998794 |
| 75 | Oklahoma | Oklahoma | 24 | USA | Oklahoma City |  | 12 | 35.46756, -97.516428 |
| 76 | Oregon | Oregon | 24 | USA | Salem |  | 14 | 44.9429, -123.0351 |
| 77 | Pennsylvania | Pennsylvania | 24 | USA | Harrisburg |  | 15 | 40.2737, -76.884418 |
| 78 | Rhode Island | Rhode-Island | 24 | USA | Providence |  | 9 | 41.823989, -71.412834 |
| 79 | South Carolina | South-Carolina | 24 | USA | Columbia |  | 10 | 34.00071, -81.034814 |
| 80 | South Dakota | South-Dakota | 24 | USA | Pierre |  | 7 | 44.368316, -100.350967 |
| 81 | Tennessee | Tennessee | 24 | USA | Nashville |  | 17 | 36.166667, -86.783333 |
| 82 | Texas | Texas | 24 | USA | Austin |  | 16 | 30.267153, -97.743061 |
| 83 | Utah | Utah | 24 | USA | Salt Lake City |  | 24 | 40.760779, -111.891047 |
| 84 | Vermont | Vermont | 24 | USA | Montpelier |  | 14 | 44.260059, -72.575387 |
| 85 | Virginia | Virginia | 24 | USA | Richmond |  | 45 | 37.540725, -77.436048 |
| 86 | Washington | Washington | 24 | USA | Olympia |  | 16 | 47.037874, -122.900695 |
| 87 | West Virginia | West-Virginia | 24 | USA | Charleston |  | 33 | 38.34982, -81.63262 |
| 88 | Wisconsin | Wisconsin | 24 | USA | Madison |  | 14 | 43.073052, -89.40123 |
| 89 | Wyoming | Wyoming | 24 | USA | Cheyenne |  | 10 | 41.139981, -104.820246 |
| 90 | District of Columbia | District-of-Columbia | 24 | USA | Washington D.C. |  | 67 | 38.895112, -77.036366 |
| 91 | Northern Basarabia | Northern-Basarabia | 52 | Republic of Moldova | Balti |  | 18 | 47.75494, 27.913 |
| 92 | Chisinau | Chisinau | 52 | Republic of Moldova | Chisinau |  | 19 | 47.026859, 28.841551 |
| 93 | Southern Basarabia | Southern-Basarabia | 52 | Republic of Moldova | Cahul |  | 37 | 45.9075, 28.19444 |
| 94 | Transnistria | Transnistria | 52 | Republic of Moldova | Tiraspol | ★ | 45 | 46.845238, 29.633101 |
| 95 | Ontario | Ontario | 23 | Canada | Ottawa |  | 24 | 45.42153, -75.697193 |
| 96 | Prince Edward Island | Prince-Edward-Island | 23 | Canada | Charlottetown |  | 19 | 46.23824, -63.13107 |
| 97 | Alberta | Alberta | 23 | Canada | Edmonton |  | 22 | 53.544389, -113.490927 |
| 98 | New Brunswick | New-Brunswick | 23 | Canada | Fredericton |  | 41 | 45.96359, -66.643115 |
| 99 | Nova Scotia | Nova-Scotia | 23 | Canada | Halifax |  | 21 | 44.648863, -63.57532 |
| 100 | Quebec | Quebec | 23 | Canada | Quebec City |  | 15 | 46.816667, -71.216667 |
| 101 | Saskatchewan | Saskatchewan | 23 | Canada | Regina |  | 23 | 50.454722, -104.606667 |
| 102 | Newfoundland and Labrador | Newfoundland | 23 | Canada | St. Johns |  | 51 | 47.560541, -52.712832 |
| 103 | British Columbia | British-Columbia | 23 | Canada | Victoria |  | 17 | 48.428421, -123.365644 |
| 104 | Yukon | Yukon | 23 | Canada | Whitehorse |  | 27 | 60.721187, -135.056845 |
| 105 | Manitoba | Manitoba | 23 | Canada | Winnipeg |  | 28 | 49.899754, -97.137494 |
| 106 | Northwest Territories | Northwest-Territories | 23 | Canada | Yellowknife | ★ | 73 | 62.453972, -114.371789 |
| 107 | Nunavut | Nunavut | 23 | Canada | Iqaluit |  | 25 | 63.746693, -68.516967 |
| 108 | Western Transdanubia | Western-Transdanubia | 13 | Hungary | Gyor |  | 209 | 47.684051, 17.635101 |
| 109 | Southern Transdanubia | Southern-Transdanubia | 13 | Hungary | Pecs |  | 137 | 46.071299, 18.233187 |
| 110 | Central Transdanubia | Central-Transdanubia | 13 | Hungary | Szekesfehervar |  | 172 | 47.188716, 18.413809 |
| 111 | Central Hungary | Central-Hungary | 13 | Hungary | Budapest |  | 136 | 47.498406, 19.040758 |
| 112 | Northern Hungary | Northern-Hungary | 13 | Hungary | Miskolc | ★ | 154 | 48.104385, 20.791356 |
| 113 | Northern Great Plain | Northern-Great-Plain | 13 | Hungary | Debrecen |  | 142 | 47.529974, 21.639357 |
| 114 | Southern Great Plain | Southern-Great-Plain | 13 | Hungary | Szeged |  | 147 | 46.253617, 20.146135 |
| 115 | Valley of Mexico | Valley-of-Mexico | 26 | Mexico | Mexico City |  | 61 | 19.432608, -99.133208 |
| 116 | Baja | Baja | 26 | Mexico | Mexicali |  | 58 | 32.64557, -115.445342 |
| 117 | Northwest of Mexico | Northwest-of-Mexico | 26 | Mexico | Culiacan |  | 90 | 24.80485, -107.385498 |
| 118 | Pacific Coast of Mexico | Pacific-Coast-of-Mexico | 26 | Mexico | Guadalajara |  | 57 | 20.666667, -103.35 |
| 119 | Oaxaca | Oaxaca | 26 | Mexico | Oaxaca |  | 171 | 17.083333, -96.75 |
| 120 | Gulf of Mexico | Gulf-of-Mexico | 26 | Mexico | Veracruz |  | 67 | 19.2, -96.13333 |
| 121 | Southeast of Mexico | Southeast-of-Mexico | 26 | Mexico | Campeche |  | 76 | 18.931225, -90.261807 |
| 122 | Northeast of Mexico | Northeast-of-Mexico | 26 | Mexico | Monterrey |  | 27 | 25.673211, -100.309201 |
| 123 | Venezuelan Andean | Venezuelan-Andean | 28 | Venezuela | Barinas |  | 48 | 8.630485, -70.209908 |
| 124 | Venezuelan Capital | Venezuelan-Capital | 28 | Venezuela | Caracas |  | 44 | 10.491016, -66.902061 |
| 125 | Central Venezuela | Central-Venezuela | 28 | Venezuela | Valencia (Venezuela) |  | 67 | 10.174127, -67.999815 |
| 126 | Central Western Venezuela | Central-Western-Venezuela | 28 | Venezuela | Barquisimeto |  | 57 | 10.063611, -69.334722 |
| 127 | Guayana | Guayana | 28 | Venezuela | Puerto Ayacucho |  | 56 | 5.665781, -67.634697 |
| 129 | Llanos | Llanos | 28 | Venezuela | San Juan de Los Morros |  | 61 | 9.901082, -67.354408 |
| 130 | North Eastern Venezuela | North-Eastern-Venezuela | 28 | Venezuela | Barcelona (Venezuela) |  | 39 | 10.133, -64.691994 |
| 131 | Zulian | Zulian | 28 | Venezuela | Maracaibo |  | 45 | 10.64504, -71.637154 |
| 132 | Subcarpathia | Subcarpathia | 40 | Ukraine | Uzhhorod |  | 73 | 48.6208, 22.287883 |
| 133 | Galicia and Lodomeria | Galicia-and-Lodomeria | 40 | Ukraine | Lviv |  | 78 | 49.839683, 24.029717 |
| 134 | Volhynia | Volhynia | 40 | Ukraine | Rivne |  | 101 | 50.6199, 26.251617 |
| 135 | Polisia | Polisia | 40 | Ukraine | Zhytomyr | ★ | 115 | 50.25465, 28.658667 |
| 136 | Podolia | Podolia | 40 | Ukraine | Kamianets-Podilskyi |  | 48 | 48.683333, 26.583333 |
| 137 | Bukovyna | Bukovyna | 40 | Ukraine | Chernivtsi |  | 62 | 48.291683, 25.935217 |
| 138 | Dnipro | Dnipro | 40 | Ukraine | Kyiv |  | 43 | 50.4501, 30.5234 |
| 139 | Siveria | Siveria | 40 | Ukraine | Chernihiv |  | 100 | 51.4982, 31.28935 |
| 140 | Bassarabia | Bassarabia | 40 | Ukraine | Odessa |  | 68 | 46.484583, 30.7326 |
| 141 | Zaporozhia | Zaporozhia | 40 | Ukraine | Zaporizhia |  | 87 | 47.8388, 35.139567 |
| 142 | Sloboda | Sloboda | 40 | Ukraine | Kharkiv |  | 141 | 49.9935, 36.230383 |
| 143 | Donbas | Donbas | 40 | Ukraine | Donetsk |  | 165 | 48.015883, 37.80285 |
| 144 | Taurida | Taurida | 40 | Ukraine | Simferopol |  | 37 | 44.948056, 34.104167 |
| 146 | Center West of Brazil | Center-West-of-Brazil | 9 | Brazil | Brasilia |  | 205 | -15.780148, -47.92917 |
| 147 | North of Brazil | North-of-Brazil | 9 | Brazil | Manaus |  | 396 | -3.106409, -60.02643 |
| 148 | Northeast of Brazil | Northeast-of-Brazil | 9 | Brazil | Salvador | ★ | 246 | -12.970382, -38.512382 |
| 149 | Southeast of Brazil | Southeast-of-Brazil | 9 | Brazil | Sao Paulo |  | 493 | -23.5475, -46.63611 |
| 150 | Parana and Santa Catarina | Parana-and-Santa-Catarina | 9 | Brazil | Curitiba |  | 281 | -25.428356, -49.273252 |
| 151 | Pampas | Pampas | 27 | Argentina | Buenos Aires |  | 118 | -34.603723, -58.381593 |
| 152 | Argentine Northwest | Argentine-Northwest | 27 | Argentina | San Miguel de Tucuman |  | 28 | -26.808285, -65.21759 |
| 153 | South East Chaco | South-East-Chaco | 27 | Argentina | Formosa |  | 30 | -26.185201, -58.17537 |
| 154 | Mesopotamia | Mesopotamia | 27 | Argentina | Corrientes |  | 60 | -27.471226, -58.839584 |
| 155 | Cuyo | Cuyo | 27 | Argentina | Mendoza |  | 37 | -32.890183, -68.84405 |
| 156 | Patagonia | Patagonia | 27 | Argentina | Neuquen | ★ | 130 | -38.952444, -68.064139 |
| 157 | Lisboa | Lisboa | 53 | Portugal | Lisbon |  | 61 | 38.725299, -9.150036 |
| 158 | Norte | Norte | 53 | Portugal | Porto |  | 48 | 41.165056, -8.602816 |
| 159 | Centro | Centro | 53 | Portugal | Coimbra |  | 43 | 40.206569, -8.414618 |
| 160 | Alentejo | Alentejo | 53 | Portugal | Evora |  | 51 | 38.571431, -7.913502 |
| 161 | Algarve | Algarve | 53 | Portugal | Faro |  | 41 | 37.019355, -7.93044 |
| 162 | Azores | Azores | 53 | Portugal | Ponta Delgada |  | 44 | 37.742242, -25.67495 |
| 163 | Madeira | Madeira | 53 | Portugal | Funchal | ★ | 92 | 32.666933, -16.924055 |
| 166 | Madrid | Madrid | 15 | Spain | Madrid |  | 21 | 40.416691, -3.700345 |
| 167 | Andalucia | Andalucia | 15 | Spain | Sevilla |  | 65 | 37.38264, -5.996295 |
| 168 | Aragon | Aragon | 15 | Spain | Zaragoza |  | 32 | 41.656831, -0.87991 |
| 169 | Asturias | Asturias | 15 | Spain | Oviedo |  | 42 | 43.360299, -5.844781 |
| 170 | Basque Country | Basque-Country | 15 | Spain | Vitoria Gasteiz |  | 23 | 42.846718, -2.671635 |
| 171 | Cantabria | Cantabria | 15 | Spain | Santander |  | 38 | 43.46096, -3.807934 |
| 173 | Castilla y Leon | Castilla-Leon | 15 | Spain | Valladolid |  | 47 | 41.652943, -4.728381 |
| 174 | Catalonia | Catalonia | 15 | Spain | Barcelona |  | 41 | 41.387917, 2.169919 |
| 175 | Extremadura | Extremadura | 15 | Spain | Merida |  | 29 | 38.915859, -6.345606 |
| 176 | Galicia | Galicia-Spain | 15 | Spain | Santiago de Compostela |  | 30 | 42.880447, -8.546303 |
| 177 | Murcia | Murcia | 15 | Spain | Murcia |  | 26 | 37.983445, -1.12989 |
| 178 | Navarra | Navarra | 15 | Spain | Pamplona |  | 21 | 42.817988, -1.644184 |
| 179 | La Rioja | La-Rioja | 15 | Spain | Logrono |  | 33 | 42.465681, -2.449983 |
| 180 | Valencian Community | Valencian | 15 | Spain | Valencia |  | 11 | 39.470239, -0.376805 |
| 181 | Castilla La Mancha | Castilla-La-Mancha | 15 | Spain | Toledo |  | 76 | 39.856778, -4.024476 |
| 183 | Canary Islands | Canary-Islands | 15 | Spain | Las Palmas de Gran Canaria | ★ | 150 | 28.124823, -15.430007 |
| 184 | Balearic Islands | Balearic-Islands | 15 | Spain | Palma de Mallorca |  | 42 | 39.569506, 2.649966 |
| 185 | Alsace | Alsace | 11 | France | Strasbourg |  | 49 | 48.583148, 7.747882 |
| 186 | Aquitaine | Aquitaine | 11 | France | Bordeaux |  | 40 | 44.837789, -0.57918 |
| 187 | Auvergne | Auvergne | 11 | France | Clermont-Ferrand |  | 33 | 45.777222, 3.087025 |
| 188 | Brittany | Brittany | 11 | France | Rennes |  | 29 | 48.113475, -1.675708 |
| 189 | Burgundy | Burgundy | 11 | France | Dijon |  | 36 | 47.322047, 5.04148 |
| 190 | Loire Valley | Loire-Valley | 11 | France | Orleans |  | 27 | 47.902964, 1.909251 |
| 191 | Champagne Ardenne | Champagne-Ardenne | 11 | France | Chalons-en-Champagne |  | 27 | 48.956682, 4.363073 |
| 192 | Corsica | Corsica | 11 | France | Ajaccio |  | 27 | 41.919229, 8.738635 |
| 193 | Franche-comte | Franche-comte | 11 | France | Besancon |  | 39 | 47.237829, 6.024054 |
| 194 | Languedoc Roussillon | Languedoc-Roussillon | 11 | France | Montpellier |  | 33 | 43.610769, 3.876716 |
| 195 | Limousin | Limousin | 11 | France | Limoges |  | 31 | 45.833619, 1.261105 |
| 196 | Lorraine | Lorraine | 11 | France | Metz |  | 35 | 49.119666, 6.176905 |
| 197 | Lower Normandy | Lower-Normandy | 11 | France | Caen |  | 28 | 49.182863, -0.370679 |
| 198 | Midi-Pyrenees | Midi-Pyrenees | 11 | France | Toulouse | ★ | 77 | 43.604652, 1.444209 |
| 199 | Paris Isle of France | Paris-Isle-of-France | 11 | France | Paris |  | 31 | 48.856614, 2.352222 |
| 200 | Pays de la Loire | Pays-de-la-Loire | 11 | France | Nantes |  | 22 | 47.218371, -1.553621 |
| 201 | Picardy | Picardy | 11 | France | Amiens |  | 25 | 49.894067, 2.295753 |
| 202 | Poitou Charentes | Poitou-Charentes | 11 | France | Poitiers |  | 21 | 46.580224, 0.340375 |
| 203 | Provence Alpes Azur | Provence-Alpes-Azur | 11 | France | Marseille |  | 33 | 43.296482, 5.36978 |
| 204 | Rhone Alps | Rhone-Alps | 11 | France | Lyon |  | 27 | 45.764043, 4.835659 |
| 205 | Upper Normandy | Upper-Normandy | 11 | France | Rouen |  | 27 | 49.443232, 1.099971 |
| 207 | North Calais | North-Calais | 11 | France | Lille |  | 54 | 50.62925, 3.057256 |
| 208 | Dublin | Dublin | 54 | Ireland | Dublin | ★ | 39 | 53.349443, -6.260082 |
| 209 | Cork | Cork | 54 | Ireland | Cork |  | 15 | 51.896892, -8.486316 |
| 210 | Shannon | Shannon | 54 | Ireland | Limerick |  | 14 | 52.6652, -8.6238 |
| 212 | Mayo | Mayo | 54 | Ireland | Castlebar |  | 9 | 53.8608, -9.2988 |
| 213 | Wexford | Wexford | 54 | Ireland | Waterford |  | 12 | 52.252729, -7.125608 |
| 215 | Louth | Louth | 54 | Ireland | Dundalk |  | 9 | 53.997945, -6.405957 |
| 216 | London | London | 29 | United Kingdom | London |  | 27 | 51.507335, -0.127683 |
| 217 | Scotland | Scotland | 29 | United Kingdom | Edinburgh |  | 28 | 55.953252, -3.188267 |
| 218 | Wales | Wales | 29 | United Kingdom | Cardiff |  | 23 | 51.481581, -3.17909 |
| 219 | Northern Ireland | Northern-Ireland | 29 | United Kingdom | Belfast |  | 17 | 54.597443, -5.934068 |
| 220 | South East of England | South-East-of-England | 29 | United Kingdom | Oxford |  | 15 | 51.751724, -1.255285 |
| 221 | South West of England | South-West-of-England | 29 | United Kingdom | Bristol |  | 27 | 51.454513, -2.58791 |
| 222 | East Midlands | East-Midlands | 29 | United Kingdom | Nottingham |  | 26 | 52.954783, -1.158109 |
| 223 | West Midlands | West-Midlands | 29 | United Kingdom | Birmingham |  | 28 | 52.486243, -1.890401 |
| 224 | East of England | East-of-England | 29 | United Kingdom | Norwich |  | 23 | 52.630886, 1.297355 |
| 225 | Yorkshire & Humberside | Yorkshire-Humberside | 29 | United Kingdom | Leeds |  | 65 | 53.801279, -1.548567 |
| 226 | North East of England | North-East-of-England | 29 | United Kingdom | Newcastle upon Tyne |  | 28 | 54.978252, -1.61778 |
| 227 | North West of England | North-West-of-England | 29 | United Kingdom | Manchester |  | 21 | 53.466667, -2.233333 |
| 228 | Brussels | Brussels | 32 | Belgium | Brussels | ★ | 39 | 50.85034, 4.35171 |
| 229 | Flanders | Flanders | 32 | Belgium | Antwerp |  | 26 | 51.219216, 4.402882 |
| 230 | Wallonia | Wallonia | 32 | Belgium | Namur |  | 31 | 50.465328, 4.867665 |
| 231 | Hovedstaden | Hovedstaden | 55 | Denmark | Copenhagen | ★ | 24 | 55.67594, 12.56553 |
| 232 | Midtjylland | Midtjylland | 55 | Denmark | Viborg |  | 31 | 56.452027, 9.396347 |
| 233 | Nordjylland | Nordjylland | 55 | Denmark | Aalborg |  | 29 | 57.028811, 9.917771 |
| 235 | Sjaelland | Sjaelland | 55 | Denmark | Soro |  | 24 | 55.442645, 11.560937 |
| 236 | Syddanmark | Syddanmark | 55 | Denmark | Vejle |  | 26 | 55.711311, 9.536354 |
| 237 | Southern Finland | Southern-Finland | 39 | Finland | Helsinki |  | 10 | 60.169845, 24.938551 |
| 238 | Western Finland | Western-Finland | 39 | Finland | Turku |  | 37 | 60.449249, 22.259239 |
| 239 | Eastern Finland | Eastern-Finland | 39 | Finland | Mikkeli |  | 32 | 61.685807, 27.273488 |
| 240 | Oulu | Oulu | 39 | Finland | Oulu |  | 28 | 65.012615, 25.471453 |
| 241 | Lapland | Lapland | 39 | Finland | Rovaniemi | ★ | 49 | 66.497022, 25.724999 |
| 242 | Aland | Aland | 39 | Finland | Mariehamn |  | 14 | 60.097095, 19.934834 |
| 243 | Baden-Wurttemberg | Baden-Wurttemberg | 12 | Germany | Stuttgart | ★ | 48 | 48.775418, 9.181759 |
| 244 | Bavaria | Bavaria | 12 | Germany | Munich |  | 26 | 48.136607, 11.577085 |
| 246 | Brandenburg and Berlin | Brandenburg-and-Berlin | 12 | Germany | Berlin |  | 42 | 52.519171, 13.406091 |
| 249 | Hesse | Hesse | 12 | Germany | Wiesbaden |  | 32 | 50.06308, 8.243344 |
| 250 | Mecklenburg-Western Pomerania | Mecklenburg | 12 | Germany | Schwerin |  | 23 | 53.632435, 11.41289 |
| 251 | Lower Saxony and Bremen | Lower-Saxony-and-Bremen | 12 | Germany | Bremen |  | 39 | 53.079296, 8.801694 |
| 252 | North Rhine-Westphalia | North-Rhine-Westphalia | 12 | Germany | Dusseldorf |  | 35 | 51.227741, 6.773456 |
| 253 | Rhineland-Palatinate | Rhineland-Palatinate | 12 | Germany | Mainz |  | 28 | 49.992862, 8.247253 |
| 254 | Saarland | Saarland | 12 | Germany | Saarbrucken |  | 22 | 49.240157, 6.996933 |
| 255 | Saxony | Saxony | 12 | Germany | Dresden |  | 23 | 51.050409, 13.737262 |
| 256 | Saxony-Anhalt | Saxony-Anhalt | 12 | Germany | Magdeburg |  | 13 | 52.130807, 11.628878 |
| 257 | Schleswig-Holstein and Hamburg | Schleswig-Holstein-and-Hamburg | 12 | Germany | Hamburg |  | 14 | 53.551085, 9.993682 |
| 258 | Thuringia | Thuringia | 12 | Germany | Erfurt |  | 27 | 50.984768, 11.02988 |
| 259 | Abruzzo | Abruzzo | 10 | Italy | L'Aquila |  | 31 | 42.349848, 13.399509 |
| 260 | Aosta Valley | Aosta-Valley | 10 | Italy | Aosta |  | 52 | 45.734945, 7.313072 |
| 261 | Apulia | Apulia | 10 | Italy | Bari |  | 40 | 41.117143, 16.871872 |
| 262 | Basilicata | Basilicata | 10 | Italy | Potenza |  | 32 | 40.640407, 15.805604 |
| 263 | Calabria | Calabria | 10 | Italy | Catanzaro |  | 43 | 38.909792, 16.587652 |
| 264 | Campania | Campania | 10 | Italy | Naples |  | 21 | 40.851775, 14.268124 |
| 265 | Emilia-Romagna | Emilia-Romagna | 10 | Italy | Bologna |  | 35 | 44.494887, 11.342616 |
| 266 | Friuli-Venezia Giulia | Friuli-Venezia-Giulia | 10 | Italy | Trieste |  | 40 | 45.649535, 13.777972 |
| 267 | Lazio | Lazio | 10 | Italy | Rome |  | 26 | 41.901514, 12.460774 |
| 268 | Liguria | Liguria | 10 | Italy | Genoa |  | 45 | 44.40565, 8.946256 |
| 269 | Lombardy | Lombardy | 10 | Italy | Milan |  | 29 | 45.465454, 9.186516 |
| 270 | Marche | Marche | 10 | Italy | Ancona |  | 37 | 43.61583, 13.518915 |
| 271 | Molise | Molise | 10 | Italy | Campobasso |  | 34 | 41.560254, 14.662716 |
| 272 | Piedmont | Piedmont | 10 | Italy | Turin |  | 54 | 45.062902, 7.67849 |
| 273 | Sardinia | Sardinia | 10 | Italy | Cagliari |  | 47 | 39.223841, 9.121661 |
| 274 | Sicily | Sicily | 10 | Italy | Palermo | ★ | 61 | 38.115688, 13.361267 |
| 275 | Trentino-South Tyrol | Trentino-South-Tyrol | 10 | Italy | Trento |  | 76 | 46.069692, 11.121089 |
| 276 | Tuscany | Tuscany | 10 | Italy | Florence |  | 22 | 43.771033, 11.248001 |
| 277 | Umbria | Umbria | 10 | Italy | Perugia |  | 29 | 43.110717, 12.390828 |
| 278 | Veneto | Veneto | 10 | Italy | Venice |  | 35 | 45.440847, 12.315515 |
| 291 | Nord-Norge | Nord-Norge | 37 | Norway | Tromso |  | 20 | 69.649205, 18.955324 |
| 292 | Sorlandet | Sorlandet | 37 | Norway | Kristiansand |  | 12 | 58.150937, 7.996193 |
| 293 | Trondelag | Trondelag | 37 | Norway | Trondheim |  | 28 | 63.430515, 10.395053 |
| 294 | Vestlandet | Vestlandet | 37 | Norway | Bergen |  | 23 | 60.391263, 5.322054 |
| 295 | Ostlandet | Ostlandet | 37 | Norway | Oslo |  | 21 | 59.913869, 10.752245 |
| 306 | Pomerania | Pomerania | 35 | Poland | Gdansk | ★ | 368 | 54.352025, 18.646638 |
| 307 | Silesia | Silesia | 35 | Poland | Wroclaw |  | 89 | 51.107885, 17.038538 |
| 312 | Bratislava | Bratislava | 36 | Slovakia | Bratislava | ★ | 34 | 48.146239, 17.107262 |
| 315 | Western Slovakia | Western-Slovakia | 36 | Slovakia | Nitra |  | 28 | 48.309892, 18.085876 |
| 316 | Central Slovakia | Central-Slovakia | 36 | Slovakia | Zilina |  | 26 | 49.219548, 18.741274 |
| 319 | Eastern Slovakia | Eastern-Slovakia | 36 | Slovakia | Kosice |  | 21 | 48.722259, 21.256987 |
| 320 | Svealand | Svealand | 38 | Sweden | Stockholm |  | 47 | 59.32893, 18.06491 |
| 321 | Norrland and Sameland | Norrland-Sameland | 38 | Sweden | Umea |  | 13 | 63.825847, 20.263035 |
| 322 | Jamtland Harjedalen | Jamtland-Harjedalen | 38 | Sweden | Ostersund |  | 27 | 63.176683, 14.636068 |
| 323 | Bohus | Bohus | 38 | Sweden | Goteborg |  | 18 | 57.70887, 11.97456 |
| 324 | Scania | Scania | 38 | Sweden | Malmo |  | 23 | 55.604981, 13.003822 |
| 325 | Gotaland | Gotaland | 38 | Sweden | Orebro | ★ | 45 | 59.275263, 15.213411 |
| 326 | Smaland | Smaland | 38 | Sweden | Jonkoping |  | 19 | 57.782614, 14.161788 |
| 328 | New South Wales | New-South-Wales | 50 | Australia | Canberra |  | 31 | -35.308236, 149.124224 |
| 329 | Queensland | Queensland | 50 | Australia | Brisbane |  | 32 | -27.471011, 153.023449 |
| 330 | South Australia | South-Australia | 50 | Australia | Adelaide |  | 68 | -34.928621, 138.599959 |
| 331 | Tasmania | Tasmania | 50 | Australia | Hobart |  | 99 | -42.881903, 147.323815 |
| 332 | Victoria | Victoria | 50 | Australia | Melbourne |  | 23 | -37.811367, 144.971829 |
| 333 | Western Australia | Western-Australia | 50 | Australia | Perth |  | 46 | -31.932854, 115.86194 |
| 334 | Northern Territory | Northern-Territory | 50 | Australia | Darwin |  | 52 | -12.46282, 130.841769 |
| 336 | Deutschschweiz | Deutschschweiz | 30 | Switzerland | Bern |  | 24 | 46.947922, 7.444608 |
| 337 | Romandie | Romandie | 30 | Switzerland | Geneva |  | 22 | 46.198392, 6.142296 |
| 338 | Svizzera italiana | Svizzera-italiana | 30 | Switzerland | Bellinzona | ★ | 22 | 46.198494, 9.026918 |
| 339 | Graubunden | Graubunden | 30 | Switzerland | Chur |  | 32 | 46.857262, 9.526731 |
| 340 | Burgenland | Burgenland | 33 | Austria | Eisenstadt |  | 34 | 47.84637, 16.52796 |
| 341 | Carinthia | Carinthia | 33 | Austria | Klagenfurt |  | 32 | 46.62794, 14.30899 |
| 342 | Lower Austria | Lower-Austria | 33 | Austria | Vienna |  | 49 | 48.208174, 16.373819 |
| 343 | Upper Austria | Upper-Austria | 33 | Austria | Linz |  | 30 | 48.30694, 14.28583 |
| 344 | Salzburg | Salzburg | 33 | Austria | Salzburg | ★ | 22 | 47.80949, 13.05501 |
| 345 | Styria | Styria | 33 | Austria | Graz |  | 25 | 47.070714, 15.439504 |
| 346 | Tyrol | Tyrol | 33 | Austria | Innsbruck |  | 28 | 47.269212, 11.404102 |
| 347 | Vorarlberg | Vorarlberg | 33 | Austria | Bregenz |  | 28 | 47.50075, 9.74231 |
| 349 | Burgas | Burgas | 42 | Bulgaria | Burgas |  | 47 | 42.497678, 27.470025 |
| 352 | Vidin | Vidin | 42 | Bulgaria | Vidin |  | 51 | 43.993714, 22.876414 |
| 353 | Plovdiv | Plovdiv | 42 | Bulgaria | Plovdiv |  | 40 | 42.143841, 24.749562 |
| 355 | Sofia | Sofia | 42 | Bulgaria | Sofia |  | 118 | 42.696492, 23.326011 |
| 356 | Varna | Varna | 42 | Bulgaria | Varna |  | 229 | 43.216645, 27.911806 |
| 358 | Ruse | Ruse | 42 | Bulgaria | Ruse |  | 92 | 43.849579, 25.955229 |
| 361 | Anhui | Anhui | 14 | China | Hefei |  | 2 | 31.821448, 117.227271 |
| 362 | Fujian | Fujian | 14 | China | Fuzhou | ★ | 2 | 26.074508, 119.296494 |
| 363 | Gansu | Gansu | 14 | China | Lanzhou |  | 6 | 36.05639, 103.79222 |
| 364 | Guangdong | Guangdong | 14 | China | Guangzhou |  | 5 | 23.129163, 113.264435 |
| 368 | Heilongjiang | Heilongjiang | 14 | China | Harbin |  | 9 | 45.803775, 126.534967 |
| 370 | Hubei | Hubei | 14 | China | Wuhan |  | 11 | 30.593099, 114.305393 |
| 371 | Hunan | Hunan | 14 | China | Changsha |  | 5 | 28.228209, 112.938814 |
| 372 | Jiangsu | Jiangsu | 14 | China | Nanjing |  | 1 | 32.060255, 118.796877 |
| 373 | Jiangxi | Jiangxi | 14 | China | Nanchang |  | 11 | 28.68316, 115.858089 |
| 375 | Liaoning | Liaoning | 14 | China | Shenyang |  | 3 | 41.80572, 123.43147 |
| 377 | Shaanxi | Shaanxi | 14 | China | Xi'an |  | 3 | 34.341568, 108.940175 |
| 378 | Shandong | Shandong | 14 | China | Jinan |  | 3 | 36.650997, 117.120497 |
| 379 | Shanxi | Shanxi | 14 | China | Taiyuan |  | 7 | 37.87059, 112.548879 |
| 380 | Sichuan | Sichuan | 14 | China | Chengdu |  | 14 | 30.658601, 104.064856 |
| 381 | Yunnan | Yunnan | 14 | China | Kunming |  | 9 | 24.880182, 102.832861 |
| 382 | Zhejiang | Zhejiang | 14 | China | Hangzhou |  | 12 | 30.274089, 120.155069 |
| 384 | Guizhou | Guizhou | 14 | China | Guiyang |  | 4 | 26.647449, 106.630143 |
| 385 | Hainan | Hainan | 14 | China | Haikou |  | 4 | 20.030793, 110.328859 |
| 386 | Henan | Henan | 14 | China | Zhengzhou |  | 5 | 34.7466, 113.625368 |
| 387 | Jilin | Jilin | 14 | China | Changchun |  | 1 | 43.817084, 125.323542 |
| 389 | Qinghai | Qinghai | 14 | China | Xining |  | 0 | 36.617144, 101.778228 |
| 390 | Guangxi | Guangxi | 14 | China | Nanning |  | 5 | 22.817239, 108.366129 |
| 391 | Inner Mongolia | Inner-Mongolia | 14 | China | Hohhot |  | 4 | 40.842299, 111.749138 |
| 392 | Ningxia | Ningxia | 14 | China | Yinchuan |  | 8 | 38.487194, 106.230909 |
| 393 | Xinjiang | Xinjiang | 14 | China | Urumqi |  | 4 | 43.825645, 87.616823 |
| 394 | Tibet | Tibet | 14 | China | Lhasa |  | 2 | 29.645554, 91.140856 |
| 395 | Beijing | Beijing | 14 | China | Beijing |  | 33 | 39.90403, 116.407526 |
| 396 | Chongqing | Chongqing | 14 | China | Chongqing |  | 2 | 29.56301, 106.551557 |
| 397 | Shanghai | Shanghai | 14 | China | Shanghai |  | 3 | 31.230393, 121.473704 |
| 413 | Thrace | Thrace | 44 | Greece | Alexandroupoli |  | 17 | 40.845719, 25.873962 |
| 414 | Macedonia | Macedonia | 44 | Greece | Thessaloniki |  | 40 | 40.63935, 22.944606 |
| 415 | Thessaly | Thessaly | 44 | Greece | Larissa |  | 23 | 39.639022, 22.419125 |
| 416 | Epirus | Epirus | 44 | Greece | Ioannina |  | 28 | 39.6675, 20.85083 |
| 417 | Central Greece | Central-Greece | 44 | Greece | Lamia |  | 19 | 38.895973, 22.4349 |
| 418 | Attica | Attica | 44 | Greece | Athens |  | 17 | 37.983716, 23.72931 |
| 419 | Peloponnese | Peloponnese | 44 | Greece | Tripoli | ★ | 115 | 37.510136, 22.372644 |
| 420 | Aegean Islands | Aegean-Islands | 44 | Greece | Chios |  | 25 | 38.370981, 26.136346 |
| 421 | Ionian Islands | Ionian-Islands | 44 | Greece | Corfu |  | 32 | 39.611839, 19.830605 |
| 422 | Crete | Crete | 44 | Greece | Heraklion |  | 33 | 35.338735, 25.144213 |
| 423 | Mazuria | Mazuria | 35 | Poland | Olsztyn |  | 55 | 53.778422, 20.480119 |
| 424 | Mazovia | Mazovia | 35 | Poland | Warsaw |  | 99 | 52.229676, 21.012229 |
| 425 | Little Poland | Little-Poland | 35 | Poland | Krakow |  | 19 | 50.06465, 19.94498 |
| 426 | Great Poland | Great-Poland | 35 | Poland | Poznan |  | 44 | 52.406374, 16.925168 |
| 437 | Southern Bohemia | Southern-Bohemia | 34 | Czech Republic | Plzen | ★ | 60 | 49.738425, 13.37362 |
| 440 | Moravia | Moravia | 34 | Czech Republic | Brno |  | 28 | 49.19506, 16.606837 |
| 442 | Northern Bohemia | Northern-Bohemia | 34 | Czech Republic | Prague |  | 33 | 50.075538, 14.437801 |
| 443 | Northern India | Northern-India | 48 | India | New Delhi |  | 18 | 28.635308, 77.22496 |
| 445 | Uttar Pradesh | Uttar-Pradesh | 48 | India | Lucknow |  | 30 | 26.846511, 80.946683 |
| 446 | Rajasthan | Rajasthan | 48 | India | Jaipur |  | 18 | 26.912417, 75.787288 |
| 447 | Madhya Pradesh | Madhya-Pradesh | 48 | India | Bhopal |  | 10 | 23.259933, 77.412615 |
| 448 | Gujarat | Gujarat | 48 | India | Gandhinagar |  | 27 | 23.22482, 72.646377 |
| 449 | Maharashtra | Maharashtra | 48 | India | Mumbai |  | 72 | 19.075984, 72.877656 |
| 450 | Andhra Pradesh | Andhra-Pradesh | 48 | India | Hyderabad |  | 25 | 17.385044, 78.486671 |
| 451 | Karnataka | Karnataka | 48 | India | Bangalore | ★ | 140 | 12.971599, 77.594563 |
| 452 | Tamil Nadu | Tamil-Nadu | 48 | India | Chennai |  | 43 | 13.060422, 80.249583 |
| 453 | Kerala | Kerala | 48 | India | Thiruvananthapuram |  | 44 | 8.487495, 76.948623 |
| 454 | Orissa | Orissa | 48 | India | Bhubaneswar |  | 20 | 20.296059, 85.82454 |
| 455 | Chhattisgarh | Chhattisgarh | 48 | India | Raipur |  | 46 | 21.251384, 81.629641 |
| 456 | Jharkhand | Jharkhand | 48 | India | Ranchi |  | 40 | 23.3441, 85.309562 |
| 457 | West Bengal | West-Bengal | 48 | India | Calcutta |  | 10 | 22.572646, 88.363895 |
| 458 | Bihar | Bihar | 48 | India | Patna |  | 16 | 25.61046, 85.141667 |
| 459 | North Eastern India | North-Eastern-India | 48 | India | Shillong |  | 14 | 25.578773, 91.893254 |
| 460 | Sumatra | Sumatra | 49 | Indonesia | Medan |  | 576 | 3.585242, 98.675598 |
| 461 | Java | Java | 49 | Indonesia | Jakarta | ★ | 356 | -6.211544, 106.845172 |
| 462 | Kalimantan | Kalimantan | 49 | Indonesia | Samarinda |  | 494 | -0.502183, 117.153801 |
| 463 | Lesser Sunda Islands | Lesser-Sunda-Islands | 49 | Indonesia | Mataram |  | 484 | -8.583333, 116.116667 |
| 464 | Sulawesi | Sulawesi | 49 | Indonesia | Palu |  | 502 | -0.898583, 119.850601 |
| 465 | Maluku islands | Maluku-islands | 49 | Indonesia | Ambon |  | 450 | -3.65607, 128.166419 |
| 466 | Papua | Papua | 49 | Indonesia | Jayapura |  | 434 | -2.541279, 140.71373 |
| 467 | Jerusalem district | Jerusalem-district | 58 | Israel | Jerusalem | ★ | 40 | 31.768319, 35.21371 |
| 468 | Nazareth North District | Nazareth-North-District | 58 | Israel | Nazareth |  | 19 | 32.699635, 35.303546 |
| 469 | Haifa district | Haifa-district | 58 | Israel | Haifa |  | 29 | 32.830361, 34.974338 |
| 470 | Tel Aviv Center District | Tel-Aviv-Center-District | 58 | Israel | Tel Aviv |  | 18 | 32.066158, 34.777819 |
| 471 | Beersheba South District | Beersheba-South-District | 58 | Israel | Beersheba |  | 25 | 31.252973, 34.791462 |
| 472 | Kerman Province | Kerman-Province | 56 | Iran | Kerman |  | 19 | 30.28027, 57.06702 |
| 473 | Sistan and Baluchistan | Sistan-Baluchistan | 56 | Iran | Zahedan | ★ | 121 | 29.496389, 60.862778 |
| 474 | South Khorasan | South-Khorasan | 56 | Iran | Birjand |  | 73 | 32.880299, 59.210411 |
| 475 | Razavi Khorasan | Razavi-Khorasan | 56 | Iran | Mashhad |  | 35 | 36.3, 59.6 |
| 476 | Yazd | Yazd | 56 | Iran | Yazd |  | 23 | 31.89661, 54.36068 |
| 477 | Semnan | Semnan | 56 | Iran | Semnan |  | 59 | 35.572269, 53.396049 |
| 478 | Esfahan | Esfahan | 56 | Iran | Isfahan |  | 25 | 32.65139, 51.679192 |
| 479 | Fars | Fars | 56 | Iran | Shiraz |  | 28 | 29.616667, 52.533333 |
| 480 | Hormozgan | Hormozgan | 56 | Iran | Bandar Abbas |  | 60 | 27.183333, 56.266667 |
| 481 | Southwestern Iran | Southwestern-Iran | 56 | Iran | Ahvaz |  | 25 | 31.31671, 48.68438 |
| 482 | Northwestern Iran | Northwestern-Iran | 56 | Iran | Tehran |  | 47 | 35.696111, 51.423056 |
| 483 | Mazandaran and Golistan | Mazandaran-and-Golistan | 56 | Iran | Gorgan |  | 26 | 36.838611, 54.434722 |
| 484 | Hokkaido | Hokkaido | 45 | Japan | Sapporo |  | 27 | 43.062096, 141.354376 |
| 485 | Tohoku | Tohoku | 45 | Japan | Sendai |  | 28 | 38.268215, 140.869356 |
| 486 | Kanto | Kanto | 45 | Japan | Tokyo | ★ | 45 | 35.689488, 139.691706 |
| 487 | Chubu | Chubu | 45 | Japan | Nagoya |  | 13 | 35.181446, 136.906398 |
| 488 | Kinki | Kinki | 45 | Japan | Osaka |  | 19 | 34.693738, 135.502165 |
| 489 | Chugoku | Chugoku | 45 | Japan | Hiroshima |  | 14 | 34.385203, 132.455293 |
| 490 | Shikoku | Shikoku | 45 | Japan | Matsuyama |  | 34 | 33.839157, 132.765575 |
| 491 | Kyushu | Kyushu | 45 | Japan | Fukuoka |  | 31 | 33.590355, 130.401716 |
| 492 | Balochistan | Balochistan | 57 | Pakistan | Quetta |  | 46 | 30.209459, 67.018173 |
| 493 | North-West Frontier Province | North-West-Frontier | 57 | Pakistan | Peshawar | ★ | 51 | 34.004299, 71.54483 |
| 494 | Punjab | Punjab | 57 | Pakistan | Islamabad |  | 26 | 33.718151, 73.060547 |
| 495 | Sindh | Sindh | 57 | Pakistan | Karachi |  | 13 | 24.893379, 67.028061 |
| 497 | Eastern Cape | Eastern-Cape | 51 | South Africa | Bhisho |  | 48 | -32.849444, 27.438056 |
| 498 | Free State | Free-State | 51 | South Africa | Bloemfontein |  | 22 | -29.118349, 26.22492 |
| 499 | Gauteng | Gauteng | 51 | South Africa | Pretoria |  | 39 | -25.73134, 28.21837 |
| 500 | KwaZulu Natal | KwaZulu-Natal | 51 | South Africa | Pietermaritzburg |  | 7 | -29.617899, 30.382489 |
| 501 | Limpopo | Limpopo | 51 | South Africa | Polokwane |  | 66 | -23.891581, 29.4496 |
| 502 | Mpumalanga | Mpumalanga | 51 | South Africa | Nelspruit |  | 28 | -25.465833, 30.985278 |
| 503 | North West Province | North-West-Province | 51 | South Africa | Mmabatho |  | 22 | -25.837762, 25.594752 |
| 504 | Northern Cape | Northern-Cape | 51 | South Africa | Kimberley |  | 98 | -28.72929, 24.75404 |
| 505 | Western Cape | Western-Cape | 51 | South Africa | Cape Town |  | 86 | -33.924869, 18.424055 |
| 507 | Central Thailand | Central-Thailand | 59 | Thailand | Bangkok |  | 29 | 13.752222, 100.493889 |
| 508 | Northern Thailand | Northern-Thailand | 59 | Thailand | Chiang Mai | ★ | 24 | 18.796464, 98.660059 |
| 509 | Eastern Thailand | Eastern-Thailand | 59 | Thailand | Rayong |  | 72 | 12.830845, 101.433915 |
| 510 | Southern Thailand | Southern-Thailand | 59 | Thailand | Hat Yai |  | 17 | 7.001944, 100.457222 |
| 511 | North-Eastern Thailand | North-Eastern-Thailand | 59 | Thailand | Khon Kaen |  | 16 | 15.971736, 102.621621 |
| 512 | Aegean Coast of Turkey | Aegean-Coast-of-Turkey | 43 | Turkey | Izmir |  | 42 | 38.41885, 27.12872 |
| 513 | Black Sea Coast of Turkey | Black-Sea-Coast-of-Turkey | 43 | Turkey | Samsun |  | 81 | 41.292782, 36.33128 |
| 514 | Central Anatolia | Central-Anatolia | 43 | Turkey | Ankara |  | 90 | 39.92077, 32.85411 |
| 515 | Eastern Anatolia | Eastern-Anatolia | 43 | Turkey | Erzurum |  | 92 | 39.9, 41.27 |
| 516 | Marmara | Marmara | 43 | Turkey | Istanbul |  | 40 | 41.00527, 28.97696 |
| 517 | Mediterranean Coast of Turkey | Mediterranean-Coast-of-Turkey | 43 | Turkey | Antalya | ★ | 122 | 36.88414, 30.70563 |
| 518 | Southeastern Anatolia | Southeastern-Anatolia | 43 | Turkey | Diyarbakir |  | 70 | 37.91441, 40.230629 |
| 519 | Gyeonggi-do | Gyeonggi | 47 | South Korea | Seoul |  | 19 | 37.566535, 126.977969 |
| 520 | Gangwon-do | Gangwon | 47 | South Korea | Chuncheon |  | 20 | 37.881315, 127.729971 |
| 521 | Chungcheongbuk-do | Chungcheongbuk | 47 | South Korea | Cheongju |  | 76 | 36.642434, 127.489032 |
| 522 | Chungcheongnam-do | Chungcheongnam | 47 | South Korea | Daejeon |  | 17 | 36.350412, 127.384548 |
| 523 | Jeollabuk-do | Jeollabuk | 47 | South Korea | Jeonju |  | 15 | 35.824224, 127.147953 |
| 524 | Jeollanam-do | Jeollanam | 47 | South Korea | Muan |  | 12 | 34.990454, 126.481686 |
| 525 | Gyeongsangbuk-do | Gyeongsangbuk | 47 | South Korea | Daegu |  | 106 | 35.871435, 128.601445 |
| 526 | Gyeongsangnam-do | Gyeongsangnam | 47 | South Korea | Changwon |  | 14 | 35.270833, 128.663056 |
| 527 | Jeju | Jeju | 47 | South Korea | Jeju City | ★ | 111 | 33.499621, 126.531188 |
| 528 | Western Netherlands | Western-Netherlands | 31 | Netherlands | Amsterdam | ★ | 62 | 52.370216, 4.895168 |
| 529 | Southern Netherlands | Southern-Netherlands | 31 | Netherlands | Maastricht |  | 38 | 50.851368, 5.690973 |
| 530 | Eastern Netherlands | Eastern-Netherlands | 31 | Netherlands | Arnhem |  | 31 | 51.985103, 5.89873 |
| 531 | Northern Netherlands | Northern-Netherlands | 31 | Netherlands | Groningen |  | 22 | 53.219384, 6.566502 |
| 532 | Moscow and Central Russia | Moscow-and-Central-Russia | 41 | Russia | Moscow |  | 19 | 55.751242, 37.618422 |
| 533 | Central Black Earth | Central-Black-Earth | 41 | Russia | Voronezh |  | 107 | 51.656887, 39.202534 |
| 534 | Eastern Siberia | Eastern-Siberia | 41 | Russia | Krasnoyarsk |  | 41 | 56.010789, 92.852775 |
| 535 | Far Eastern Russia | Far-Eastern-Russia | 41 | Russia | Khabarovsk |  | 48 | 48.485637, 135.079588 |
| 536 | Northern Russia | Northern-Russia | 41 | Russia | Arhangelsk |  | 38 | 64.547052, 40.55209 |
| 537 | North Caucasus | North-Caucasus | 41 | Russia | Rostov on Don |  | 67 | 47.233333, 39.7 |
| 538 | Leningrad Oblast | Leningrad-Oblast | 41 | Russia | St Petersburg |  | 29 | 59.939524, 30.312021 |
| 540 | Urals | Urals | 41 | Russia | Yekaterinburg |  | 24 | 56.837524, 60.603272 |
| 541 | Volga Vyatka | Volga-Vyatka | 41 | Russia | Nizhny Novgorod |  | 73 | 56.327852, 44.000685 |
| 542 | Western Siberia | Western-Siberia | 41 | Russia | Novosibirsk |  | 113 | 55.030064, 82.919942 |
| 543 | Kaliningrad | Kaliningrad | 41 | Russia | Kaliningrad |  | 43 | 54.710289, 20.509016 |
| 544 | Volga | Volga | 41 | Russia | Volgograd |  | 35 | 48.709878, 44.52275 |
| 549 | Gotland | Gotland | 38 | Sweden | Visby |  | 14 | 57.6348, 18.29484 |
| 561 | Jammu and Kashmir | Jammu-Kashmir | 48 | India | Srinagar |  | 32 | 34.083658, 74.797368 |
| 562 | Svalbard & Jan Mayen | Svalbard-Jan-Mayen | 37 | Norway | Longyearbyen | ★ | 15 | 78.22, 15.65 |
| 571 | Slovenian Littoral | Slovenian-Littoral | 61 | Slovenia | Nova Gorica |  | 82 | 45.957233, 13.648287 |
| 581 | Inner Carniola | Inner-Carniola | 61 | Slovenia | Postojna |  | 85 | 45.775063, 14.213397 |
| 591 | Upper Carniola | Upper-Carniola | 61 | Slovenia | Kranj |  | 65 | 46.239207, 14.355654 |
| 601 | Styria and Carinthia | Styria-Carinthia | 61 | Slovenia | Maribor |  | 39 | 46.557399, 15.645982 |
| 611 | Lower Carniola | Lower-Carniola | 61 | Slovenia | Ljubljana |  | 80 | 46.051426, 14.505966 |
| 621 | Prekmurje | Prekmurje | 61 | Slovenia | Murska Sobota | ★ | 89 | 46.66124, 16.164904 |
| 622 | Slavonia | Slavonia | 63 | Croatia | Osijek |  | 51 | 45.554962, 18.695514 |
| 623 | Central Croatia | Central-Croatia | 63 | Croatia | Zagreb |  | 59 | 45.813029, 15.977895 |
| 624 | Northwest Croatia | Northwest-Croatia | 63 | Croatia | Varazdin |  | 124 | 46.305746, 16.336607 |
| 625 | Lika and Gorski Kotar | Lika-Gorski-Kotar | 63 | Croatia | Karlovac |  | 127 | 45.492897, 15.555268 |
| 626 | Istria and Kvarner | Istria-Kvarner | 63 | Croatia | Rijeka |  | 129 | 45.327063, 14.442176 |
| 627 | North Dalmatia | North-Dalmatia | 63 | Croatia | Zadar | ★ | 168 | 44.119392, 15.232911 |
| 628 | South Dalmatia | South-Dalmatia | 63 | Croatia | Split |  | 88 | 43.508132, 16.440194 |
| 629 | Norte Grande | Norte-Grande | 64 | Chile | Antofagasta |  | 146 | -23.65, -70.4 |
| 630 | Norte Chico | Norte-Chico | 64 | Chile | La Serena |  | 43 | -29.906301, -71.250204 |
| 631 | Zona Central | Zona-Central | 64 | Chile | Santiago |  | 112 | -33.46912, -70.641997 |
| 632 | Zona Sur | Zona-Sur | 64 | Chile | Temuco |  | 41 | -38.73383, -72.584496 |
| 633 | Zona Austral | Zona-Austral | 64 | Chile | Punta Arenas |  | 37 | -53.154478, -70.916476 |
| 634 | Vojvodina | Vojvodina | 65 | Serbia | Novi Sad |  | 120 | 45.262231, 19.851858 |
| 635 | Belgrade | Belgrade | 65 | Serbia | Belgrade |  | 219 | 44.820556, 20.462222 |
| 636 | Sumadija | Sumadija | 65 | Serbia | Kragujevac |  | 120 | 44.012711, 20.926741 |
| 637 | Eastern Serbia | Eastern-Serbia | 65 | Serbia | Zajecar |  | 75 | 43.916667, 22.3 |
| 638 | Western Serbia | Western-Serbia | 65 | Serbia | Uzice |  | 66 | 43.86417, 19.850031 |
| 639 | Raska | Raska | 65 | Serbia | Kraljevo |  | 71 | 43.723433, 20.6871 |
| 640 | Southern Serbia | Southern-Serbia | 65 | Serbia | Nis | ★ | 129 | 43.32472, 21.90333 |
| 641 | Sabah | Sabah | 66 | Malaysia | Kota Kinabalu |  | 28 | 5.976474, 116.115777 |
| 642 | Sarawak | Sarawak | 66 | Malaysia | Kuching | ★ | 34 | 1.530976, 110.34422 |
| 643 | Peninsular Malaysia | Peninsular-Malaysia | 66 | Malaysia | Kuala Lumpur |  | 12 | 3.139003, 101.686855 |
| 644 | Luzon | Luzon | 67 | Philippines | Manila |  | 69 | 14.599512, 120.98422 |
| 645 | Visayas | Visayas | 67 | Philippines | Cebu |  | 276 | 10.315699, 123.885437 |
| 646 | Mindanao | Mindanao | 67 | Philippines | Davao |  | 282 | 7.190708, 125.455341 |
| 647 | Palawan | Palawan | 67 | Philippines | Puerto Princesa | ★ | 412 | 9.967216, 118.78551 |
| 648 | Singapore City | Singapore-City | 68 | Singapore | Downtown Core | ★ | 74 | 1.286696, 103.85351 |
| 649 | West Srpska Republic | West-Srpska-Republic | 69 | Bosnia and Herzegovina | Banja Luka | ★ | 106 | 44.766667, 17.183333 |
| 650 | East Srpska Republic | East-Srpska-Republic | 69 | Bosnia and Herzegovina | Bijeljina |  | 84 | 44.75, 19.216667 |
| 651 | Brcko District | Brcko-District | 69 | Bosnia and Herzegovina | Brcko |  | 138 | 44.87278, 18.80833 |
| 652 | Federation of BiH | Federation-of-BiH | 69 | Bosnia and Herzegovina | Sarajevo |  | 57 | 43.85, 18.25 |
| 653 | Rio Grande do Sul | Rio-Grande-do-Sul | 9 | Brazil | Porto Alegre |  | 248 | -30.027704, -51.228735 |
| 654 | Pohja-Eesti | Pohja-Eesti | 70 | Estonia | Tallinn | ★ | 32 | 59.44269, 24.753197 |
| 655 | Kirde-Eesti | Kirde-Eesti | 70 | Estonia | Narva |  | 17 | 59.37412, 28.182233 |
| 656 | Kesk-Eesti | Kesk-Eesti | 70 | Estonia | Viljandi |  | 25 | 58.367654, 25.595335 |
| 657 | Laane-Eesti | Laane-Eesti | 70 | Estonia | Parnu |  | 23 | 58.387261, 24.497352 |
| 658 | Louna-Eesti | Louna-Eesti | 70 | Estonia | Tartu |  | 14 | 58.370847, 26.714722 |
| 659 | Vidzeme | Vidzeme | 71 | Latvia | Riga | ★ | 95 | 56.946203, 24.104287 |
| 660 | Latgale | Latgale | 71 | Latvia | Daugavpils |  | 35 | 55.871851, 26.517821 |
| 661 | Zemgale | Zemgale | 71 | Latvia | Jelgava |  | 32 | 56.652608, 23.722659 |
| 662 | Kurzeme | Kurzeme | 71 | Latvia | Liepaja |  | 32 | 56.511311, 21.011193 |
| 663 | Lithuania Minor | Lithuania-Minor | 72 | Lithuania | Klaipeda |  | 65 | 55.7, 21.133333 |
| 664 | Samogitia | Samogitia | 72 | Lithuania | Telsiai |  | 45 | 55.983579, 22.250813 |
| 665 | Lithuanian Highland | Lithuanian-Highland | 72 | Lithuania | Panevezys | ★ | 22 | 55.737436, 24.370333 |
| 666 | Dainava | Dainava | 72 | Lithuania | Vilnius |  | 13 | 54.689387, 25.280024 |
| 667 | Sudovia | Sudovia | 72 | Lithuania | Marijampole |  | 14 | 54.557812, 23.349812 |
| 668 | Chagang | Chagang | 73 | North Korea | Kanggye |  | 8 | 40.966667, 126.6 |
| 669 | Pyongan | Pyongan | 73 | North Korea | Pyongyang |  | 7 | 39.031859, 125.753765 |
| 670 | Hwangae | Hwangae | 73 | North Korea | Sariwŏn | ★ | 11 | 38.50722, 125.75583 |
| 671 | Kangwon | Kangwon | 73 | North Korea | Wonsan |  | 16 | 39.1475, 127.446111 |
| 672 | Hamgyong | Hamgyong | 73 | North Korea | Hamhung |  | 5 | 39.916667, 127.533333 |
| 673 | Ryanggang | Ryanggang | 73 | North Korea | Hyesan |  | 10 | 41.392761, 128.168198 |
| 674 | Charrua | Charrua | 74 | Uruguay | Montevideo | ★ | 133 | -34.883611, -56.181944 |
| 675 | Paranena | Paranena | 75 | Paraguay | Asuncion | ★ | 31 | -25.282197, -57.6351 |
| 676 | Central East Chaco | Central-East-Chaco | 75 | Paraguay | Pozo Colorado |  | 52 | -22.633333, -60.5 |
| 677 | Chuquisaca and Tarija | Chuquisaca-and-Tarija | 76 | Bolivia | Tarija |  | 133 | -21.531811, -64.731163 |
| 678 | Beni and Cochabamba | Beni-and-Cochabamba | 76 | Bolivia | Trinidad | ★ | 89 | -14.833333, -64.9 |
| 679 | Santa Cruz | Santa-Cruz | 76 | Bolivia | Santa Cruz |  | 64 | -17.866667, -63 |
| 680 | Bolivian Altiplano | Bolivian-Altiplano | 76 | Bolivia | La Paz |  | 54 | -16.49901, -68.146248 |
| 681 | Pando | Pando | 76 | Bolivia | Cobija |  | 45 | -11.02671, -68.76918 |
| 682 | Great Andes | Great-Andes | 77 | Peru | Cusco |  | 192 | -13.525, -71.972222 |
| 683 | Mid Andes | Mid-Andes | 77 | Peru | Ica |  | 48 | -14.06394, -75.743988 |
| 684 | Low Andes | Low-Andes | 77 | Peru | Arequipa |  | 36 | -16.398822, -71.536883 |
| 685 | Chimor | Chimor | 77 | Peru | Trujillo | ★ | 51 | -8.112, -79.0288 |
| 686 | Northern Low Amazon | Northern-Low-Amazon | 77 | Peru | Iquitos |  | 212 | -3.733333, -73.25 |
| 687 | Southern Low Amazon | Southern-Low-Amazon | 77 | Peru | Pucallpa |  | 110 | -8.383333, -74.55 |
| 688 | Lima | Lima | 77 | Peru | Lima DC |  | 40 | -12.04318, -77.02824 |
| 689 | Amazonica | Amazonica | 78 | Colombia | Leticia |  | 50 | -4.21528, -69.94056 |
| 690 | Andina | Andina | 78 | Colombia | Medellin |  | 61 | 6.235925, -75.575137 |
| 691 | Caribe e Insular | Caribe-e-Insular | 78 | Colombia | Cartagena |  | 56 | 10.387337, -75.519663 |
| 692 | Orinoquia | Orinoquia | 78 | Colombia | Villavicencio |  | 26 | 4.15, -73.633333 |
| 693 | Pacifica | Pacifica | 78 | Colombia | Cali | ★ | 25 | 3.420556, -76.522222 |
| 694 | Cundiboyacense | Cundiboyacense | 78 | Colombia | Bogota |  | 79 | 4.598056, -74.075833 |
| 695 | Povardarie | Povardarie | 79 | North Macedonia | Skopje |  | 96 | 41.997346, 21.427996 |
| 696 | Western Macedonia | Western-Macedonia | 79 | North Macedonia | Tetovo |  | 51 | 42, 20.966667 |
| 697 | Eastern Macedonia | Eastern-Macedonia | 79 | North Macedonia | Stip | ★ | 119 | 41.745651, 22.19697 |
| 698 | North Montenegrin Mountains | North-Montenegrin-Mountains | 80 | Montenegro | Pljevlja |  | 60 | 43.36, 19.36 |
| 699 | Central Montenegro | Central-Montenegro | 80 | Montenegro | Podgorica |  | 58 | 42.442575, 19.268646 |
| 700 | Montenegrin Coast | Montenegrin-Coast | 80 | Montenegro | Bar | ★ | 47 | 42.080952, 19.109871 |
| 701 | Northern Taiwan | Northern-Taiwan | 81 | Republic of China (Taiwan) | Taipei | ★ | 78 | 25.091075, 121.559835 |
| 702 | Central Taiwan | Central-Taiwan | 81 | Republic of China (Taiwan) | Taichung |  | 30 | 24.233208, 120.941737 |
| 703 | Eastern Taiwan | Eastern-Taiwan | 81 | Republic of China (Taiwan) | Taitung |  | 4 | 22.984612, 120.987632 |
| 704 | Southern Taiwan | Southern-Taiwan | 81 | Republic of China (Taiwan) | Kaohsiung |  | 54 | 23.010871, 120.666004 |
| 705 | Southern Cyprus | Southern-Cyprus | 82 | Cyprus | Nicosia | ★ | 73 | 35.166667, 33.366667 |
| 706 | Northern Cyprus | Northern-Cyprus | 82 | Cyprus | Famagusta |  | 6 | 35.285702, 33.841129 |
| 707 | Brestskaya | Brestskaya | 83 | Belarus | Brest |  | 37 | 52.133333, 23.666667 |
| 708 | Homelskaya | Homelskaya | 83 | Belarus | Gomel |  | 35 | 52.445278, 30.984167 |
| 709 | Hrodzienskaya | Hrodzienskaya | 83 | Belarus | Hrodna |  | 50 | 53.666667, 23.833333 |
| 710 | Minskaya | Minskaya | 83 | Belarus | Minsk |  | 47 | 53.9, 27.566667 |
| 711 | Mahilyowskaya | Mahilyowskaya | 83 | Belarus | Mahilyow | ★ | 42 | 53.9, 30.333333 |
| 712 | Vitsebskaya | Vitsebskaya | 83 | Belarus | Vitsebsk |  | 42 | 55.183333, 30.166667 |
| 713 | Auckland | Auckland | 84 | New Zealand | Auckland |  | 31 | -36.84846, 174.763332 |
| 714 | Wellington | Wellington | 84 | New Zealand | Wellington |  | 20 | -41.28646, 174.776236 |
| 715 | Canterbury | Canterbury | 84 | New Zealand | Canterbury |  | 9 | -43.754228, 171.163725 |
| 716 | Otago | Otago | 84 | New Zealand | Otago |  | 32 | -44.828004, 169.634525 |
| 717 | Al Riyadh | Al-Riyadh | 164 | Saudi Arabia | Riyadh |  | 40 | 24.711667, 46.724167 |
| 718 | Al Bahah | Al-Bahah | 164 | Saudi Arabia | Al Bahah |  | 14 | 20.012991, 41.460449 |
| 719 | Northern Borders | Northern-Borders | 164 | Saudi Arabia | Arar |  | 8 | 30.980829, 41.025009 |
| 720 | Al Jawf | Al-Jawf | 164 | Saudi Arabia | Sakaka | ★ | 54 | 29.970869, 40.205009 |
| 721 | Al Madinah | Al-Madinah | 164 | Saudi Arabia | Medina |  | 14 | 24.460899, 39.62019 |
| 722 | Al Qasim | Al-Qasim | 164 | Saudi Arabia | Buraidah |  | 17 | 26.333333, 43.966667 |
| 723 | Ha'il | Ha-il | 164 | Saudi Arabia | Ha'il |  | 6 | 27.516667, 41.683333 |
| 724 | Asir | Asir | 164 | Saudi Arabia | Abha |  | 8 | 18.220221, 42.50816 |
| 725 | Eastern Province | Eastern-Province | 164 | Saudi Arabia | Dammam |  | 73 | 26.392667, 49.977714 |
| 726 | Tabuk | Tabuk | 164 | Saudi Arabia | Tabuk |  | 35 | 28.390409, 36.5732 |
| 727 | Najran | Najran | 164 | Saudi Arabia | Najran |  | 8 | 17.49173, 44.13229 |
| 728 | Makkah | Makkah | 164 | Saudi Arabia | Mecca |  | 21 | 21.416667, 39.816667 |
| 729 | Jizan | Jizan | 164 | Saudi Arabia | Jizan |  | 36 | 16.89192, 42.549751 |
| 730 | Sinai | Sinai | 165 | Egypt | Dahab | ★ | 51 | 28.509136, 34.513634 |
| 731 | Lower Egypt | Lower-Egypt | 165 | Egypt | Cairo |  | 290 | 30.04442, 31.235712 |
| 732 | Western Desert | Western-Desert | 165 | Egypt | Siwa |  | 200 | 29.203171, 25.519545 |
| 733 | Middle Egypt | Middle-Egypt | 165 | Egypt | Amarna |  | 187 | 27.65, 30.966667 |
| 734 | Upper Egypt | Upper-Egypt | 165 | Egypt | Luxor |  | 157 | 25.687243, 32.639636 |
| 735 | Red Sea Coast | Red-Sea-Coast | 165 | Egypt | Hurghada |  | 200 | 27.257896, 33.811607 |
| 736 | Abu Dhabi | Abu-Dhabi | 166 | United Arab Emirates | Abu Dhabi |  | 52 | 24.466667, 54.366667 |
| 737 | Dubai | Dubai | 166 | United Arab Emirates | Dubai |  | 68 | 25.271139, 55.307485 |
| 738 | Sharjah | Sharjah | 166 | United Arab Emirates | Sharjah |  | 37 | 25.357522, 55.391865 |
| 739 | Ajman | Ajman | 166 | United Arab Emirates | Ajman |  | 26 | 25.403023, 55.491797 |
| 740 | Ras al-Khaimah | Ras-al-Khaimah | 166 | United Arab Emirates | Ras al-Khaimah |  | 37 | 25.800693, 55.976199 |
| 741 | Umm al Quwain | Umm-al-Quwain | 166 | United Arab Emirates | Falaj Al Moalla |  | 19 | 25.357609, 55.863496 |
| 742 | Fujairah | Fujairah | 166 | United Arab Emirates | Fujairah |  | 39 | 25.117388, 56.352568 |
| 743 | Kosovo | Kosovo | 65 | Serbia | Pristina |  | 73 | 42.67272, 21.16688 |
| 744 | Tirana | Tirana | 167 | Albania | Tirana |  | 69 | 41.326053, 19.816007 |
| 745 | Albanian Coast | Albanian-Coast | 167 | Albania | Durres |  | 96 | 41.32306, 19.44139 |
| 746 | Southeastern Albania | Southeastern-Albania | 167 | Albania | Berat |  | 124 | 40.70583, 19.95222 |
| 747 | Abkhazia | Abkhazia | 168 | Georgia | Sokhumi |  | 35 | 43.003333, 41.015278 |
| 748 | West Georgia | West-Georgia | 168 | Georgia | Kutaisi |  | 91 | 42.25, 42.7 |
| 749 | Lower Kartli | Lower-Kartli | 168 | Georgia | Akhaltsikhe |  | 124 | 41.638889, 42.986111 |
| 750 | Inner Kartli | Inner-Kartli | 168 | Georgia | Tbilisi |  | 256 | 41.716667, 44.783333 |
| 751 | Kakheti | Kakheti | 168 | Georgia | Telavi |  | 271 | 41.916667, 45.483333 |
| 752 | Northern Armenia | Northern-Armenia | 169 | Armenia | Gyumri |  | 20 | 40.789444, 43.8475 |
| 753 | Central Armenia | Central-Armenia | 169 | Armenia | Yerevan | ★ | 27 | 40.183333, 44.516667 |
| 754 | Syunik | Syunik | 169 | Armenia | Kapan |  | 22 | 39.201111, 46.415 |
| 755 | Gegharkunik | Gegharkunik | 169 | Armenia | Gavar |  | 14 | 40.358889, 45.126667 |
| 756 | North West States | North-West-States | 170 | Nigeria | Kano |  | 36 | 12, 8.516667 |
| 757 | North East States | North-East-States | 170 | Nigeria | Maiduguri |  | 23 | 11.833333, 13.15 |
| 758 | North Central States | North-Central-States | 170 | Nigeria | Abuja |  | 41 | 9.066667, 7.483333 |
| 759 | South West States | South-West-States | 170 | Nigeria | Lagos |  | 29 | 6.453056, 3.395833 |
| 760 | South South States | South-South-States | 170 | Nigeria | Port Harcourt | ★ | 56 | 4.75, 7 |
| 761 | South East States | South-East-States | 170 | Nigeria | Awka |  | 52 | 6.206944, 7.067778 |
| 762 | Western Cuba | Western-Cuba | 171 | Cuba | Havana |  | 25 | 23.133333, -82.383333 |
| 763 | Las Villas | Las-Villas | 171 | Cuba | Santa Clara | ★ | 4 | 22.406919, -79.964939 |
| 764 | Oriente | Oriente | 171 | Cuba | Santiago de Cuba |  | 23 | 20.019833, -75.813917 |

</details>

The city name of region 693 carries a trailing space in the live response (`"Cali "`).

---

## Regions by country

<details>
<summary>Regions of each original owner</summary>

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

</details>

---

## Open questions

| Topic | Earlier notes | Observed 2026-11-27 | Page follows |
|-------|---------------|---------------------|--------------|
| Capital flags | 72 capitals (2026-06-14) | 61 capitals | Live response |
| Population | Counts of 2026-06-14 | Not compared | Earlier notes |

**Source:** the old reference tables (`travelData`, and `map-data` captured 2026-06-14); comparison with `erepublik.info.countries` and a real `map-data` request, 2026-11-27.
