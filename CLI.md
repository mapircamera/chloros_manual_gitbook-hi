# CLI : कमांड लाइन

<figure><img src='.gitbook/assets/cli.JPG' alt=''><figcaption></figcaption></figure>

**Chloros CLI** Chloros इमेज प्रोसेसिंग इंजन तक शक्तिशाली कमांड-लाइन एक्सेस प्रदान करता है, जो आपके इमेजिंग वर्कफ़्लो के लिए स्वचालन, स्क्रिप्टिंग और हेडलेस ऑपरेशन को सक्षम करता है।

### प्रमुख विशेषताऐं

* 🚀 **स्वचालन** - एकाधिक डेटासेट की स्क्रिप्ट बैच प्रोसेसिंग
* 🔗 **एकीकरण** - मौजूदा वर्कफ़्लोज़ और पाइपलाइनों में एम्बेड करें
* 💻 **हेडलेस ऑपरेशन** - बिना जीयूआई के चलाएं
* 🌍 **बहु-भाषा** - 38 भाषाओं के लिए समर्थन
* ⚡ **समानांतर प्रसंस्करण** - आपके सीपीयू को गतिशील रूप से स्केल करता है (16 समानांतर श्रमिकों तक)

### आवश्यकताएं

| आवश्यकता | विवरण |
| ------------------- | ---------------------------------------------------------------------------------- |
| **ऑपरेटिंग सिस्टम** | Windows 10/11 (64-बिट) |
| **लाइसेंस** | Chloros+ ([भुगतान योजना आवश्यक](https://cloud.mapir.camera/pricing)) |
| **स्मृति** | न्यूनतम 8जीबी रैम (16जीबी अनुशंसित) |
| **इंटरनेट** | लाइसेंस सक्रियण के लिए आवश्यक |
| **डिस्क स्थान** | प्रोजेक्ट आकार के अनुसार भिन्न होता है |

{% संकेत शैली = "चेतावनी" %}
**लाइसेंस आवश्यकता**: CLI के लिए सशुल्क Chloros+ सदस्यता की आवश्यकता होती है। मानक (निःशुल्क) योजनाओं में CLI पहुंच नहीं है। अपग्रेड करने के लिए [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) पर जाएं।
{% अंतिम संकेत %}

## त्वरित शुरुआत

### स्थापना

CLI स्वचालित रूप से Chloros इंस्टॉलर के साथ शामिल है:

1. डाउनलोड करें और चलाएं **Chloros इंस्टॉलर.exe**

2. इंस्टॉलेशन विज़ार्ड को पूरा करें
3. CLI स्थापित: `C:\Program Files\Chloros\resources\cli\chloros-cli.exe`

{% संकेत शैली = "सफलता" %}
इंस्टॉलर स्वचालित रूप से आपके सिस्टम PATH में `chloros-cli` जोड़ता है। स्थापना के बाद अपने टर्मिनल को पुनः आरंभ करें।
{% अंतिम संकेत %}

### पहली बार सेटअप

CLI का उपयोग करने से पहले, अपना Chloros+ लाइसेंस सक्रिय करें:

```bash
# Login with your Chloros+ account
chloros-cli login user@example.com 'your_password'

# Check license status
chloros-cli status

# Process your first project
chloros-cli process "C:\Images\Dataset001"
```

### मूल उपयोग

डिफ़ॉल्ट सेटिंग्स वाले फ़ोल्डर को संसाधित करें:

```powershell
chloros-cli process "C:\Images\Dataset001"
```

***

## कमांड संदर्भ

### सामान्य सिंटैक्स

```
chloros-cli [global-options] <command> [command-options]
```

***

## आदेश

### `process` - प्रक्रिया छवियाँ

अंशांकन के साथ एक फ़ोल्डर में छवियों को संसाधित करें।

**वाक्यविन्यास:**

```bash
chloros-cli process <input-folder> [options]
```

**उदाहरण:**

```powershell
chloros-cli process "C:\Datasets\Survey_001" --vignette --reflectance
```

#### प्रक्रिया आदेश विकल्प

| विकल्प | प्रकार | डिफ़ॉल्ट | विवरण |
| ---------------------- | ------- | -------------- | -------------------------------------------------------------------------------------------------- |
| `<input-folder>` | पथ | _आवश्यक_ | RAW/JPG मल्टीस्पेक्ट्रल छवियों वाला फ़ोल्डर |
| `-o, --output` | पथ | इनपुट के समान | संसाधित छवियों के लिए आउटपुट फ़ोल्डर |
| `-n, --project-name` | स्ट्रिंग | स्वतः उत्पन्न | कस्टम प्रोजेक्ट का नाम |
| `--vignette` | झंडा | सक्षम | विग्नेट सुधार सक्षम करें |
| `--no-vignette` | झंडा | - | विग्नेट सुधार अक्षम करें |
| `--reflectance` | झंडा | सक्षम | परावर्तन अंशांकन सक्षम करें |
| `--no-reflectance` | झंडा | - | परावर्तन अंशांकन अक्षम करें |
| `--ppk` | झंडा | विकलांग | .daq लाइट सेंसर डेटा से PPK सुधार लागू करें |
| `--format` | विकल्प | TIFF (16-बिट) | आउटपुट स्वरूप: `TIFF (16-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)` |
| `--min-target-size` | पूर्णांक | ऑटो | अंशांकन पैनल का पता लगाने के लिए पिक्सेल में न्यूनतम लक्ष्य आकार |
| `--target-clustering` | पूर्णांक | ऑटो | लक्ष्य क्लस्टरिंग सीमा (0-100) |
| `--exposure-pin-1` | स्ट्रिंग | कोई नहीं | कैमरा मॉडल के लिए एक्सपोज़र लॉक करें (पिन 1) |
| `--exposure-pin-2` | स्ट्रिंग | कोई नहीं | कैमरा मॉडल के लिए एक्सपोज़र लॉक करें (पिन 2) |
| `--recal-interval` | पूर्णांक | ऑटो | सेकंड में पुनर्अंशांकन अंतराल |
| `--timezone-offset` | पूर्णांक | 0 | समय क्षेत्र घंटों में ऑफसेट |

***

### `login` - खाता प्रमाणित करें

CLI प्रोसेसिंग सक्षम करने के लिए अपने Chloros+ क्रेडेंशियल के साथ लॉगिन करें।

**वाक्यविन्यास:**

```bash
chloros-cli login <email> <password>
```

**उदाहरण:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% संकेत शैली = "चेतावनी" %}
**विशेष वर्ण**: `$`, `!`, या रिक्त स्थान जैसे वर्ण वाले पासवर्ड के चारों ओर एकल उद्धरण चिह्नों का उपयोग करें।
{% अंतिम संकेत %}

**आउटपुट:**<figure><img src='.gitbook/assets/clilogin_w.JPG' alt=''><figcaption></figcaption></figure>***

### `logout` - स्पष्ट क्रेडेंशियल

संग्रहीत क्रेडेंशियल साफ़ करें और अपने खाते से लॉगआउट करें।

**वाक्यविन्यास:**

```bash
chloros-cli logout
```

**उदाहरण:**

```powershell
chloros-cli logout
```

**आउटपुट:**

```
✓ Logout successful
ℹ Credentials cleared from cache
```

{% संकेत शैली = "जानकारी" %}
**SDK उपयोगकर्ता**: Python SDK Python स्क्रिप्ट के भीतर क्रेडेंशियल साफ़ करने के लिए एक प्रोग्रामेटिक `logout()` विधि भी प्रदान करता है। विवरण के लिए [Python SDK दस्तावेज़](api-python-sdk.md#logout) देखें।
{% अंतिम संकेत %}

***

### `status` - लाइसेंस स्थिति जांचें

वर्तमान लाइसेंस और प्रमाणीकरण स्थिति प्रदर्शित करें।

**वाक्यविन्यास:**

```bash
chloros-cli status
```

**उदाहरण:**

```powershell
chloros-cli status
```

**आउटपुट:**

```
╔══════════════════════════════════════╗
║     LICENSE & ACCOUNT INFORMATION    ║
╚══════════════════════════════════════╝

📧 Email: user@example.com
📋 Plan: Chloros+ Professional
🔓 API/CLI Access: Enabled
✓ Status: Active
```

***

### `export-status` - निर्यात प्रगति की जाँच करें

प्रसंस्करण के दौरान या उसके बाद थ्रेड 4 निर्यात प्रगति की निगरानी करें।

**वाक्यविन्यास:**

```bash
chloros-cli export-status
```

**उदाहरण:**

```powershell
chloros-cli export-status
```

**केस का उपयोग करें:** निर्यात प्रगति की जांच करने के लिए प्रोसेसिंग चालू होने पर इस कमांड को कॉल करें।***

### `language` - इंटरफ़ेस भाषा प्रबंधित करें

CLI इंटरफ़ेस भाषा देखें या बदलें।

**वाक्यविन्यास:**

```bash
# Show current language
chloros-cli language

# List all available languages
chloros-cli language --list

# Set a specific language
chloros-cli language <language-code>
```

**उदाहरण:**

```powershell
# View current language
chloros-cli language

# List all 38 supported languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Change to Japanese
chloros-cli language ja
```

#### समर्थित भाषाएँ (कुल 38)

| कोड | भाषा | मूल नाम |
| ------- | ---------------------- | ---------------- |
| `en` | अंग्रेजी | अंग्रेजी |
| `es` | स्पैनिश | एस्पनॉल |
| `pt` | पुर्तगाली | पुर्तगाली |
| `fr` | फ़्रेंच | फ़्रांसीसी |
| `de` | जर्मन | जर्मन |
| `it` | इटालियन | इटालियनो |
| `ja` | जापानी | 日本語 |
| `ko` | कोरियाई | 한국어 |
| `zh` | चीनी (सरलीकृत) | 简体中文 |
| `zh-TW` | चीनी (पारंपरिक) | 繁體中文 |
| `ru` | रूसी | Русский |
| `nl` | डच | नीदरलैंड |
| `ar` | अरबी | العربية |
| `pl` | पोलिश | पोल्स्की |
| `tr` | तुर्की | तुर्कसे |
| `hi` | हिंदी | हिंदी |
| `id` | इंडोनेशियाई | बहासा इंडोनेशिया |
| `vi` | वियतनामी | तियांग वियत |
| `th` | थाई | ไทย |
| `sv` | स्वीडिश | स्वेन्स्का |
| `da` | डेनिश | डांस्क |
| `no` | नॉर्वेजियन | नॉर्स्क |
| `fi` | फ़िनिश | सुओमी |
| `el` | ग्रीक | Ελληνικά |
| `cs` | चेक | सेस्टिना |
| `hu` | हंगेरियन | मग्यार |
| `ro` | रोमानियाई | रोमानी |
| `uk` | यूक्रेनी | Українська |
| `pt-BR` | ब्राज़ीलियाई पुर्तगाली | पोर्टुगुएस ब्रासीलिरो |
| `zh-HK` | कैंटोनीज़ | 粵語 |
| `ms` | मलय | बहासा मेलायु |
| `sk` | स्लोवाक | स्लोवेनसीना |
| `bg` | बल्गेरियाई | बेल्ज़िक |
| `hr` | क्रोएशियाई | ह्रवत्स्की |
| `lt` | लिथुआनियाई | लितुविų |
| `lv` | लातवियाई | लात्वीशू |
| `et` | एस्टोनियाई | ईस्टी |
| `sl` | स्लोवेनियाई | स्लोवेन्स्कीना |

{% संकेत शैली = "सफलता" %}
**स्वचालित दृढ़ता**: आपकी भाषा प्राथमिकता `~/.chloros/cli_language.json` में सहेजी जाती है और सभी सत्रों में बनी रहती है।
{% अंतिम संकेत %}

***

### `set-project-folder` - डिफ़ॉल्ट प्रोजेक्ट फ़ोल्डर सेट करें

डिफ़ॉल्ट प्रोजेक्ट फ़ोल्डर स्थान बदलें (जीयूआई के साथ साझा)।

**वाक्यविन्यास:**

```bash
chloros-cli set-project-folder <folder-path>
```

**उदाहरण:**

```powershell
chloros-cli set-project-folder "C:\Projects\2025"
```

***

### `get-project-folder` - प्रोजेक्ट फ़ोल्डर दिखाएँ

वर्तमान डिफ़ॉल्ट प्रोजेक्ट फ़ोल्डर स्थान प्रदर्शित करें।

**वाक्यविन्यास:**

```bash
chloros-cli get-project-folder
```

**उदाहरण:**

```powershell
chloros-cli get-project-folder
```

**आउटपुट:**

```
ℹ Current project folder: C:\Projects\2025
```

***

### `reset-project-folder` - डिफ़ॉल्ट पर रीसेट करें

प्रोजेक्ट फ़ोल्डर को डिफ़ॉल्ट स्थान पर रीसेट करें।

**वाक्यविन्यास:**

```bash
chloros-cli reset-project-folder
```

***

## वैश्विक विकल्प

ये विकल्प सभी आदेशों पर लागू होते हैं:

| विकल्प | प्रकार | डिफ़ॉल्ट | विवरण |
| --------------- | ------- | ----------------- | ------------------------------------------------ |
| `--backend-exe` | पथ | स्वतः पता लगाया गया | निष्पादन योग्य बैकएंड का पथ |
| `--port` | पूर्णांक | 5000 | बैकएंड API पोर्ट नंबर |
| `--restart` | झंडा | - | फोर्स रीस्टार्ट बैकएंड (मौजूदा प्रक्रियाओं को समाप्त करता है) |
| `--version` | झंडा | - | संस्करण जानकारी दिखाएँ और बाहर निकलें |
| `--help` | झंडा | - | सहायता जानकारी दिखाएँ और बाहर निकलें |

**वैश्विक विकल्पों के साथ उदाहरण:**

```powershell
chloros-cli --port 5001 process "C:\Datasets\Survey_001"
```

***

## प्रोसेसिंग सेटिंग्स गाइड

### समानांतर प्रसंस्करण

Chloros+ CLI **स्वचालित रूप से आपके कंप्यूटर की क्षमताओं से मेल खाने के लिए समानांतर प्रसंस्करण को मापता है:**यह काम किस प्रकार करता है:**

* आपके सीपीयू कोर और रैम का पता लगाता है
* श्रमिकों को आवंटित करता है: **2× सीपीयू कोर** (हाइपरथ्रेडिंग का उपयोग करता है)
* **अधिकतम: 16 समानांतर कार्यकर्ता** (स्थिरता के लिए)**सिस्टम स्तर:**

| सिस्टम प्रकार | सीपीयू | रैम | श्रमिक | प्रदर्शन |
| ----------------- | ---------- | -------- | -------- | --------------- |
| **हाई-एंड** | 16+ कोर | 32+ जीबी | 16 तक | अधिकतम गति |
| **मध्य-सीमा** | 8-15 कोर | 16-31 जीबी | 8-16 | उत्कृष्ट गति |
| **लो-एंड** | 4-7 कोर | 8-15 जीबी | 4-8 | अच्छी गति |

{% संकेत शैली = "सफलता" %}
**स्वचालित अनुकूलन**: CLI स्वचालित रूप से आपके सिस्टम विनिर्देशों का पता लगाता है और इष्टतम समानांतर प्रसंस्करण को कॉन्फ़िगर करता है। किसी मैन्युअल कॉन्फ़िगरेशन की आवश्यकता नहीं!
{% अंतिम संकेत %}

### डिबेयर तरीके

CLI डिफ़ॉल्ट और अनुशंसित डिबायर एल्गोरिदम के रूप में **उच्च गुणवत्ता (तेज़)** का उपयोग करता है:

| विधि | गुणवत्ता | गति | विवरण |
| -------------------------------- | ------- | ----- | ------------------------------------------------ |
| **उच्च गुणवत्ता (तेज़)** ⭐ | ⭐⭐⭐⭐ | ⚡⚡⚡ | एज-अवेयर एल्गोरिथम (डिफ़ॉल्ट, अनुशंसित) |

### विग्नेट सुधार

**यह क्या करता है:** छवि किनारों पर प्रकाश के गिरने को ठीक करता है (कैमरा इमेजरी में गहरे कोने आम हैं)।

* **डिफ़ॉल्ट रूप से सक्षम** - अधिकांश उपयोगकर्ताओं को इसे सक्षम रखना चाहिए
* अक्षम करने के लिए `--no-vignette` का उपयोग करें

{% संकेत शैली = "सफलता" %}
**सिफारिश**: पूरे फ्रेम में एक समान चमक सुनिश्चित करने के लिए हमेशा विग्नेट सुधार सक्षम करें।
{% अंतिम संकेत %}

### परावर्तन अंशांकन

अंशांकन पैनलों का उपयोग करके कच्चे सेंसर मानों को मानकीकृत परावर्तन प्रतिशत में परिवर्तित करता है।

* **डिफ़ॉल्ट रूप से सक्षम** - वनस्पति विश्लेषण के लिए आवश्यक
* छवियों में अंशांकन लक्ष्य पैनल की आवश्यकता है
* अक्षम करने के लिए `--no-reflectance` का उपयोग करें

{% संकेत शैली = "जानकारी" %}
**आवश्यकताएँ**: सुनिश्चित करें कि सटीक परावर्तन रूपांतरण के लिए अंशांकन पैनल आपकी छवियों में ठीक से प्रदर्शित और दृश्यमान हों।
{% अंतिम संकेत %}

### पीपीके सुधार

**यह क्या करता है:** बेहतर जीपीएस सटीकता के लिए DAQ-A-SD लॉग डेटा का उपयोग करके पोस्ट-प्रोसेस्ड किनेमेटिक सुधार लागू करता है।

* **डिफ़ॉल्ट रूप से अक्षम**
* सक्षम करने के लिए `--ppk` का उपयोग करें
* MAPIR DAQ-A-SD लाइट सेंसर से प्रोजेक्ट फ़ोल्डर में .daq फ़ाइलों की आवश्यकता है।

### आउटपुट प्रारूप

<table><thead><tr><th width="197">प्रारूप</th><th width="130.20001220703125">बिट गहराई</th><th width="116.5999755859375">फ़ाइल आकार</th><th>सर्वश्रेष्ठ</th></tr></thead><tbody><tr><td><strong>TIFF (16-बिट)</strong> ⭐</td><td>16-बिट पूर्णांक</td><td>बड़ा</td><td>जीआईएस विश्लेषण, फोटोग्रामेट्री (अनुशंसित)</td></tr><tr><td><strong>TIFF (32-बिट, प्रतिशत)</strong></td><td>32-बिट फ्लोट</td><td>बहुत बड़ा</td><td>वैज्ञानिक विश्लेषण, अनुसंधान</td></tr><tr><td><strong>PNG (8-बिट)</strong></td><td>8-बिट पूर्णांक</td><td>मध्यम</td><td>दृश्य निरीक्षण, वेब साझाकरण</td></tr><tr><td><strong>JPG (8-बिट)</strong></td><td>8-बिट पूर्णांक</td><td>छोटा</td><td>त्वरित पूर्वावलोकन, संपीड़ित आउटपुट</td></tr></tbody></table>

***

## स्वचालन एवं स्क्रिप्टिंग

### पावरशेल बैच प्रोसेसिंग

एकाधिक डेटासेट फ़ोल्डरों को स्वचालित रूप से संसाधित करें:

```powershell
# process_all_datasets.ps1

$datasets = Get-ChildItem "C:\Datasets\2025" -Directory

foreach ($dataset in $datasets) {
    Write-Host "Processing $($dataset.Name)..." -ForegroundColor Cyan
    
    chloros-cli process $dataset.FullName `
        --vignette `
        --reflectance
    
    if ($LASTEXITCODE -eq 0) {
        Write-Host "✓ $($dataset.Name) complete" -ForegroundColor Green
    } else {
        Write-Host "✗ $($dataset.Name) failed" -ForegroundColor Red
    }
}

Write-Host "All datasets processed!" -ForegroundColor Green
```

### Windows बैच स्क्रिप्ट

बैच प्रोसेसिंग के लिए सरल लूप:

```batch
@echo off
echo Starting batch processing...

for /d %%i in (C:\Datasets\2025\*) do (
    echo.
    echo ========================================
    echo Processing: %%i
    echo ========================================
    chloros-cli process "%%i"
    
    if %ERRORLEVEL% EQU 0 (
        echo SUCCESS: %%i processed
    ) else (
        echo ERROR: %%i failed
    )
)

echo.
echo All datasets processed!
pause
```

### Python ऑटोमेशन स्क्रिप्ट

त्रुटि प्रबंधन के साथ उन्नत स्वचालन:

```python
import subprocess
import os
import sys
from pathlib import Path
from datetime import datetime

def process_dataset(input_folder):
    """Process a folder using Chloros CLI"""
    cmd = ['chloros-cli', 'process', str(input_folder)]
    
    # Execute command
    result = subprocess.run(
        cmd, 
        capture_output=True, 
        text=True,
        encoding='utf-8'
    )
    
    return result.returncode == 0, result.stdout, result.stderr

def main():
    """Process all datasets in a directory"""
    datasets_dir = Path('C:/Datasets/2025')
    log_file = Path('processing_log.txt')
    
    successful = []
    failed = []
    
    # Start processing
    print(f"Starting batch processing: {datetime.now()}")
    print(f"Scanning: {datasets_dir}")
    print("=" * 60)
    
    for dataset_folder in sorted(datasets_dir.iterdir()):
        if not dataset_folder.is_dir():
            continue
        
        print(f"\nProcessing: {dataset_folder.name}")
        
        success, stdout, stderr = process_dataset(dataset_folder)
        
        if success:
            print(f"✓ {dataset_folder.name} - SUCCESS")
            successful.append(dataset_folder.name)
        else:
            print(f"✗ {dataset_folder.name} - FAILED")
            failed.append(dataset_folder.name)
            
            # Log error details
            with open(log_file, 'a', encoding='utf-8') as f:
                f.write(f"\n=== {dataset_folder.name} - {datetime.now()} ===\n")
                f.write(f"STDOUT:\n{stdout}\n")
                f.write(f"STDERR:\n{stderr}\n")
    
    # Print summary
    print("\n" + "=" * 60)
    print(f"SUMMARY - Completed: {datetime.now()}")
    print(f"  Successful: {len(successful)}")
    print(f"  Failed: {len(failed)}")
    
    if failed:
        print(f"\nFailed folders:")
        for folder in failed:
            print(f"  - {folder}")
        print(f"\nCheck {log_file} for error details")
        sys.exit(1)
    else:
        print("\nAll datasets processed successfully!")
        sys.exit(0)

if __name__ == '__main__':
    main()
```

***

## प्रोसेसिंग वर्कफ़्लो

### मानक वर्कफ़्लो

1. **इनपुट**: RAW/JPG छवि जोड़े वाला फ़ोल्डर
2. **डिस्कवरी**: समर्थित छवि फ़ाइलों के लिए CLI ऑटो-स्कैन
3. **प्रसंस्करण**: आपके सीपीयू कोर के समानांतर मोड स्केल (Chloros+)
4. **आउटपुट**: संसाधित छवियों के साथ कैमरा-मॉडल सबफ़ोल्डर बनाता है

### उदाहरण आउटपुट संरचना

```

MyProject/
├── project.json                             # Project metadata
├── 2025_0203_193056_008.JPG                # Original JPG
├── 2025_0203_193055_007.RAW                # Original RAW
└── Survey3N_RGN/                           # Processed outputs ✓
    ├── 2025_0203_193056_008_Reflectance.tif   # Calibrated reflectance
    ├── 2025_0203_193056_008_Target.tif        # Target detection
    └── ...
```

### प्रसंस्करण समय का अनुमान

100 छवियों (प्रत्येक 12 एमपी) के लिए विशिष्ट प्रसंस्करण समय:

| मोड | समय | हार्डवेयर |
| ----------------- | --------- | ------------------------------------------------ |
| **समानांतर मोड** | 5-10 मिनट | i7/Ryzen 7, 16GB RAM, SSD (16 कर्मचारियों तक) |
| **समानांतर मोड** | 10-15 मिनट | i5/Ryzen 5, 8GB रैम, HDD (8 कर्मचारियों तक) |

{% संकेत शैली = "जानकारी" %}
**प्रदर्शन युक्ति**: प्रसंस्करण समय छवि गणना, रिज़ॉल्यूशन और कंप्यूटर विशिष्टताओं के आधार पर भिन्न होता है।
{% अंतिम संकेत %}

***

## समस्या निवारण

### CLI नहीं मिला

**गलती:**

```
'chloros-cli' is not recognized as an internal or external command
```

**समाधान:**

1. स्थापना स्थान सत्यापित करें:

```powershell
dir "C:\Program Files\Chloros\resources\cli\chloros-cli.exe"
```

2. यदि PATH में नहीं है तो पूर्ण पथ का उपयोग करें:

```powershell
"C:\Program Files\Chloros\resources\cli\chloros-cli.exe" process "C:\Datasets\Field_A"
```

3. PATH में मैन्युअल रूप से जोड़ें:
   * ओपन सिस्टम गुण → पर्यावरण चर
   * पथ चर संपादित करें
   * जोड़ें: `C:\Program Files\Chloros\resources\cli`
   * टर्मिनल पुनः प्रारंभ करें

***

### बैकएंड प्रारंभ होने में विफल**गलती:**

```

Backend failed to start within 30 seconds
```

**समाधान:**

1. जांचें कि क्या बैकएंड पहले से चल रहा है (पहले इसे बंद करें)
2. जांचें कि Windows फ़ायरवॉल अवरुद्ध नहीं हो रहा है
3. भिन्न पोर्ट आज़माएँ:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

4. फोर्स रीस्टार्ट बैकएंड:

```powershell
chloros-cli --restart process "C:\Datasets\Field_A"
```

***

### लाइसेंस/प्रमाणीकरण मुद्दे**गलती:**

```

Chloros+ license required for CLI access
```

**समाधान:**

1. सत्यापित करें कि आपके पास एक सक्रिय Chloros+ सदस्यता है
2. अपने क्रेडेंशियल्स के साथ लॉगिन करें:

```powershell
chloros-cli login user@example.com 'password'
```

3. लाइसेंस की स्थिति जांचें:

```powershell
chloros-cli status
```

4. समर्थन से संपर्क करें: info@mapir.camera

***

### कोई छवियाँ नहीं मिलीं**गलती:**

```

No images found in the specified folder
```

**समाधान:**

1. सत्यापित करें कि फ़ोल्डर में समर्थित प्रारूप हैं (.RAW, .TIF, .JPG)
2. जांचें कि फ़ोल्डर पथ सही है (रिक्त स्थान वाले पथों के लिए उद्धरण चिह्नों का उपयोग करें)
3. सुनिश्चित करें कि आपके पास फ़ोल्डर के लिए पढ़ने की अनुमति है
4. जांचें कि फ़ाइल एक्सटेंशन सही हैं

***

### प्रोसेसिंग स्टॉल या हैंग**समाधान:**

1. उपलब्ध डिस्क स्थान की जाँच करें (आउटपुट के लिए पर्याप्त सुनिश्चित करें)
2. मेमोरी खाली करने के लिए अन्य एप्लिकेशन बंद करें
3. छवि संख्या कम करें (बैचों में प्रक्रिया)

***

### पोर्ट पहले से ही उपयोग में है**गलती:**

```

Port 5000 is already in use
```

**समाधान:**

एक भिन्न पोर्ट निर्दिष्ट करें:

```powershell
chloros-cli --port 5001 process "C:\Datasets\Field_A"
```

***

## अक्सर पूछे जाने वाले प्रश्न

### प्रश्न: क्या मुझे CLI के लिए लाइसेंस की आवश्यकता है?

**ए:**हाँ! CLI के लिए सशुल्क**Chloros+ लाइसेंस** की आवश्यकता होती है।

* ❌ मानक (निःशुल्क) योजना: CLI अक्षम
* ✅ Chloros+ (भुगतान किए गए) प्लान: CLI पूरी तरह से सक्षम

यहां सदस्यता लें: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

***

### प्रश्न: क्या मैं GUI के बिना सर्वर पर CLI का उपयोग कर सकता हूँ?**ए:** हाँ! CLI पूरी तरह से हेडलेस चलता है। आवश्यकताएं:

* Windows सर्वर 2016 या बाद का संस्करण
* विज़ुअल C++ पुनर्वितरण योग्य स्थापित
* पर्याप्त रैम (न्यूनतम 8 जीबी, 16 जीबी अनुशंसित)
* किसी भी मशीन पर एकमुश्त जीयूआई लाइसेंस सक्रियण

***

### प्रश्न: संसाधित छवियाँ कहाँ सहेजी जाती हैं?**ए:** डिफ़ॉल्ट रूप से, संसाधित छवियां कैमरा-मॉडल सबफ़ोल्डर्स (उदाहरण के लिए, `Survey3N_RGN/`) में इनपुट के समान फ़ोल्डर में सहेजी जाती हैं।

विभिन्न आउटपुट फ़ोल्डर निर्दिष्ट करने के लिए `-o` विकल्प का उपयोग करें:

```powershell
chloros-cli process "C:\Input" -o "D:\Output"
```

***

### प्रश्न: क्या मैं एक साथ अनेक फ़ोल्डर संसाधित कर सकता हूँ?**ए:** सीधे एक कमांड में नहीं, लेकिन आप फ़ोल्डरों को क्रमिक रूप से संसाधित करने के लिए स्क्रिप्टिंग का उपयोग कर सकते हैं। [स्वचालन और स्क्रिप्टिंग](CLI.md#automation--scripting) अनुभाग देखें।***

### प्रश्न: मैं CLI आउटपुट को लॉग फ़ाइल में कैसे सहेजूँ?**पावरशेल:**

```powershell
chloros-cli process "C:\Datasets\Field_A" | Tee-Object -FilePath "processing.log"
```

**बैच:**

```batch
chloros-cli process "C:\Datasets\Field_A" > processing.log 2>&1
```

***

### प्रश्न: यदि मैं प्रोसेसिंग के दौरान Ctrl+C दबाऊं तो क्या होगा?**ए:** CLI होगा:

1. शान से प्रसंस्करण बंद करो
2. बैकएंड बंद करें
3. कोड 130 के साथ बाहर निकलें

आंशिक रूप से संसाधित छवियाँ आउटपुट फ़ोल्डर में रह सकती हैं।

***

### प्रश्न: क्या मैं CLI प्रोसेसिंग को स्वचालित कर सकता हूँ?**ए:** बिल्कुल! CLI को स्वचालन के लिए डिज़ाइन किया गया है। पॉवरशेल, बैच और Python उदाहरणों के लिए [ऑटोमेशन और स्क्रिप्टिंग](CLI.md#automation--scripting) देखें।***

### प्रश्न: मैं CLI संस्करण की जांच कैसे करूं?**ए:**

```powershell
chloros-cli --version
```

**आउटपुट:**

```

Chloros CLI 1.0.2
```

***

## मदद प्राप्त करें

### कमांड-लाइन सहायता

सहायता जानकारी सीधे CLI में देखें:

```powershell
# General help
chloros-cli --help

# Command-specific help
chloros-cli process --help
chloros-cli login --help
chloros-cli language --help
```

### सहायता चैनल

* **ईमेल**: info@mapir.camera
* **वेबसाइट**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **मूल्य निर्धारण**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)***

## संपूर्ण उदाहरण

### उदाहरण 1: बुनियादी प्रसंस्करण

डिफ़ॉल्ट सेटिंग्स के साथ प्रक्रिया (विग्नेट, परावर्तन):

```powershell
chloros-cli process "C:\Datasets\Field_A_2025_01_15"
```

***

### उदाहरण 2: उच्च गुणवत्ता वाला वैज्ञानिक आउटपुट

32-बिट फ्लोट TIFF:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "TIFF (32-bit, Percent)" ^
  --vignette ^
  --reflectance
```

***

### उदाहरण 3: तेज़ पूर्वावलोकन प्रसंस्करण

त्वरित समीक्षा के लिए अंशांकन के बिना 8-बिट PNG:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --format "PNG (8-bit)" ^
  --no-vignette ^
  --no-reflectance
```

***

### उदाहरण 4: पीपीके-संशोधित प्रसंस्करण

परावर्तन के साथ पीपीके सुधार लागू करें:

```powershell
chloros-cli process "C:\Datasets\Field_A" ^
  --ppk ^
  --reflectance
```

***

### उदाहरण 5: कस्टम आउटपुट स्थान

विशिष्ट प्रारूप के साथ अलग-अलग ड्राइव पर प्रक्रिया करें:

```powershell
chloros-cli process "C:\Input\Raw_Images" ^
  -o "D:\Output\Processed" ^
  --format "TIFF (16-bit)"
```

***

### उदाहरण 6: प्रमाणीकरण वर्कफ़्लो

पूर्ण प्रमाणीकरण प्रवाह:

```powershell
# Step 1: Login
chloros-cli login user@example.com 'MyP@ssw0rd'

# Step 2: Verify status
chloros-cli status

# Step 3: Process images
chloros-cli process "C:\Datasets\Field_A"

# Step 4: Logout (optional, when switching accounts)
chloros-cli logout
```

***

### उदाहरण 7: बहुभाषी प्रयोग

इंटरफ़ेस भाषा बदलें:

```powershell
# List available languages
chloros-cli language --list

# Change to Spanish
chloros-cli language es

# Process with Spanish interface
chloros-cli process "C:\Vuelos\Campo_A"

# Change back to English
chloros-cli language en
```