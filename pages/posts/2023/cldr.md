---
title: 如何格式化国家名称
description: 在为项目国际化时，根据不同语言选择切换对应语言文案都已经有了成熟的解决方案。但是如果遇到需要根据不同语言展示不同国家名称时，需要如何处理呢？本文介绍这一场景的几种处理方案。
date: 2023-06-24
tag: Web
---

# 如何格式化国家名称

在为项目国际化时，根据不同语言选择切换对应语言文案都已经有了成熟的解决方案。但是如果遇到需要根据不同语言展示不同国家名称时，需要如何处理呢？

本文介绍这一场景的几种处理方案。

## 使用 `CLDR`

[CLDR](https://cldr.unicode.org/)(Common Locale Data Repository) 是一个提供国际化数据的项目，其中包括了不同语言的名称、国家名称、日期、货币等等数据，目前被各大公司[广泛使用](https://cldr.unicode.org/#h.ezpykkomyltl)在各种项目中，比如 [Google AdSense 中的国家名称](https://developers.google.com/adsense/management/reference/rest/v2/Dimension) 便是使用 `CLDR` 国家代码存储，展示时根据不同语言展示对应国家名称。

![cldr-adsense](/images/minigame/cldr-adsense.png)

那么，我们要如何使用 `CLDR` 呢？

其实方法也很简单，可以直接[下载数据](https://cldr.unicode.org/index/downloads)使用，也可以通过 [cldr-json](https://github.com/unicode-org/cldr-json) 这一提供了 `JSON` 格式数据的 `NPM` 包安装需要的数据。

### 1. 安装 cldr-json

`cldr-json` 中提供了[很多不同数据的包](https://github.com/unicode-org/cldr-json/blob/main/PACKAGES.md)，我们需要的国家名称数据在 [cldr-localenames-modern](https://github.com/unicode-org/cldr-json/tree/main/cldr-json/cldr-localenames-modern) 中。

```bash
npm install cldr-localenames-modern
```

### 2. 引入对应语言数据

这里我们以英文的国家数据为例，其路径在 `cldr-localenames-modern/main/en/territories.json` 中。

```javascript
import CLDRJSON from 'cldr-localenames-modern/main/en/territories.json';

console.log(CLDRJSON.main.en.localeDisplayNames)
```

数据格式如下，其中键是两位的国家代码，值时当前语言的国家名称。根据这一数据我们可以根据国家代码去匹配国家名称。

```json
{
  "main": {
    "en": {
      "identity": {
        "version": {
          "_cldrVersion": "43"
        },
        "language": "en"
      },
      "localeDisplayNames": {
        "territories": {
          "001": "world",
          "002": "Africa",
          "003": "North America",
          "005": "South America",
          "009": "Oceania",
          "011": "Western Africa",
          "013": "Central America",
          "014": "Eastern Africa",
          "015": "Northern Africa",
          "017": "Middle Africa",
          "018": "Southern Africa",
          "019": "Americas",
          "021": "Northern America",
          "029": "Caribbean",
          "030": "Eastern Asia",
          "034": "Southern Asia",
          "035": "Southeast Asia",
          "039": "Southern Europe",
          "053": "Australasia",
          "054": "Melanesia",
          "057": "Micronesian Region",
          "061": "Polynesia",
          "142": "Asia",
          "143": "Central Asia",
          "145": "Western Asia",
          "150": "Europe",
          "151": "Eastern Europe",
          "154": "Northern Europe",
          "155": "Western Europe",
          "202": "Sub-Saharan Africa",
          "419": "Latin America",
          "AC": "Ascension Island",
          "AD": "Andorra",
          "AE": "United Arab Emirates",
          "AF": "Afghanistan",
          "AG": "Antigua & Barbuda",
          "AI": "Anguilla",
          "AL": "Albania",
          "AM": "Armenia",
          "AO": "Angola",
          "AQ": "Antarctica",
          "AR": "Argentina",
          "AS": "American Samoa",
          "AT": "Austria",
          "AU": "Australia",
          "AW": "Aruba",
          "AX": "Åland Islands",
          "AZ": "Azerbaijan",
          "BA": "Bosnia & Herzegovina",
          "BA-alt-short": "Bosnia",
          "BB": "Barbados",
          "BD": "Bangladesh",
          "BE": "Belgium",
          "BF": "Burkina Faso",
          "BG": "Bulgaria",
          "BH": "Bahrain",
          "BI": "Burundi",
          "BJ": "Benin",
          "BL": "St. Barthélemy",
          "BM": "Bermuda",
          "BN": "Brunei",
          "BO": "Bolivia",
          "BQ": "Caribbean Netherlands",
          "BR": "Brazil",
          "BS": "Bahamas",
          "BT": "Bhutan",
          "BV": "Bouvet Island",
          "BW": "Botswana",
          "BY": "Belarus",
          "BZ": "Belize",
          "CA": "Canada",
          "CC": "Cocos (Keeling) Islands",
          "CD": "Congo - Kinshasa",
          "CD-alt-variant": "Congo (DRC)",
          "CF": "Central African Republic",
          "CG": "Congo - Brazzaville",
          "CG-alt-variant": "Congo (Republic)",
          "CH": "Switzerland",
          "CI": "Côte d’Ivoire",
          "CI-alt-variant": "Ivory Coast",
          "CK": "Cook Islands",
          "CL": "Chile",
          "CM": "Cameroon",
          "CN": "China",
          "CO": "Colombia",
          "CP": "Clipperton Island",
          "CQ": "Sark",
          "CR": "Costa Rica",
          "CU": "Cuba",
          "CV": "Cape Verde",
          "CV-alt-variant": "Cabo Verde",
          "CW": "Curaçao",
          "CX": "Christmas Island",
          "CY": "Cyprus",
          "CZ": "Czechia",
          "CZ-alt-variant": "Czech Republic",
          "DE": "Germany",
          "DG": "Diego Garcia",
          "DJ": "Djibouti",
          "DK": "Denmark",
          "DM": "Dominica",
          "DO": "Dominican Republic",
          "DZ": "Algeria",
          "EA": "Ceuta & Melilla",
          "EC": "Ecuador",
          "EE": "Estonia",
          "EG": "Egypt",
          "EH": "Western Sahara",
          "ER": "Eritrea",
          "ES": "Spain",
          "ET": "Ethiopia",
          "EU": "European Union",
          "EZ": "Eurozone",
          "FI": "Finland",
          "FJ": "Fiji",
          "FK": "Falkland Islands",
          "FK-alt-variant": "Falkland Islands (Islas Malvinas)",
          "FM": "Micronesia",
          "FO": "Faroe Islands",
          "FR": "France",
          "GA": "Gabon",
          "GB": "United Kingdom",
          "GB-alt-short": "UK",
          "GD": "Grenada",
          "GE": "Georgia",
          "GF": "French Guiana",
          "GG": "Guernsey",
          "GH": "Ghana",
          "GI": "Gibraltar",
          "GL": "Greenland",
          "GM": "Gambia",
          "GN": "Guinea",
          "GP": "Guadeloupe",
          "GQ": "Equatorial Guinea",
          "GR": "Greece",
          "GS": "South Georgia & South Sandwich Islands",
          "GT": "Guatemala",
          "GU": "Guam",
          "GW": "Guinea-Bissau",
          "GY": "Guyana",
          "HK": "Hong Kong SAR China",
          "HK-alt-short": "Hong Kong",
          "HM": "Heard & McDonald Islands",
          "HN": "Honduras",
          "HR": "Croatia",
          "HT": "Haiti",
          "HU": "Hungary",
          "IC": "Canary Islands",
          "ID": "Indonesia",
          "IE": "Ireland",
          "IL": "Israel",
          "IM": "Isle of Man",
          "IN": "India",
          "IO": "British Indian Ocean Territory",
          "IQ": "Iraq",
          "IR": "Iran",
          "IS": "Iceland",
          "IT": "Italy",
          "JE": "Jersey",
          "JM": "Jamaica",
          "JO": "Jordan",
          "JP": "Japan",
          "KE": "Kenya",
          "KG": "Kyrgyzstan",
          "KH": "Cambodia",
          "KI": "Kiribati",
          "KM": "Comoros",
          "KN": "St. Kitts & Nevis",
          "KP": "North Korea",
          "KR": "South Korea",
          "KW": "Kuwait",
          "KY": "Cayman Islands",
          "KZ": "Kazakhstan",
          "LA": "Laos",
          "LB": "Lebanon",
          "LC": "St. Lucia",
          "LI": "Liechtenstein",
          "LK": "Sri Lanka",
          "LR": "Liberia",
          "LS": "Lesotho",
          "LT": "Lithuania",
          "LU": "Luxembourg",
          "LV": "Latvia",
          "LY": "Libya",
          "MA": "Morocco",
          "MC": "Monaco",
          "MD": "Moldova",
          "ME": "Montenegro",
          "MF": "St. Martin",
          "MG": "Madagascar",
          "MH": "Marshall Islands",
          "MK": "North Macedonia",
          "ML": "Mali",
          "MM": "Myanmar (Burma)",
          "MM-alt-short": "Myanmar",
          "MN": "Mongolia",
          "MO": "Macao SAR China",
          "MO-alt-short": "Macao",
          "MP": "Northern Mariana Islands",
          "MQ": "Martinique",
          "MR": "Mauritania",
          "MS": "Montserrat",
          "MT": "Malta",
          "MU": "Mauritius",
          "MV": "Maldives",
          "MW": "Malawi",
          "MX": "Mexico",
          "MY": "Malaysia",
          "MZ": "Mozambique",
          "NA": "Namibia",
          "NC": "New Caledonia",
          "NE": "Niger",
          "NF": "Norfolk Island",
          "NG": "Nigeria",
          "NI": "Nicaragua",
          "NL": "Netherlands",
          "NO": "Norway",
          "NP": "Nepal",
          "NR": "Nauru",
          "NU": "Niue",
          "NZ": "New Zealand",
          "NZ-alt-variant": "Aotearoa New Zealand",
          "OM": "Oman",
          "PA": "Panama",
          "PE": "Peru",
          "PF": "French Polynesia",
          "PG": "Papua New Guinea",
          "PH": "Philippines",
          "PK": "Pakistan",
          "PL": "Poland",
          "PM": "St. Pierre & Miquelon",
          "PN": "Pitcairn Islands",
          "PR": "Puerto Rico",
          "PS": "Palestinian Territories",
          "PS-alt-short": "Palestine",
          "PT": "Portugal",
          "PW": "Palau",
          "PY": "Paraguay",
          "QA": "Qatar",
          "QO": "Outlying Oceania",
          "RE": "Réunion",
          "RO": "Romania",
          "RS": "Serbia",
          "RU": "Russia",
          "RW": "Rwanda",
          "SA": "Saudi Arabia",
          "SB": "Solomon Islands",
          "SC": "Seychelles",
          "SD": "Sudan",
          "SE": "Sweden",
          "SG": "Singapore",
          "SH": "St. Helena",
          "SI": "Slovenia",
          "SJ": "Svalbard & Jan Mayen",
          "SK": "Slovakia",
          "SL": "Sierra Leone",
          "SM": "San Marino",
          "SN": "Senegal",
          "SO": "Somalia",
          "SR": "Suriname",
          "SS": "South Sudan",
          "ST": "São Tomé & Príncipe",
          "SV": "El Salvador",
          "SX": "Sint Maarten",
          "SY": "Syria",
          "SZ": "Eswatini",
          "SZ-alt-variant": "Swaziland",
          "TA": "Tristan da Cunha",
          "TC": "Turks & Caicos Islands",
          "TD": "Chad",
          "TF": "French Southern Territories",
          "TG": "Togo",
          "TH": "Thailand",
          "TJ": "Tajikistan",
          "TK": "Tokelau",
          "TL": "Timor-Leste",
          "TL-alt-variant": "East Timor",
          "TM": "Turkmenistan",
          "TN": "Tunisia",
          "TO": "Tonga",
          "TR": "Türkiye",
          "TR-alt-variant": "Turkey",
          "TT": "Trinidad & Tobago",
          "TV": "Tuvalu",
          "TW": "Taiwan",
          "TZ": "Tanzania",
          "UA": "Ukraine",
          "UG": "Uganda",
          "UM": "U.S. Outlying Islands",
          "UN": "United Nations",
          "UN-alt-short": "UN",
          "US": "United States",
          "US-alt-short": "US",
          "UY": "Uruguay",
          "UZ": "Uzbekistan",
          "VA": "Vatican City",
          "VC": "St. Vincent & Grenadines",
          "VE": "Venezuela",
          "VG": "British Virgin Islands",
          "VI": "U.S. Virgin Islands",
          "VN": "Vietnam",
          "VU": "Vanuatu",
          "WF": "Wallis & Futuna",
          "WS": "Samoa",
          "XA": "Pseudo-Accents",
          "XB": "Pseudo-Bidi",
          "XK": "Kosovo",
          "YE": "Yemen",
          "YT": "Mayotte",
          "ZA": "South Africa",
          "ZM": "Zambia",
          "ZW": "Zimbabwe",
          "ZZ": "Unknown Region"
        }
      }
    }
  }
}
```

## 使用 `Intl.DisplayNames`

我们也可以使用浏览器提供的 [Intl.DisplayNames](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DisplayNames) 这一原生 `API` 来格式化国家名称、货币单位等数据，不需要额外安装 `CLDR` 数据。不过需要注意浏览器兼容性问题，`Format.JS` 提供了一个 [polyfill](https://formatjs.io/docs/polyfills/intl-displaynames/)。

```javascript
// 获取区域的英文显示名称
let regionNames = new Intl.DisplayNames(["en"], { type: "region" });
regionNames.of("419"); // "Latin America"
regionNames.of("BZ"); // "Belize"
regionNames.of("US"); // "United States"
regionNames.of("BA"); // "Bosnia & Herzegovina"
regionNames.of("MM"); // "Myanmar (Burma)"

// 获取繁体中文区域的显示名称
regionNames = new Intl.DisplayNames(["zh-Hant"], { type: "region" });
regionNames.of("419"); // "拉丁美洲"
regionNames.of("BZ"); // "貝里斯"
regionNames.of("US"); // "美國"
regionNames.of("BA"); // "波士尼亞與赫塞哥維納"
regionNames.of("MM"); // "緬甸"
```

`Intl.DisplayNames` 也可以格式化货币单位、日期名称等其他数据

```javascript
// Get display names of currency code in English
let currencyNames = new Intl.DisplayNames(["en"], { type: "currency" });
// Get currency names
currencyNames.of("USD"); // "US Dollar"
currencyNames.of("EUR"); // "Euro"
currencyNames.of("TWD"); // "New Taiwan Dollar"
currencyNames.of("CNY"); // "Chinese Yuan"

// Get display names of currency code in Traditional Chinese
currencyNames = new Intl.DisplayNames(["zh-Hant"], { type: "currency" });
currencyNames.of("USD"); // "美元"
currencyNames.of("EUR"); // "歐元"
currencyNames.of("TWD"); // "新台幣"
currencyNames.of("CNY"); // "人民幣"

const dn = new Intl.DisplayNames("pt", { type: "dateTimeField" });
console.log(dn.of("era")); // 'era'
console.log(dn.of("year")); // 'ano'
console.log(dn.of("month")); // 'mês'
console.log(dn.of("quarter")); // 'trimestre'
console.log(dn.of("weekOfYear")); // 'semana'
console.log(dn.of("weekday")); // 'dia da semana'
console.log(dn.of("dayPeriod")); // 'AM/PM'
console.log(dn.of("day")); // 'dia'
console.log(dn.of("hour")); // 'hora'
console.log(dn.of("minute")); // 'minuto'
console.log(dn.of("second")); // 'segundo'
```

## 在 `React` 中使用 `Format.JS` 以及 `react-intl`

如果是 `React` 项目，并且国际化方案使用的是 `Format.JS`  的 `react-intl` 也可以方便的使用 [FormattedDisplayName](https://formatjs.io/docs/react-intl/components#formatteddisplayname) 组件和 [formatDisplayName](https://formatjs.io/docs/react-intl/api#formatdisplayname) API 来格式化国家名称。其底层使用的是浏览器的 `Intl.DisplayNames` API，并且提供了一个 [polyfill](https://formatjs.io/docs/polyfills/intl-displaynames/)来解决浏览器兼容性问题。

```tsx
<FormattedDisplayName type="region" value="US" />
// 输出：United States
```

```javascript
// ISO-3166 two letters region code to localized display name
intl.formatDisplayName('US', {type: 'region'})
// 输出：United States
```

## 参考链接

- [Unicode CLDR Project](https://cldr.unicode.org/)
- [cldr-json](https://github.com/unicode-org/cldr-json)
- [CLDR JSON Packages](https://github.com/unicode-org/cldr-json/blob/main/PACKAGES.md)
- [cldr-localenames-modern](https://github.com/unicode-org/cldr-json/tree/main/cldr-json/cldr-localenames-modern)
- [Intl.DisplayNames](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/DisplayNames)
- [react-intl FormattedDisplayName 组件](https://formatjs.io/docs/react-intl/components#formatteddisplayname)
- [react-intl formatDisplayName API](https://formatjs.io/docs/react-intl/api#formatdisplayname)
- [@formatjs/intl-displaynames](https://formatjs.io/docs/polyfills/intl-displaynames/)
