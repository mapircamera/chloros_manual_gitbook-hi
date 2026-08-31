# CLI : कमांड लाइन

> **पूर्ण संदर्भ:**[CLI संदर्भ](reference/cli-reference.md)**हर उप-कमांड के हर फ़्लैग** को दस्तावेज़ करता है और AI सहायकों के लिए अनुकूलित है — इसके URL को अपने सहायक में पेस्ट करें और एक काम करने वाली कमांड के लिए पूछें: `https://mapir.gitbook.io/chloros/reference/cli-reference`
>
> **AI टूल्स के लिए टिप:** इस मैनुअल का कोई भी पेज, उसके URL (जैसे `https://mapir.gitbook.io/chloros/reference/cli-reference.md`) में `.md` जोड़कर रॉ मार्कडाउन के रूप में उपलब्ध है, और `https://mapir.gitbook.io/chloros/llms.txt` पूरे मैनुअल को LLM उपभोग के लिए अनुक्रमित करता है।

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: banner shows CLI 1.1.0; reshoot the CLI welcome/banner output on the 1.2.0 build so the version line reads "Chloros CLI 1.2.0" -->


##CLI
क्या है

`chloros-cli` उसी प्रोसेसिंग इंजन का कमांड-लाइन फ्रंट एंड है जिसका उपयोगChloros
डेस्कटॉप ऐप करता है। यहChloros
बैकएंड (`127.0.0.1:5000` पर एक स्थानीय सर्वर) पर एक पतलाHTTP
क्लाइंट है — अधिकांश कमांड स्वचालित रूप से बैकएंड को शुरू करते हैं, इसलिए एक स्क्रिप्ट को केवल एक ही `chloros-cli process …` कॉल की आवश्यकता होती है।

यह **Windows
10/11 (x64)**और**Linux
(x86_64, और JetPack 6 पर NVIDIA Jetson arm64)** पर, किसी भी टर्मिनल में, बिना किसी GUI के चलता है। अपने इंस्टॉल को इस प्रकार सत्यापित करें:

```bash
chloros-cli --version    # prints "Chloros CLI 1.2.0"
```

कमांड परिवार, एक नज़र में:

* **प्रोसेसिंग और खाता** — `process`, `login`, `logout`, `status`, `export-status`, `language` (38 भाषाएँ — [समर्थित भाषाएँ](supported-languages.md) देखें), `set-project-folder` / `get-project-folder` / `reset-project-folder`, `selftest`, `update` (Linux
/केवल Jetson)
* **लाइव हार्डवेयर** — `lattice` (LATTICE कैमरा नियंत्रण, 45+ उप-कमांड), `daq pool-*` (DAQ लाइट सेंसर), `time-sync` (PTP)
* **स्वचालन** — `project` (एक सहेजे गएChloros
प्रोजेक्ट को हेडलेस चलाएँ, जिसमें YAML कैप्चर रेसिपी शामिल हैं)

जानने योग्य वैश्विक विकल्प: `--port N` (बैकएंड पोर्ट, डिफ़ॉल्ट `5000`), `-v/--verbose`, `--restart` (बैकएंड को ज़बरदस्ती पुनः आरंभ करें), `--backend-exe PATH`। पूरी सूचियों के लिए [CLI
संदर्भ](reference/cli-reference.md) देखें।

***

## इंस्टॉलेशन

CLI
**हर प्लेटफ़ॉर्म परChloros
इंस्टॉलर के अंदर आता है** — कोई अलगCLI
डाउनलोड नहीं है। [डाउनलोड](download.md) पेज से इंस्टॉलर प्राप्त करें।

###Windows


इंस्टॉलरCLI
को इस पते पर रखता है:

```

C:\Program Files\Chloros\cli\chloros-cli.exe
```

