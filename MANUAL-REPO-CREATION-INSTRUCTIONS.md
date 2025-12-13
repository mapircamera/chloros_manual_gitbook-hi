# मैनुअल रिपॉजिटरी निर्माण निर्देश

यदि आप मैन्युअल रूप से रिपॉजिटरी बनाना पसंद करते हैं या स्वचालित स्क्रिप्ट काम नहीं करती है, तो इन निर्देशों का पालन करें।

## पूर्वावश्यकताएँ

1. उचित अनुमतियों के साथ GitHub खाता
2. आपके कंप्यूटर पर Git इंस्टॉल हो गया है
3. GitHub CLI (वैकल्पिक लेकिन अनुशंसित): https://cli.github.com/

## विधि 1: GitHub CLI का उपयोग करना (अनुशंसित)

### GitHub CLI इंस्टॉल करें
```powershell
# Windows (using winget)
winget install --id GitHub.cli

# Or download from https://cli.github.com/
```

### प्रमाणित करें
```powershell
gh auth login
```

### प्रत्येक रिपॉजिटरी बनाएं
प्रत्येक भाषा के लिए, चलाएँ:

```powershell
# Example for Spanish
gh repo create chloros_manual_gitbook-es --public --description "Chloros Professional Multispectral Image Processing Software - User Manual (Español)" --clone

cd chloros_manual_gitbook-es

# Create README.md
@"
# Chloros User Manual - Español

This is the Spanish translation of the Chloros Professional Multispectral Image Processing Software user manual.

## About Chloros
Chloros is professional multispectral image processing software that transforms imagery into actionable insights.

## Links
- [Main Website](https://www.mapir.camera)
- [Chloros Download](https://drive.google.com/file/d/1HjwrUY4M7HGxDbMybO7iPe_6JoHnUGr4/view?usp=drive_link)
- [English Documentation](https://mapir.gitbook.io/chloros)
"@ | Out-File README.md -Encoding utf8

# Create .gitignore
@"
.DS_Store
Thumbs.db
.vscode/
_book/
node_modules/
"@ | Out-File .gitignore -Encoding utf8

# Commit and push
git add .
git commit -m "Initial commit - Spanish translation repository"
git push -u origin main

cd ..
```

सभी 27 भाषाओं के लिए दोहराएं (सभी भाषा कोड के लिए language-repos-list.md देखें)।

## विधि 2: GitHub वेब इंटरफ़ेस का उपयोग करना

### प्रत्येक भाषा के लिए:

1. **GitHub पर जाएं**
- https://github.com/new पर नेविगेट करें
   
2. **रिपॉजिटरी सेटिंग्स**
- रिपॉजिटरी का नाम: `chloros_manual_gitbook-[language-code]`
- विवरण: `Chloros Professional Multispectral Image Processing Software - User Manual ([Native Language Name])`
- सार्वजनिक भंडार
- ✓ एक README फ़ाइल जोड़ें
- .gitignore जोड़ें: कोई नहीं (हम इसे बाद में जोड़ेंगे)
- लाइसेंस: कोई नहीं (या आपका पसंदीदा लाइसेंस)

3. **"रिपॉजिटरी बनाएं" पर क्लिक करें**

4. **स्थानीय रूप से क्लोन करें**
```powershell
   git clone https://github.com/YOUR-USERNAME/chloros_manual_gitbook-[language-code]
   cd chloros_manual_gitbook-[language-code]
   ```

5. **फ़ाइलें जोड़ें**
- उचित सामग्री के साथ README.md संपादित करें
- .gitignore फ़ाइल बनाएं
- प्रतिबद्ध और धक्का

6. सभी 27 भाषाओं के लिए **दोहराएँ**

## बनाने के लिए सभी रिपॉजिटरी नाम

संदर्भ के लिए इस सूची को कॉपी और पेस्ट करें:

```
chloros_manual_gitbook-es       (Spanish - Español)
chloros_manual_gitbook-pt       (Portuguese - Português)
chloros_manual_gitbook-fr       (French - Français)
chloros_manual_gitbook-de       (German - Deutsch)
chloros_manual_gitbook-it       (Italian - Italiano)
chloros_manual_gitbook-ja       (Japanese - 日本語)
chloros_manual_gitbook-ko       (Korean - 한국어)
chloros_manual_gitbook-zh-CN    (Chinese Simplified - 简体中文)
chloros_manual_gitbook-zh-TW    (Chinese Traditional - 繁體中文)
chloros_manual_gitbook-ru       (Russian - Русский)
chloros_manual_gitbook-nl       (Dutch - Nederlands)
chloros_manual_gitbook-ar       (Arabic - العربية)
chloros_manual_gitbook-pl       (Polish - Polski)
chloros_manual_gitbook-tr       (Turkish - Türkçe)
chloros_manual_gitbook-hi       (Hindi - हिंदी)
chloros_manual_gitbook-id       (Indonesian - Bahasa Indonesia)
chloros_manual_gitbook-vi       (Vietnamese - Tiếng Việt)
chloros_manual_gitbook-th       (Thai - ไทย)
chloros_manual_gitbook-sv       (Swedish - Svenska)
chloros_manual_gitbook-da       (Danish - Dansk)
chloros_manual_gitbook-no       (Norwegian - Norsk)
chloros_manual_gitbook-fi       (Finnish - Suomi)
chloros_manual_gitbook-el       (Greek - Ελληνικά)
chloros_manual_gitbook-cs       (Czech - Čeština)
chloros_manual_gitbook-hu       (Hungarian - Magyar)
chloros_manual_gitbook-ro       (Romanian - Română)
chloros_manual_gitbook-uk       (Ukrainian - Українська)
```

