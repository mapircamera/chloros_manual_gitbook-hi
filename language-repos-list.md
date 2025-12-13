# Chloros मैनुअल भाषा रिपॉजिटरी

यह दस्तावेज़ Chloros मैन्युअल अनुवाद प्रोजेक्ट के लिए सभी 28 भाषा रिपॉजिटरी को सूचीबद्ध करता है।

## रिपॉजिटरी नामकरण कन्वेंशन

`chloros_manual_gitbook-[language-code]`

## रिपॉजिटरी सूची

| # | भाषा | मूल नाम | रेपो नाम | भाषा कोड |
|---|---|---|---|----|---|
| 1 | अंग्रेजी | अंग्रेजी | `chloros_manual_gitbook` | एन (वर्तमान) |
| 2 | स्पैनिश | एस्पनॉल | `chloros_manual_gitbook-es` | तों |
| 3 | पुर्तगाली | पुर्तगाली | `chloros_manual_gitbook-pt` | पीटी |
| 4 | फ़्रेंच | फ़्रांसीसी | `chloros_manual_gitbook-fr` | fr |
| 5 | जर्मन | जर्मन | `chloros_manual_gitbook-de` | डे |
| 6 | इटालियन | इटालियनो | `chloros_manual_gitbook-it` | यह |
| 7 | जापानी | 日本語 | `chloros_manual_gitbook-ja` | जा |
| 8 | कोरियाई | 한국어 | `chloros_manual_gitbook-ko` | को |
| 9 | चीनी (सरलीकृत) | 简体中文 | `chloros_manual_gitbook-zh-CN` | zh-CN |
| 10 | चीनी (पारंपरिक) | 繁體中文 | `chloros_manual_gitbook-zh-TW` | zh-TW |
| 11 | रूसी | Русский | `chloros_manual_gitbook-ru` | आरयू |
| 12 | डच | नीदरलैंड | `chloros_manual_gitbook-nl` | एनएल |
| 13 | अरबी | العربية | `chloros_manual_gitbook-ar` | एआर |
| 14 | पोलिश | पोल्स्की | `chloros_manual_gitbook-pl` | कृपया |
| 15 | तुर्की | तुर्कसे | `chloros_manual_gitbook-tr` | tr |
| 16 | हिंदी | हिंदी | `chloros_manual_gitbook-hi` | नमस्ते |
| 17 | इंडोनेशियाई | बहासा इंडोनेशिया | `chloros_manual_gitbook-id` | आईडी |
| 18 | वियतनामी | तियांग वियत | `chloros_manual_gitbook-vi` | vi |
| 19 | थाई | ไทย | `chloros_manual_gitbook-th` | वें |
| 20 | स्वीडिश | स्वेन्स्का | `chloros_manual_gitbook-sv` | एसवी |
| 21 | डेनिश | डांस्क | `chloros_manual_gitbook-da` | दा |
| 22 | नॉर्वेजियन | नॉर्स्क | `chloros_manual_gitbook-no` | नहीं |
| 23 | फ़िनिश | सुओमी | `chloros_manual_gitbook-fi` | फाई |
| 24 | ग्रीक | Ελληνικά | `chloros_manual_gitbook-el` | एल |
| 25 | चेक | सेस्टिना | `chloros_manual_gitbook-cs` | सीएस |
| 26 | हंगेरियन | मग्यार | `chloros_manual_gitbook-hu` | हू |
| 27 | रोमानियाई | रोमानी | `chloros_manual_gitbook-ro` | आरओ |
| 28 | यूक्रेनी | Українська | `chloros_manual_gitbook-uk` | यूके |

## GitBook सिंक्रोनाइज़ेशन योजना

### चरण 1: GitHub रिपॉजिटरी बनाएं
पॉवरशेल स्क्रिप्ट चलाएँ: `create-language-repos.ps1`