और उस फ़ोल्डर को आपके सिस्टम `PATH` में जोड़ता है — इंस्टॉल करने के बाद **एक नया टर्मिनल खोलें**ताकि अपडेट किया गया `PATH` पिक हो जाए। इंस्टॉलर इंस्टॉल रूट में लॉन्चर स्क्रिप्ट्स (`Chloros_CLI.bat` / `Chloros_CLI.ps1`) भी डालता है, साथ ही एक**Chloros
CLI
** स्टार्ट-मेनू शॉर्टकट, जिनमें से प्रत्येक एक टर्मिनल खोलता है जिसमें `chloros-cli` उपयोग के लिए तैयार होता है।

###Linux


अपने आर्किटेक्चर के लिए `.deb` इंस्टॉल करें:

```bash
# Linux x86_64
sudo dpkg -i chloros-amd64.deb

# NVIDIA Jetson (arm64, JetPack 6)
sudo dpkg -i chloros-arm64-jp6.deb
```

यह `chloros-cli` को `/usr/bin/chloros-cli` में इंस्टॉल करता है (पहले से `PATH` पर) और बैकएंड को `/usr/lib/chloros/chloros-backend` पर, साथ ही LATTICE कैमरों के लिए आवश्यक ArenaSDK
रनटाइम। विवरण के लिए [Linux
इंस्टॉलेशन](linux/linux-installation.md) देखें।

### सत्यापित करें

```bash
chloros-cli --version    # "Chloros CLI 1.2.0"
chloros-cli selftest     # 7-step diagnostic: backend, API, GPU/CUDA, denoiser models
chloros-cli status       # license tier + logged-in user
```

***

## लॉगिन और लाइसेंसिंग

CLI
(औरPython
SDK
) एक्सेस के लिए **पेडChloros
+ प्लान**की आवश्यकता होती है — किसी भी पेड टियर में यह होता है; फ्री टियर में नहीं। यह सीमा बैकएंड द्वारा**सर्वर-साइड** लागू की जाती है, न किCLI
बाइनरी द्वारा: एक लॉग्ड-आउट कॉल को `401 AUTH_REQUIRED` के साथ अस्वीकार कर दिया जाता है, और मुफ़्त टियर पर लॉग-इन कॉल `403 PLAN_UPGRADE_REQUIRED` के साथ अस्वीकार कर दी जाती है, चाहे वह `chloros-cli`,SDK
, या एक हाथ से बनाए गएHTTP
क्लाइंट से आ रही हो। [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) पर अपग्रेड करें।

प्रति मशीन **एक बार** लॉग इन करें:

```bash
chloros-cli login user@example.com 'YourPassword'
chloros-cli status
```

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: login success output predates 1.2.0; reshoot `chloros-cli login` followed by `chloros-cli status` on the 1.2.0 build showing the license tier line -->


{% hint style="warning" %}
**विशेष वर्णों वाले पासवर्ड**(`$`, `!`, spaces): wrap the password in**single quotes**, as shown above. In PowerShell double quotes, `$$` को शेल द्वारा बिगाड़ दिया जाता है (CLI
401 पर इसे पहचान लेता है और स्वचालित रूप से दोबारा प्रयास करता है, लेकिन सिंगल कोट्स पूरी तरह से इस समस्या से बचाते हैं)।
{% endhint %}

सत्र `~/.chloros/user_session.json` में कैश हो जाता है और प्लान की ग्रेस पीरियड (मासिक प्लान के लिए 30 दिन, वार्षिक प्लान की समाप्ति तक) के लिए ऑफ़लाइन काम करता रहता है। `chloros-cli status` सशुल्क प्लान के बिना भी काम करता है, इसलिए अस्वीकृति का कारण हमेशा दिखाई देता है।

{% hint style="danger" %}
**हेडलेस कार्य शेड्यूल कर रहे हैं? पहले लॉग इन करें।**एक बैकएंड-स्पॉनिंग कमांड (`process`, `status`, `export-status`, …)**नो कैश्ड सेशन**के साथ चलने पर तेज़ी से विफल नहीं होता — यह stdin पर एक इंटरैक्टिव `Email:` / `Password:` प्रॉम्प्ट में चला जाता है। इसलिए एक अनअटेंडेड क्रॉन जॉब या सीआई स्टेप**इनपुट की प्रतीक्षा में हैंग** हो जाएगा। किसी भी चीज़ को शेड्यूल करने से पहले मशीन पर `chloros-cli login EMAIL 'PASSWORD'` एक बार चलाएँ।
{% endhint %}