## त्वरित कमांड सूची (GitHub CLI)

यदि आपके पास GitHub CLI स्थापित है, तो आप इन आदेशों को क्रम से चला सकते हैं:

```powershell
gh repo create chloros_manual_gitbook-es --public --description "Chloros User Manual - Español" --clone
gh repo create chloros_manual_gitbook-pt --public --description "Chloros User Manual - Português" --clone
gh repo create chloros_manual_gitbook-fr --public --description "Chloros User Manual - Français" --clone
gh repo create chloros_manual_gitbook-de --public --description "Chloros User Manual - Deutsch" --clone
gh repo create chloros_manual_gitbook-it --public --description "Chloros User Manual - Italiano" --clone
gh repo create chloros_manual_gitbook-ja --public --description "Chloros User Manual - 日本語" --clone
gh repo create chloros_manual_gitbook-ko --public --description "Chloros User Manual - 한국어" --clone
gh repo create chloros_manual_gitbook-zh-CN --public --description "Chloros User Manual - 简体中文" --clone
gh repo create chloros_manual_gitbook-zh-TW --public --description "Chloros User Manual - 繁體中文" --clone
gh repo create chloros_manual_gitbook-ru --public --description "Chloros User Manual - Русский" --clone
gh repo create chloros_manual_gitbook-nl --public --description "Chloros User Manual - Nederlands" --clone
gh repo create chloros_manual_gitbook-ar --public --description "Chloros User Manual - العربية" --clone
gh repo create chloros_manual_gitbook-pl --public --description "Chloros User Manual - Polski" --clone
gh repo create chloros_manual_gitbook-tr --public --description "Chloros User Manual - Türkçe" --clone
gh repo create chloros_manual_gitbook-hi --public --description "Chloros User Manual - हिंदी" --clone
gh repo create chloros_manual_gitbook-id --public --description "Chloros User Manual - Bahasa Indonesia" --clone
gh repo create chloros_manual_gitbook-vi --public --description "Chloros User Manual - Tiếng Việt" --clone
gh repo create chloros_manual_gitbook-th --public --description "Chloros User Manual - ไทย" --clone
gh repo create chloros_manual_gitbook-sv --public --description "Chloros User Manual - Svenska" --clone
gh repo create chloros_manual_gitbook-da --public --description "Chloros User Manual - Dansk" --clone
gh repo create chloros_manual_gitbook-no --public --description "Chloros User Manual - Norsk" --clone
gh repo create chloros_manual_gitbook-fi --public --description "Chloros User Manual - Suomi" --clone
gh repo create chloros_manual_gitbook-el --public --description "Chloros User Manual - Ελληνικά" --clone
gh repo create chloros_manual_gitbook-cs --public --description "Chloros User Manual - Čeština" --clone
gh repo create chloros_manual_gitbook-hu --public --description "Chloros User Manual - Magyar" --clone
gh repo create chloros_manual_gitbook-ro --public --description "Chloros User Manual - Română" --clone
gh repo create chloros_manual_gitbook-uk --public --description "Chloros User Manual - Українська" --clone
```

## रेपो निर्माण के बाद अगले चरण

1. **GitBook में**: अपने Chloros मैनुअल को 27 बार डुप्लिकेट करें (प्रत्येक भाषा के लिए एक)
2. **प्रत्येक स्थान को नाम दें**: "Chloros मैनुअल - [मूल भाषा का नाम]"
3. **GitHub से कनेक्ट करें**: प्रत्येक GitBook स्पेस सेटिंग्स में, संबंधित GitHub रेपो से कनेक्ट करें
4. **सिंक सक्षम करें**: GitBook और GitHub के बीच द्वि-दिशात्मक सिंक सेट करें
5. **सत्यापित करें**: जांचें कि सभी रेपो ठीक से सिंक हो गए हैं
6. **अनुवाद**: प्रत्येक रेपो में सामग्री का अनुवाद करने के लिए अनुवाद टूल का उपयोग करें

## समस्या निवारण

### समस्या: "भंडार पहले से मौजूद है"
- रिपॉजिटरी का नाम पहले ही लिया जा चुका है
- एक अलग नाम चुनें या पहले मौजूदा रेपो को हटा दें

### समस्या: GitHub CLI प्रमाणित नहीं
चलाएँ: `gh auth login`

### समस्या: अनुमति अस्वीकृत
- सुनिश्चित करें कि आपके पास अपने खाते/संगठन में रेपो बनाने की अनुमति है
- अपनी GitHub टोकन अनुमतियाँ जांचें

### मुद्दा: दर सीमित करना
- GitHub में API दर सीमाएँ हैं
- रिपॉजिटरी बनाने के बीच कुछ मिनट प्रतीक्षा करें
- या उन्हें वेब इंटरफ़ेस के माध्यम से मैन्युअल रूप से बनाएं