### चरण 2: डुप्लिकेट GitBook स्पेस
GitBook में:
1. अपने वर्तमान Chloros मैनुअल स्पेस पर जाएं
2. स्थान को 27 बार डुप्लिकेट करें
3. प्रत्येक स्थान को भाषा के साथ नाम दें (उदाहरण के लिए, "Chloros मैनुअल - Español", "Chloros मैनुअल - 日本語", आदि)

### चरण 3: GitBook को GitHub से कनेक्ट करें
प्रत्येक डुप्लिकेट किए गए GitBook स्थान के लिए:
1. स्पेस सेटिंग्स → इंटीग्रेशन पर जाएं
2. "GitHub" चुनें
3. संबंधित भाषा भंडार से कनेक्ट करें
4. द्वि-दिशात्मक सिंक सेट करें
5. शाखा चुनें (आमतौर पर `main`)

### चरण 4: अनुवाद
एक बार सभी GitBook स्पेस सिंक हो जाएं:
1. GitHub रेपो में प्रत्येक स्थान की अपनी सामग्री होगी
2. मार्कडाउन फ़ाइलों का अनुवाद करने के लिए अनुवाद स्क्रिप्ट चलाएँ
3. परिवर्तन स्वचालित रूप से GitBook पर वापस सिंक हो जाएंगे

## रिपोजिटरी यूआरएल (निर्माण के बाद)

यह मानते हुए कि GitHub उपयोगकर्ता नाम `MAPIR` या आपका संगठन है:

- https://github.com/MAPIR/chloros_manual_gitbook (अंग्रेजी - वर्तमान)
- https://github.com/MAPIR/chloros_manual_gitbook-es
- https://github.com/MAPIR/chloros_manual_gitbook-pt
- https://github.com/MAPIR/chloros_manual_gitbook-fr
- https://github.com/MAPIR/chloros_manual_gitbook-de
- https://github.com/MAPIR/chloros_manual_gitbook-it
- https://github.com/MAPIR/chloros_manual_gitbook-ja
- https://github.com/MAPIR/chloros_manual_gitbook-ko
- https://github.com/MAPIR/chloros_manual_gitbook-zh-CN
- https://github.com/MAPIR/chloros_manual_gitbook-zh-TW
- https://github.com/MAPIR/chloros_manual_gitbook-ru
- https://github.com/MAPIR/chloros_manual_gitbook-nl
- https://github.com/MAPIR/chloros_manual_gitbook-ar
- https://github.com/MAPIR/chloros_manual_gitbook-pl
- https://github.com/MAPIR/chloros_manual_gitbook-tr
- https://github.com/MAPIR/chloros_manual_gitbook-hi
- https://github.com/MAPIR/chloros_manual_gitbook-id
- https://github.com/MAPIR/chloros_manual_gitbook-vi
- https://github.com/MAPIR/chloros_manual_gitbook-th
- https://github.com/MAPIR/chloros_manual_gitbook-sv
- https://github.com/MAPIR/chloros_manual_gitbook-da
- https://github.com/MAPIR/chloros_manual_gitbook-no
- https://github.com/MAPIR/chloros_manual_gitbook-fi
- https://github.com/MAPIR/chloros_manual_gitbook-el
- https://github.com/MAPIR/chloros_manual_gitbook-cs
- https://github.com/MAPIR/chloros_manual_gitbook-hu
- https://github.com/MAPIR/chloros_manual_gitbook-ro
- https://github.com/MAPIR/chloros_manual_gitbook-uk

## अनुवाद उपकरण

रिपॉजिटरी निर्माण के बाद, अतिरिक्त स्क्रिप्ट प्रदान की जाएंगी:
- एआई/अनुवाद एपीआई का उपयोग करके स्वचालित मार्कडाउन अनुवाद
- सभी भाषा रिपो का बैच प्रसंस्करण
- गुणवत्ता नियंत्रण और समीक्षा वर्कफ़्लो
- अंग्रेजी (मास्टर) और अनुवाद के बीच समन्वय बनाए रखना