***

## आपका पहला प्रोसेसिंग रन

`process` को कैप्चर के एक फ़ोल्डर पर पॉइंट करें — यह स्वचालित रूप सेSurvey3
(`.raw` + `.jpg`), LATTICE (`.tif`/`.tiff`), `.dng`, या एक मिश्रण:

```bash
chloros-cli process "C:\Images\flight_001"          # Windows
chloros-cli process ~/images/flight_001              # Linux
```

प्रगति स्ट्रीम प्रत्येक पाइपलाइन थ्रेड (डिटेक्टिंग, एनालाइज़िंग, प्रोसेसिंग, एक्सपोर्टिंग) के अनुसार लाइव होती है, और एक सफल रन यह रिपोर्ट करके समाप्त होता है कि उसने कितने इमेज उत्पाद लिखे (`Image products written: N`)।



<!-- SCREENSHOT-NEEDED: terminal capture of a `chloros-cli process` run on a LATTICE captures folder completing successfully — per-thread progress lines visible and the final "Image products written: N" summary line -->
### आउटपुट कहाँ जाते हैं

`process` आपके इनपुट फ़ोल्डर में नहीं, बल्कि एक **प्रोजेक्ट फ़ोल्डर** में लिखता है:

* यदि कोई `-o` नहीं है: तो प्रोजेक्ट आपके डिफ़ॉल्ट प्रोजेक्ट फ़ोल्डर के अंतर्गत बनाया जाता है (GUI के साथ साझा; इसे `get-project-folder` / के साथ प्रबंधित करें) `set-project-folder`, फॉलबैक `~/Chloros Projects`), जिसका नाम `-n/--project-name` द्वारा या जब इसे छोड़ दिया जाता है तो एक टाइमस्टैम्प (`YYYYMMDD_HHMMSS`) द्वारा रखा जाता है।
* `-o PATH` के साथ: वह फ़ोल्डर **परियोजना फ़ोल्डर** है। यदि इसमें पहले से ही एक `project.json` है, तो ओवरराइट करने के बजाय एक `_1`/`_2`… उपसर्ग वाला सहोदर फ़ोल्डर बनाया जाता है।

प्रोजेक्ट के अंदर, उत्पादों को **कैमरे के अनुसार, फिर फ़ाइल प्रारूप के अनुसार** समूहित किया गया है:

```
<project>/
├── project.json
├── calibration_data.json
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── NDVI_Index_Images/           # one folder per requested index
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

कैमरा फ़ोल्डर LATTICE के लिए `LATT-<sensor>-<lens>-F<filter>` (कैप्चर के EXIF `Model` से मेल खाता है) और `<model>_<filter>` है (जैसे `Survey3N_RGN`)Survey3
के लिए। फ़ॉर्मेट फ़ोल्डर `--format` का अनुसरण करता है: `tiff16`, `tiff8`, `png8`, `jpg8`, या `tiff32` के लिए `TIFF (32-bit, Percent)`।

{% hint style="info" %}
**हर एक्सपोर्ट किया गया उत्पाद SOURCE फ़ाइल का नाम रखता है।**`capture_..._raw.tif` का एक रेडियंस एक्सपोर्ट अभी भी `capture_..._raw.tif` नाम का है — यह बस `tiff32/Radiance_Images/` में रहता है।**फ़ोल्डर उत्पाद की पहचान करता है, फ़ाइल का नाम नहीं**, इसलिए डायरेक्टरी के लिए ग्लॉब (glob) करें, न कि `*radiance*` उपसर्ग के लिए।
{% endhint %}

### वे विकल्प जिनका आप वास्तव में उपयोग करेंगे

| फ़्लैग | डिफ़ॉल्ट | यह क्या करता है |
| --- | --- | --- |
| `-o, --output PATH` | डिफ़ॉल्ट प्रोजेक्ट फ़ोल्डर | प्रोजेक्ट फ़ोल्डर का स्थान (ऊपर देखें)। |
| `-n, --project-name NAME` | टाइमस्टैम्प | प्रोजेक्ट का नाम। |
| `--format FMT` | `TIFF (16-bit)` | `TIFF (16-bit)` में से एक, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. |
| `--indices NAME [NAME ...]` | none | निर्यात के लिए वनस्पति सूचकांक (देखें [वनस्पति सूचकांक](#vegetation-indices))। |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` = न्यूरल डेबायर, धीमा, उच्चतम गुणवत्ता (Chloros
+, NVIDIA GPU). |
| `--vignette / --no-vignette` | चालू | विनेट सुधार। |
| `--reflectance / --no-reflectance` | चालू | परावर्तन कैलिब्रेशन; LATTICE के लिए यह परावर्तन उत्पाद टॉगल भी है। |
| `--input-level {auto,raw,debayered,processed}` | `auto` | LATTICE TIFFs के लिए पाइपलाइन एंट्री पॉइंट को ज़बरदस्ती लागू करें। |

बाकी सब कुछ के लिए — टारगेट-डिटेक्शन ट्यूनिंग, पीपीके, एक्सपोज़र पिन, एरे-अलाइनमेंट फ्लैग — [CLI
रेफरेंस का `process` अनुभाग](reference/cli-reference.md) देखें।

***

## क्या एक्सपोर्ट करना है यह चुनना (LATTICE उत्पाद)

LATTICE प्रोसेसिंग **एक ही पास में हर लागू उत्पाद**में फैलती है। चार प्रति-उत्पाद टॉगल**डिफ़ॉल्ट रूप से चालू** होते हैं; इनमें से किसी एक को बंद करने के लिए `--no-` फ़ॉर्म का उपयोग करें:

| टॉगल | उत्पाद |
| --- | --- |
| `--debayered` | रैखिक डेमोसाइक → `Debayered_Images/` |
| `--preview` | डिस्प्ले पूर्वावलोकन (सफेद संतुलन + गामा; बहु-स्पेक्ट्रल के लिए फाल्स-कलर स्ट्रेच) → `Preview_Images/` |
| `--radiance` | float32 रेडियंस, W/m²/sr/nm → `Radiance_Images/` (हमेशा `tiff32/`) |
| `--reflectance` | uint16 परावर्तन, Pix4D-तैयार → `Reflectance_Calibrated_Images/` |

RGB
मास्टर कैमरों से केवल डेबयेर किया गया + प्रीव्यू ही उत्सर्जित होता है — ब्रॉडबैंड सेंसर के लिए प्रति-बैंड रेडियंस/रिफ्लेक्टेंस का कोई अर्थ नहीं होता, इसलिए वे टॉगल उनके लिए नो-ऑप्स हैं।Survey3
`.raw` टॉगलों को अनदेखा करता है और मानक रिफ्लेक्टेंस/टारगेट पथ का अनुसरण करता है।

```bash
# Radiance only — no DAQ downwelling needed
chloros-cli process ~/captures/lattice_flight --no-debayered --no-preview --no-reflectance
```

**`--reflectance-source {auto,target,daq}`** (डिफ़ॉल्ट `auto`) परावर्तन संदर्भ चुनता है: `auto` फ्रेम के भीतर QA-पासिंग [कैलिब्रेशन लक्ष्य](calibration-targets.md) बनाता है पूर्ण संदर्भ है और जब कोई लक्ष्य मौजूद नहीं होता है तो यह DAQ लाइट-सेंसर डाउनवेलिंग विभाजन (ρ = π·L/E) पर वापस चला जाता है; `target` सख्त है (कोई DAQ प्रतिस्थापन नहीं); `daq` DAQ-प्राधिकृत है। प्रति-इकाई मापे गए लक्ष्य स्कैन `--target-reflectance-dir` के साथ प्रदान किए जा सकते हैं।

{% hint style="info" %}
**रिफ्लेक्टेंस पिक्सल पढ़ना:**DN का अर्थ ρ = 1.0**प्रति-स्रोत** — LATTICE फ़ाइलें XMP में `Chloros:PixelScale=32768` स्टैम्प करती हैं;Survey3
फ़ाइलें 65535 का उपयोग करती हैं (और कोई `Chloros:*` टैग नहीं ले जाती हैं)। टैग को पढ़ें और उसे एक स्थिरांक मानने के बजाय उससे विभाजित करें। विवरण और एक जानबूझकर किया गया बिना-स्केल वाला एज केस [CLI
संदर्भ](reference/cli-reference.md) में हैं।
{% endhint %}

**प्रोसेसिंग हमेशा `raw` से शुरू होती है।** व्युत्पन्न उत्पाद (डेबेयर्ड/रेडियंस/रिफ्लेक्टेंस एक्सपोर्ट्स) को कभी भी पाइपलाइन में वापस नहीं डाला जाता — उन्हें फिर से आयात करके प्रोसेस करने से कैलिब्रेशन गणित दो बार लागू हो जाएगा, इसलिएChloros
उन्हें छोड़ देता है और ऐसा बताता भी है। `--input-level` एक जानबूझकर बनाया गया बचाव का रास्ता है जब आपको वास्तव में किसी एंट्री पॉइंट को जबरदस्ती लागू करने की आवश्यकता होती है।

***

## जब एक रन विफल होता है

1.2.0 से, `process` बिना दिखाने के कुछ भी करके &quot;सफल&quot; होने के बजाय ज़ोर से विफल हो जाता है:

* एक रन जो **उत्पादों का अनुरोध करता है लेकिन कोई नहीं लिखता**— केवल `project.json` और `calibration_data.json` — `Processing finished but wrote no image products.` प्रिंट करता है और**गैर-शून्य पर बाहर निकलता है**, ताकि स्क्रिप्ट इसे पहचान सकें। सामान्य कारण: इनपुट फ़ोल्डर को कैप्चर के रूप में नहीं पहचाना गया (लेआउट और `--input-level` की जाँच करें), या अनुरोधित प्रत्येक उत्पाद उन कैमरों के लिए लागू नहीं था (उदाहरण के लिए, केवलRGB
-कैमरे से रेडियंस/रिफ्लेक्टेंस का अनुरोध करना)।
* एक **जानबूझकर केवल मेटाडेटा रन** (हर प्रोडक्ट टॉगल ऑफ, कोई `--indices` नहीं) अभी भी सफल है — वहाँ खाली इमेज आउटपुट सही परिणाम है।
* `--verbose` के साथ फिर से चलाएँ और `[LATTICE-EXPORT]` / `[EXPORT-CHECK]` लाइनों के लिए बैकएंड लॉग की जाँच करें, जो प्रति-कैमरे स्किप को समझाती हैं।

एग्जिट कोड: `0` सफलता · `1` सामान्य विफलता · `2` तर्क त्रुटि · `130` Ctrl+C द्वारा बाधित।

***

## वनस्पति सूचकांक

एक या अधिक पूर्वनिर्धारित नामों के साथ `--indices` चलाएँ; प्रत्येक सूचकांक अपने स्वयं के `<INDEX>_Index_Images/` फ़ोल्डर में आता है:

```bash
chloros-cli process ~/images/flight_001 --indices NDVI NDRE GNDVI
```

`process --indices` द्वारा स्वीकार किए जाने वाले 22 प्रीसेट नाम:

`NDVI` `GNDVI` `NDRE` `OSAVI` `SAVI` `MSAVI2` `EVI` `MSR` `TDVI` `LAI` `GCI` `GRVI` `GSAVI` `GOSAVI` `NLI` `MNLI` `RDVI` `WDRVI` `CVI` `ENDVI` `GLI` `VARI`

{% hint style="warning" %}
**तीन इंडेक्स सूचियाँ मौजूद हैं — उन्हें एक-दूसरे से न मिलाएं।**GUI के प्रोजेक्ट सेटिंग्स ड्रॉपडाउन में 27 फ़ॉर्मूले हैं (`FCI1`, `FCI2`, `GARI` जोड़ता है, `GEMI`, `LCI` — ये पाँच GUI-केवल हैं और `--indices` के लिए**मान्य** नहीं हैं)। लाइव/ऑफ़लाइन `lattice index --preset` कमांड अपनी अलग 22-प्रीसेट सूची का उपयोग करता है। सूत्र और बैंड मैथ [मल्टीस्पेक्ट्रल इंडेक्स फॉर्मूला](project-settings/multispectral-index-formulas.md) में दस्तावेजीकृत हैं।
{% endhint %}

***

## DAQ लाइट सेंसर: एक त्वरित परिचय

`daq pool-*` परिवार बैकएंड के स्थायी पूल के माध्यम सेMAPIR
DAQ स्पेक्ट्रल सेंसर (DAQ-U यूएसबी पर, DAQ-M BLE पर, DAQ-E ईथरनेट पर) को ड्राइव करता है — GUI,CLI
, औरSDK
सभी एक ही लाइव हैंडल साझा करते हैं। **`pool-*` शिप किए गएCLI
में समर्थित DAQ पथ है;** अन्य `daq` उप-कमांड जिन्हें आप संदर्भित होते हुए देख सकते हैं, वे हैं एकMAPIR
-आंतरिक स्रोत-केवल सतह- और एक स्पष्ट त्रुटि के साथ बाहर निकलना जो आपको `pool-*` की ओर इंगित करती है।

```bash
# 1. Open a pooled session (pick the line matching your sensor)
chloros-cli daq pool-connect                              # smart-detect
chloros-cli daq pool-connect --port COM3                  # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF      # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local   # DAQ-E by hostname (reliable)

# 2. List pooled sensors and their ids
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 3. Read the latest calibrated spectrum (W/m²/nm)
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 4. Record a calibrated .daq file for 60 s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 5. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

`pool-record` बिना `--duration` के तब तक चलता है जब तक `pool-record --stop`; डिफ़ॉल्ट आउटपुट डायरेक्टरी **बैकएंड की मशीन पर** `~/Documents/DAQ Live View/` है। कैप-करेक्शन प्रोफ़ाइल कनेक्ट समय पर चुनी जाती है (`--cap-id`, बैकएंड डिफ़ॉल्ट `sunshine_cosine`) और इसे `pool-set-cap` के साथ लाइव बदला जा सकता है — कैप प्रोफाइल और सेंसर की कैलिब्रेटेड रेंज इस मैनुअल के DAQ अध्यायों में शामिल हैं।

{% hint style="warning" %}
**मल्टी-एनआईसी होस्ट पर DAQ-E:** बूट के बाद पहली `pool-connect --eth` ऑटो-डिस्कवरी एक स्वस्थ सेंसर के साथ भी विफल हो सकती है। `--eth-host <ip-or-hostname>` विश्वसनीय रूप है — जब भी डिस्कवरी खाली हाथ लौटे तो इसका उपयोग करें।
{% endhint %}

***

## लैटिस कैमरे, पीटीपी और प्रोजेक्ट ऑटोमेशन

`lattice` परिवार (45+ उप-कमांड) LATTICE कैमरा के काम को शुरू से अंत तक कवर करता है: डिस्कवरी, एकल कैप्चर, GUI के स्मार्ट-प्रैप कनेक्ट फ्लो के साथ स्थायी समकालिक ऐरे, लाइव ब्राउज़र पूर्वावलोकन, संरेखण, इंडेक्स गणित, और होस्ट-NIC निदान। एक झलक:

```bash
chloros-cli lattice info                                          # discover cameras
chloros-cli lattice capture -o output/                            # one frame, all export types
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4       # persistent synced array
chloros-cli lattice array-capture --processing reflectance -o out/
```

इसके साथ ही: `chloros-cli time-sync` उस PTP ग्रैंडमास्टर की रिपोर्ट करता है जिसेChloros
होस्ट चलाता है (LATTICE कैमरे और DAQ-E सेंसर क्रॉस-डिवाइस टाइमस्टैम्प के लिए इसके स्लेव होते हैं), और `chloros-cli project` एक सहेजा हुआChloros
प्रोजेक्ट खोलता है और इसके कैमरों, ऐरे और सेंसरों को हेडलेस चलाता है — जिसमें स्क्रिप्टेड YAML कैप्चर रेसिपी भी शामिल हैं।

ये तीन परिवार (`lattice`, `project`, `daq pool-*`) वे एकमात्र हैं जो **रिमोट** बैकएंड चलाने के लिए `CHLOROS_BACKEND_URL` का सम्मान करते हैं; कोर कमांड हमेशा स्थानीय मशीन को लक्षित करते हैं।

पूर्ण वॉकथ्रू इस मैनुअल के LATTICE अध्यायों में हैं; हर फ़्लैग [CLI
संदर्भ](reference/cli-reference.md) में है।

***

## समस्या निवारण: शीर्ष 5

| लक्षण | समाधान |
| --- | --- |
| `Login required`, या एक शेड्यूल किया गया जॉब `Email:` प्रॉम्प्ट पर अटक जाता है | इस मशीन पर एक बार `chloros-cli login EMAIL 'PASSWORD'` चलाएँ — कैश्ड सेशन प्रॉम्प्ट के बिना कमांड तेज़ी से विफल होने के बजाय इंटरैक्टिव रूप से काम करते हैं। |
| `backend unreachable` |Chloros
डेस्कटॉप ऐप शुरू करें, या बैकएंड बाइनरी को सीधे चलाएँ (`chloros-backend`). यदि आप `lattice`/`project`/`daq pool-*` को किसी दूरस्थ बैकएंड पर इंगित करते हैं, तो `CHLOROS_BACKEND_URL` की जाँच करें। |
| एरे कनेक्ट अवरुद्ध: `FRAMES WILL DROP` / `Reduce ROI to enable` | होस्ट NIC रिसीव रिंग डिफ़ॉल्ट पर रीसेट — पहले से काम कर रहे रिग के कनेक्ट करने से इनकार करने का #1 कारण, आमतौर पर NIC ड्राइवर अपडेट के बाद। **उन्नत** टर्मिनल से `chloros-cli lattice network --fix` चलाएँ (या `ReceiveBufferLen=256`, `PendingReceives=64` सेट करें); संदर्भ के *होस्ट NIC सेटअप और ट्यूनिंग* देखें। |
| `daq` उप-कमांड बाहर निकलता है: &quot;पूर्ण daq पैकेज की आवश्यकता है…&quot; | शिप किए गए बिल्ड्स पर अपेक्षित — संकलितCLI
केवल `daq pool-*` परिवार को शिप करता है, जो कनेक्ट, स्ट्रीम, रिकॉर्ड, और कैप चयन को कवर करता है। `pool-*` (याPython
से `chloros_sdk.connect_daq_sensor()`) का उपयोग करें। |
| बड़े फ़ोल्डरों से पहले Jetson एक स्वैप चेतावनी प्रिंट करता है | फ़ाइल-समर्थित स्वैप जोड़ें —CLI
चलाने के लिए सटीक `fallocate`/`swapon` कमांड प्रिंट करता है। |

***

## सहायता प्राप्त करना

```bash
chloros-cli --help              # top-level help
chloros-cli process --help      # per-command help
chloros-cli lattice --help
chloros-cli daq --help          # lists the pool-* subcommands
```

* **प्रत्येक फ़्लैग, प्रत्येक सबकमांड:** [CLI
संदर्भ](reference/cli-reference.md)
* **Python
के समकक्ष:** [Python
SDK
](api-python-sdk.md) और [SDK
संदर्भ](reference/sdk-reference.md)
* **समर्थन:** info@mapir.camera · [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
