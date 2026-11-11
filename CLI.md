# CLI: कमांड लाइन

> **पूर्ण संदर्भ:**[CLI संदर्भ](reference/cli-reference.md)**हर उप-कमांड के हर फ़्लैग** को दस्तावेज़ित करता है और AI सहायकों के लिए अनुकूलित है — इसका URL अपने सहायक में पेस्ट करें और एक काम करने वाली कमांड के लिए पूछें: `https://mapir.gitbook.io/chloros/reference/cli-reference`
>
> **AI टूल्स के लिए टिप:** इस मैनुअल का कोई भी पेज, उसके URL में `.md` जोड़कर रॉ मार्कडाउन के रूप में उपलब्ध है (उदा. `https://mapir.gitbook.io/chloros/reference/cli-reference.md`), और `https://mapir.gitbook.io/chloros/llms.txt` LLM उपभोग के लिए पूरे मैनुअल को अनुक्रमित करता है।

> **क्या आप चाहते हैं कि आपका असिस्टेंट स्वयं Chloros चलाए?** `chloros-mcp`, एक MCP सर्वर जो Python SDK के साथ इंस्टॉल होता है, Claude Desktop, Claude Code, Cursor और अन्य MCP क्लाइंट्स के साथ काम करता है: देखें [AI असिस्टेंट्स के साथ Chloros का उपयोग करना](ai-assistants.md).

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: banner shows CLI 1.1.0; reshoot the CLI welcome/banner output on the 1.3.0 build so the version line reads "Chloros CLI 1.3.0" -->


## **CLI** क्या है

`chloros-cli` उसी प्रोसेसिंग इंजन का कमांड-लाइन फ्रंट एंड है जिसका उपयोग Chloros
डेस्कटॉप ऐप करता है। यह Chloros
बैकएंड (`127.0.0.1:5000` पर एक स्थानीय सर्वर) पर एक पतला HTTP
क्लाइंट है — अधिकांश कमांड स्वचालित रूप से बैकएंड को शुरू करते हैं, इसलिए एक स्क्रिप्ट को केवल एक ही `chloros-cli process …` कॉल की आवश्यकता होती है। फिर बैकएंड अगले कमांड के लिए चलता रहता है: यह `chloros-cli logout` पर रुकता है (जब तक कि ऐप खुला न हो या कोई रन या बिल्ड चल रहा न हो), `chloros-cli backend stop` पर, या 30 मिनट बाद अपने आप, जब कोई क्लाइंट, रन या कनेक्टेड डिवाइस न हो। `chloros-cli backend status` इसे दिखाता है।

यह **Windows
10/11 (x64)**और**Linux
(x86_64, और JetPack 6 पर NVIDIA Jetson arm64)** पर, किसी भी टर्मिनल में, बिना किसी GUI के चलता है। अपने इंस्टॉल को इसके साथ सत्यापित करें:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

कमांड परिवार, एक नज़र में:

* **प्रोसेसिंग और खाता** — `process`, `login`, `logout`, `status`, `export-status`, `language` (38 भाषाएँ — [समर्थित भाषाएँ](supported-languages.md) देखें), `set-project-folder` / `get-project-folder` / `reset-project-folder`, `selftest`, `update` (केवल Linux
/Jetson)
* **लाइव हार्डवेयर** — `lattice` (LATTICE कैमरा नियंत्रण, 45+ उप-कमांड), `daq pool-*` (DAQ लाइट सेंसर), `time-sync` (PTP)
* **स्वचालन** — `project` (एक सहेजे गए Chloros
प्रोजेक्ट को हेडलेस चलाएँ, जिसमें YAML कैप्चर रेसिपी शामिल हैं)

जानने योग्य वैश्विक विकल्प: `--port N` (बैकएंड पोर्ट, डिफ़ॉल्ट `5000`), `-v/--verbose`, `--restart` (बैकएंड को ज़बरदस्ती पुनः आरंभ करें), `--backend-exe PATH`। पूरी सूचियों के लिए [CLI
संदर्भ](reference/cli-reference.md) देखें।

***

## इंस्टॉलेशन

CLI
**हर प्लेटफ़ॉर्म पर Chloros
इंस्टॉलर के अंदर आता है** — कोई अलग CLI
डाउनलोड नहीं है। इंस्टॉलर को [डाउनलोड](download.md) पेज से प्राप्त करें।

### Windows


इंस्टॉलर CLI
को इस पते पर रखता है:

```

C:\Program Files\Chloros\cli\chloros-cli.exe
```

और उस फ़ोल्डर को आपके सिस्टम `PATH` में एक बार जोड़ता है (एक अपग्रेड पहले के इंस्टॉलरों द्वारा छोड़ी गई डुप्लिकेट प्रविष्टियों को भी हटाता है) — इंस्टॉल करने के बाद **एक नया टर्मिनल खोलें**ताकि अपडेट किया गया `PATH` पिक हो जाए। इंस्टॉलर इंस्टॉल रूट में लॉन्चर स्क्रिप्ट्स (`Chloros_CLI.bat` / `Chloros_CLI.ps1`) और एक**Chloros
CLI
** भी डालता है। स्टार्ट-मेनू शॉर्टकट, जिनमें से प्रत्येक एक टर्मिनल खोलता है जिसमें `chloros-cli` उपयोग के लिए तैयार होता है।

### Linux


अपने आर्किटेक्चर के लिए `.deb` को `apt` के साथ इंस्टॉल करें (`dpkg -i` नहीं, जो Python
SDK
की अनुशंसित आवश्यकताओं को छोड़ देता है):

```bash
# Linux x86_64
sudo apt install ./chloros_1.3.0_amd64.deb

# NVIDIA Jetson (arm64, JetPack 6)
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
```

यह `chloros-cli` को `/usr/bin/chloros-cli` में इंस्टॉल करता है (पहले से `PATH` पर), बैकएंड और CLI
प्रोग्राम जो `/usr/lib/chloros/backend/` और `/usr/lib/chloros/cli/` में अनपैक किए गए हैं (`/usr/lib/chloros/chloros-backend` एक लॉन्चर है जो बैकएंड को शुरू करता है), एरेना SDK
रनटाइम जिसकी बैकएंड को LATTICE कैमरों के लिए आवश्यकता होती है, और सिस्टम `python3` के लिए Python
SDK
। विवरण के लिए [Linux
इंस्टॉलेशन](linux/linux-installation.md) देखें।

### सत्यापित करें

```bash
chloros-cli --version    # "Chloros CLI 1.3.0"
chloros-cli selftest     # 7-step diagnostic: backend, API, GPU/CUDA, denoiser models
chloros-cli status       # license tier + logged-in user
```

***

## लॉगिन और लाइसेंसिंग

CLI
(और Python
SDK
) एक्सेस के लिए **पेड Chloros
+ प्लान**की आवश्यकता होती है — किसी भी पेड टियर में यह होता है; फ्री टियर में नहीं। यह सीमा**सर्वर-साइड** बैकएंड द्वारा लागू की जाती है, न कि CLI
बाइनरी द्वारा: एक लॉग्ड-आउट कॉल को `401 AUTH_REQUIRED` के साथ अस्वीकार कर दिया जाता है, और फ्री टियर पर लॉग इन किए गए कॉल को `403 PLAN_UPGRADE_REQUIRED` के साथ अस्वीकार कर दिया जाता है, चाहे वह `chloros-cli`, SDK
, या एक हस्तनिर्मित HTTP
क्लाइंट से आया हो। [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) पर अपग्रेड करें।

**प्रति मशीन एक बार** लॉग इन करें:

```bash
chloros-cli login user@example.com 'YourPassword'
chloros-cli status
```

<figure><img src=".gitbook/assets/cli login_w.JPG" alt=""><figcaption></figcaption></figure>
<!-- SCREENSHOT-UPDATE: login success output predates 1.2.0; reshoot `chloros-cli login` followed by `chloros-cli status` on the 1.3.0 build showing the license tier line -->


{% hint style="warning" %}
**विशेष वर्णों वाले पासवर्ड**(`$`, `!`, spaces): wrap the password in**single quotes**, as shown above. In PowerShell double quotes, `$$` को शेल द्वारा बिगाड़ दिया जाता है (CLI
401 पर इसे पहचान लेता है और स्वचालित रूप से दोबारा प्रयास करता है, लेकिन सिंगल कोट्स इस समस्या से पूरी तरह से बचाते हैं).
{% endhint %}

सत्र `~/.chloros/user_session.json` में कैश हो जाता है और प्लान की ग्रेस पीरियड (मासिक प्लान के लिए 30 दिन, वार्षिक प्लान के लिए समाप्ति तक) के लिए ऑफ़लाइन काम करता रहता है। `chloros-cli status` सशुल्क प्लान के बिना भी काम करता है, इसलिए इनकार का कारण हमेशा दिखाई देता है।

{% hint style="danger" %}
**हेडलेस कार्य शेड्यूल कर रहे हैं? पहले लॉग इन करें।**एक बैकएंड-स्पॉन करने वाली कमांड (`process`, `status`, `export-status`, …)**नो कैश्ड सेशन**के साथ चलने पर जल्दी विफल नहीं होता — यह stdin पर एक इंटरैक्टिव `Email:` / `Password:` प्रॉम्प्ट में चला जाता है। इसलिए एक अनअटेंडेड क्रॉन जॉब या सीआई स्टेप**इनपुट की प्रतीक्षा में हैंग** हो जाएगा। किसी भी चीज़ को शेड्यूल करने से पहले मशीन पर `chloros-cli login EMAIL 'PASSWORD'` एक बार चलाएँ।
{% endhint %}

***

## आपका पहला प्रोसेसिंग रन

`process` को कैप्चर के एक फ़ोल्डर पर पॉइंट करें — यह स्वचालित रूप से Survey3
(`.raw` + `.jpg`), LATTICE (`.tif`/`.tiff`), `.dng`, या एक मिश्रण:

```bash
chloros-cli process "C:\Images\flight_001"          # Windows
chloros-cli process ~/images/flight_001              # Linux
```

प्रगति स्ट्रीम प्रत्येक पाइपलाइन थ्रेड (डिटेक्टिंग, एनालाइज़िंग, प्रोसेसिंग, एक्सपोर्टिंग) के अनुसार लाइव होती है, और एक सफल रन यह रिपोर्ट करके समाप्त होता है कि उसने कितने इमेज उत्पाद लिखे हैं: `Image products written: N`, उसके बाद `(plus M clamp masks)` जब रन ने [क्लैम्प मास्क](output-image-formats.md#clamp-masks) भी लिखे हों (उन्हें उत्पादों के रूप में नहीं गिना जाता है)।

<!-- SCREENSHOT-NEEDED: terminal capture of a `chloros-cli process` run on a LATTICE captures folder completing successfully — per-thread progress lines visible and the final "Image products written: N (plus M clamp masks)" summary line -->


यदि कोई Chloros
बैकएंड पहले से ही चल रहा है (उदाहरण के लिए डेस्कटॉप ऐप का), तो `process` उसका उपयोग करता है और उसी में अपना प्रोजेक्ट खोलता है। यह तब तक इनकार करता रहता है जब तक ऐप उस प्रोजेक्ट पर खुला रहता है जिसे ऐप ने खोला था: या तो ऐप से प्रक्रिया करें, या उस प्रोजेक्ट को बंद करें। यदि पिछली रन अभी भी समाप्त हो रही है, तो यह इनकार करने से पहले उसके लिए 15 सेकंड तक प्रतीक्षा करता है। `--restart` एक नया बैकएंड शुरू करता है। Linux
पर, एक बैकएंड जिसे systemd सेवा चलाती है, उसे कभी भी पुनः आरंभ नहीं किया जाता है: `process` इसका उपयोग वैसे ही करता है, और जहाँ यह अन्यथा इसे पुनः आरंभ करता (`--restart`, या कोई इम्पोर्ट जिसे कुछ भी नहीं मिला), यह `sudo systemctl restart <unit>` प्रिंट करता है और `1` के साथ बाहर निकलता है।

### आउटपुट कहाँ जाते हैं

`process` आपके इनपुट फ़ोल्डर में नहीं, बल्कि एक **प्रोजेक्ट फ़ोल्डर** में लिखता है:

* `-o` के बिना: प्रोजेक्ट आपके डिफ़ॉल्ट प्रोजेक्ट फ़ोल्डर में बनाया जाता है (जो GUI के साथ साझा किया जाता है; इसे `get-project-folder` / `set-project-folder` के साथ प्रबंधित करें; जब कोई सेट नहीं होता है, तो `Documents/MAPIR/Chloros Projects` आपके उपयोगकर्ता फ़ोल्डर में), `-n/--project-name` द्वारा नामित या जब छोड़ा जाता है तो एक टाइमस्टैम्प (`YYYYMMDD_HHMMSS`) द्वारा नामित होता है।
* `-o PATH` के साथ: वह फ़ोल्डर **परियोजना फ़ोल्डर** है। यदि इसमें पहले से ही एक `project.json` है, तो एक `_1`/`_2`… उपसर्ग वाला एक सहोदर फ़ोल्डर ओवरराइट करने के बजाय बनाया जाता है। एक सापेक्ष `PATH` उस फ़ोल्डर के सापेक्ष लिया जाता है जिससे आप `chloros-cli` चलाते हैं।

{% hint style="info" %}
**सापेक्ष पथ (1.3.0 में बदला गया)।** बैकएंड द्वारा खोला गया हर पथ — किसी भी कमांड पर `-o`/`--output` (कैप्चर कमांड के डिफ़ॉल्ट `output` सहित), `--input`, `--burst-dir`, `--profile`, `--save` और `--mask` — उस फ़ोल्डर के विरुद्ध हल किया जाता है जहाँ से आप `chloros-cli` चलाते हैं, इसलिए `-o captures/` `<current folder>/captures/` में लिखता है। 1.2.0 में बैकएंड उन्हें अपने स्वयं के इंस्टॉल फ़ोल्डर में हल करता था, जो &quot;Access is denied&quot; (प्रवेश से इनकार किया गया) की त्रुटि के साथ विफल हो जाता था। एक रिमोट बैकएंड (`CHLOROS_BACKEND_URL`) के खिलाफ, पथ टाइप किए गए रूप में भेजे जाते हैं और उस मशीन पर हल हो जाते हैं।
{% endhint %}

{% hint style="warning" %}
**Windows
: जब तक लंबी पथें चालू न हों, प्रोजेक्ट फ़ोल्डर को छोटा रखें।** Windows
में लंबी पथें बंद होने पर (जो Windows
का डिफ़ॉल्ट है), कोई भी फ़ाइल पथ 260 वर्णों से लंबा नहीं हो सकता, और उत्पाद प्रोजेक्ट फ़ोल्डर के नीचे लगभग 130 वर्णों तक ही रहते हैं। `process` शुरू होने से पहले इसकी जाँच करता है: बहुत गहरे प्रोजेक्ट फ़ोल्डर को **&quot;यह प्रोजेक्ट फ़ोल्डर इस कंप्यूटर के लिए बहुत गहरा है&quot;** संदेश के साथ अस्वीकार कर दिया जाता है, सबसे लंबा प्रोजेक्ट फ़ोल्डर जो फिट बैठता है, और लंबे पथों को चालू करने का तरीका — कुछ भी प्रोसेस नहीं किया जाता है। एक छोटा `-o` (या `set-project-folder`) का उपयोग करें, या Windows
के लंबे पथ चालू करें (इंस्टॉलर का **&#x27;Windows
long paths&#x27; सक्षम करें** विकल्प, जो डिफ़ॉल्ट रूप से चयनित होता है) और Chloros
को पुनः आरंभ करें।
{% endhint %}

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

कैमरा फ़ोल्डर LATTICE के लिए `LATT-<sensor>-<lens>-F<filter>` है (जो कैप्चर के EXIF `Model` से मेल खाता है) और `<model>_<filter>` Survey3
के लिए (उदा. `Survey3N_RGN`)। फ़ॉर्मेट फ़ोल्डर `--format` का अनुसरण करता है: `tiff16`, `tiff8`, `png8`, `jpg8`, या `tiff32` `TIFF (32-bit, Percent)` के लिए।

मल्टीस्पेक्ट्रल M3C कैमरा के लिए, एक `clamp_masks/` फ़ोल्डर उत्पाद फ़ोल्डरों (उदाहरण के लिए `tiff16/clamp_masks/Reflectance_Calibrated_Images/` और `tiff32/clamp_masks/Radiance_Images/`) के बगल में एक फ़ॉर्मेट फ़ोल्डर में भी दिखाई दे सकता है। इसमें रेडियंस और रिफ्लेक्टेंस उत्पादों के प्रति-पिक्सल मास्क होते हैं, जिनमें बैंड अनमिक्स ने एक नकारात्मक मान को शून्य पर क्लैंप किया था — देखें [क्लैंप मास्क](output-image-formats.md#clamp-masks)। कोई भी उत्पाद फ़ोल्डर कभी भी मास्क नहीं रखता।

{% hint style="info" %}
**हर एक्सपोर्ट किया गया प्रोडक्ट SOURCE फ़ाइल का नाम रखता है।**`capture_..._raw.tif` के रेडियंस एक्सपोर्ट का नाम अभी भी `capture_..._raw.tif` है — यह बस `tiff32/Radiance_Images/` में रहता है।**फ़ोल्डर उत्पाद की पहचान करता है, फ़ाइल का नाम नहीं**, इसलिए डायरेक्टरी के लिए ग्लॉब (glob) करें, न कि `*radiance*` सफ़िक्स के लिए।
{% endhint %}

### वे विकल्प जिनका आप वास्तव में उपयोग करेंगे

| फ़्लैग | डिफ़ॉल्ट | यह क्या करता है |
| --- | --- | --- |
| `-o, --output PATH` | डिफ़ॉल्ट प्रोजेक्ट फ़ोल्डर | प्रोजेक्ट फ़ोल्डर का स्थान (ऊपर देखें)। |
| `-n, --project-name NAME` | टाइमस्टैम्प | प्रोजेक्ट का नाम। |
| `--format FMT` | `TIFF (16-bit)` | `TIFF (16-bit)` में से एक, `TIFF (8-bit)`, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. Chloros
अपस्केल नहीं करता है: सभी-8-बिट छवियों पर एक गहरा प्रारूप `TIFF (8-bit)` के रूप में लिखा जाता है, और यह एक बार ऐसा कहता है। |
| `--indices NAME [NAME ...]` | कोई नहीं | निर्यात के लिए वनस्पति सूचकांक (देखें [वनस्पति सूचकांक](#vegetation-indices))। |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` = न्यूरल डेबायर, धीमा, सबसे उच्च गुणवत्ता (Chloros
+, CUDA GPU की आवश्यकता है: इसके बिना रन `standard` का उपयोग करता है; LATTICE कैप्चर के लिए अस्वीकार कर दिया गया, जिन्हें हमेशा `standard` मिलता है)। |
| `--vignette / --no-vignette` | चालू | विनेट सुधार। |
| `--reflectance / --no-reflectance` | चालू | परावर्तन कैलिब्रेशन; LATTICE के लिए यह परावर्तन उत्पाद टॉगल भी है। |
| `--input-level {auto,raw,debayered,processed}` | `auto` | LATTICE TIFFs के लिए पाइपलाइन एंट्री पॉइंट को ज़बरदस्ती लागू करें। |

बाकी सब कुछ के लिए — टारगेट-डिटेक्शन ट्यूनिंग, पीपीके, एक्सपोज़र पिन, एरे-अलाइनमेंट फ्लैग — [एक्सपीआरओटीएक्स000155एक्सपीआरओटीएक्स अनुभाग देखें CLI
संदर्भ](reference/cli-reference.md)।

***

## क्या एक्सपोर्ट करना है यह चुनना (LATTICE उत्पाद)

LATTICE प्रोसेसिंग **एक ही पास में हर लागू उत्पाद**में फैलती है। चार प्रति-उत्पाद टॉगल**डिफ़ॉल्ट रूप से चालू** होते हैं; इनमें से किसी एक को बंद करने के लिए `--no-` फ़ॉर्म का उपयोग करें:

| टॉगल | उत्पाद |
| --- | --- |
| `--debayered` | रैखिक डेमोसाइक → `Debayered_Images/`: कैलिब्रेशन पैक का डार्क ऑफ़सेट हटाया जाता है, फिर बिना किसी रैखिकता सुधार, फ़्लैट फ़ील्ड या अनमिक्स के एक एज-अवेयर डेमोसाइक किया जाता है — यह वही फ़ाइल है जिसे कैप्चर अपने `debayered` स्तर पर सहेजता है। सेंसर की मूल अभिविन्यास में रखी जाती है (XMP में दर्ज)। |
| `--preview` | डिस्प्ले पूर्वावलोकन (सफेद संतुलन + गामा; बहु-स्पेक्ट्रल के लिए फाल्स-कलर स्ट्रेच) → `Preview_Images/` |
| `--radiance` | float32 रेडियंस, W/m²/sr/nm → `Radiance_Images/` (हमेशा `tiff32/`) |
| `--reflectance` | uint16 परावर्तन, Pix4D-तैयार → `Reflectance_Calibrated_Images/` |

RGB
मास्टर कैमरों से केवल डेबयेर + प्रीव्यू ही उत्सर्जित होता है — एक ब्रॉडबैंड सेंसर के लिए प्रति-बैंड रेडियंस/रिफ्लेक्टेंस का कोई अर्थ नहीं होता, इसलिए उनके लिए वे टॉगल नो-ऑप्स होते हैं। Survey3
`.raw` टॉगलों को अनदेखा करता है और मानक रिफ्लेक्टेंस/टारगेट पथ का अनुसरण करता है।

```bash
# Radiance only — no DAQ downwelling needed
chloros-cli process ~/captures/lattice_flight --no-debayered --no-preview --no-reflectance
```

**`--reflectance-source {auto,target,daq}`** (डिफ़ॉल्ट `auto`) परावर्तन संदर्भ चुनता है: `auto` फ्रेम के भीतर QA-पास करने वाले [कैलिब्रेशन टारगेट](calibration-targets.md) को पूर्ण संदर्भ बनाता है और DAQ लाइट-सेंसर डाउनवेलिंग विभाजन पर वापस चला जाता है (ρ = π·L/E) जब कोई लक्ष्य मौजूद नहीं होता है (एक लक्ष्य जो QA पास करता है और एक फ़्लैग होता है, वह मापे गए स्कोर पर DAQ के साथ प्रतिस्पर्धा करता है); `target` सख्त है (कोई DAQ प्रतिस्थापन नहीं); `daq` DAQ-प्राधिकृत है। प्रति-इकाई मापे गए लक्ष्य स्कैन `--target-reflectance-dir` के साथ प्रदान किए जा सकते हैं।

{% hint style="info" %}
**रिफ्लेक्टेंस पिक्सल पढ़ना:**DN का अर्थ ρ = 1.0**प्रति-स्रोत** होता है — LATTICE फ़ाइलें XMP में `Chloros:PixelScale=32768` स्टाम्प करती हैं; Survey3
फ़ाइलें 65535 का उपयोग करती हैं और इनमें कोई `Chloros:PixelScale` टैग नहीं होता (हालांकि इनमें अन्य `Chloros:*` प्रोसेसिंग टैग होते हैं)। एक स्थिरांक मानने के बजाय टैग को पढ़ें और उससे विभाजित करें। विवरण और एक जानबूझकर किया गया बिना-स्केल वाला एज केस [CLI
संदर्भ](reference/cli-reference.md) में हैं।
{% endhint %}

**प्रोसेसिंग हमेशा `raw` से शुरू होती है।** व्युत्पन्न उत्पाद (डेबेयर्ड/रेडियंस/रिफ्लेक्टेंस एक्सपोर्ट्स) को कभी भी पाइपलाइन में वापस नहीं डाला जाता — उन्हें फिर से आयात करके प्रोसेस करने से कैलिब्रेशन गणित दो बार लागू होगा, इसलिए Chloros
उन्हें छोड़ देता है और ऐसा बताता भी है। `--input-level` जानबूझकर बनाया गया एक बचाव मार्ग है जब आपको वास्तव में किसी एंट्री पॉइंट को जबरदस्ती लागू करने की आवश्यकता होती है।

***

## जब एक रन विफल होता है

1.2.0 के अनुसार, `process` कुछ भी दिखाए बिना &quot;सफल&quot; होने के बजाय ज़ोर से विफल हो जाता है:

* एक रन जिसने **उत्पादों का अनुरोध किया लेकिन कुछ भी नहीं लिखा**— केवल `project.json` और `calibration_data.json` — `Processing finished but wrote no image products.` प्रिंट करता है और**गैर-शून्य पर बाहर निकलता है**, ताकि स्क्रिप्ट्स इसे पहचान सकें। सामान्य कारण: इनपुट फ़ोल्डर को कैप्चर के रूप में नहीं पहचाना गया (लेआउट और `--input-level` की जाँच करें), या अनुरोधित प्रत्येक उत्पाद उन कैमरों के लिए लागू नहीं था (उदाहरण के लिए, केवल RGB
-कैमरे से रेडियंस/रिफ्लेक्टेंस माँगना)।
* एक **जानबूझकर केवल मेटाडेटा रन** (हर प्रोडक्ट टॉगल ऑफ, कोई `--indices` नहीं) अभी भी एक सफलता है — वहाँ एक खाली इमेज आउटपुट सही परिणाम है।
* `--verbose` के साथ दोबारा चलाएँ और `[LATTICE-EXPORT]` / `[EXPORT-CHECK]` लाइनों के लिए बैकएंड लॉग की जाँच करें, जो प्रति-कैमरे स्किप को समझाती हैं।
* लंबी पथ सेटिंग्स बंद वाले Windows
पर, 260-अक्षर की पथ सीमा से बहुत गहरी प्रोजेक्ट फ़ोल्डर को प्रोसेसिंग शुरू होने **से पहले** अस्वीकार कर दिया जाता है, और सबसे लंबा प्रोजेक्ट फ़ोल्डर जो फिट बैठता है, उसे चुना जाता है (देखें [आउटपुट कहाँ जाते हैं](#where-the-outputs-land)).
* कैप्चर कमांड्स (`lattice capture`, `lattice array-capture` और अन्य कैप्चर परिवार) हर उस फ़ाइल का नाम बताते हैं जिसे वे लिख नहीं सके और `1` पर बाहर निकल जाते हैं; ड्राइव भर जाने पर दोहराए जाने वाले कैप्चर रुक जाते हैं।

एग्जिट कोड: `0` सफलता · `1` सामान्य विफलता · `2` तर्क त्रुटि · `130` Ctrl+C द्वारा बाधित।

***

## वनस्पति सूचकांक

एक या अधिक पूर्वनिर्धारित नामों के साथ `--indices` चलाएँ; प्रत्येक सूचकांक अपने स्वयं के `<INDEX>_Index_Images/` फ़ोल्डर में जाता है:

```bash
chloros-cli process ~/images/flight_001 --indices NDVI GNDVI OSAVI
```

`process --indices` द्वारा स्वीकार किए जाने वाले 22 प्रीसेट नाम:

`NDVI` `GNDVI` `NDRE` `OSAVI` `SAVI` `MSAVI2` `EVI` `MSR` `TDVI` `LAI` `GCI` `GRVI` `GSAVI` `GOSAVI` `NLI` `MNLI` `RDVI` `WDRVI` `CVI` `ENDVI` `GLI` `VARI`

परियोजना में अभी तक सूचीबद्ध नहीं किया गया कोई नाम उसमें जोड़ा जाता है। किसी ऐसे नाम को, जो पूर्वनिर्धारित नहीं है, या जिसे कैमरों के बैंड नहीं बना सकते, एक चेतावनी के साथ छोड़ दिया जाता है और उसका नाम बताया जाता है। यदि CLI
प्रोजेक्ट की वर्तमान इंडेक्स सूची को पढ़ नहीं पाता है, तो यह उसे बदलने के बजाय अपरिवर्तित छोड़ देता है और चेतावनी देता है।

{% hint style="warning" %}
**तीन इंडेक्स सूचियाँ मौजूद हैं — उन्हें एक-दूसरे से न मिलाएँ।**GUI के प्रोजेक्ट सेटिंग्स ड्रॉपडाउन में 27 फ़ॉर्मूले हैं (`FCI1`, `FCI2`, `GARI` जोड़ता है, `GEMI`, `LCI` — ये पाँच GUI-केवल हैं और `--indices` के लिए**मान्य** नहीं हैं)। लाइव/ऑफ़लाइन `lattice index --preset` कमांड अपनी अलग 22-प्रीसेट सूची का उपयोग करती है। सूत्र और बैंड मैथ [मल्टीस्पेक्ट्रल इंडेक्स सूत्र](project-settings/multispectral-index-formulas.md) में प्रलेखित हैं।
{% endhint %}

***

## DAQ लाइट सेंसर: एक संक्षिप्त परिचय

`daq pool-*` परिवार बैकएंड के स्थायी पूल के माध्यम से MAPIR
DAQ स्पेक्ट्रल सेंसर (DAQ-U यूएसबी पर, DAQ-M BLE पर, DAQ-E ईथरनेट पर) को ड्राइव करता है — GUI, CLI
, और SDK
सभी एक ही लाइव हैंडल साझा करते हैं। **`pool-*` शिप किए गए CLI
में समर्थित DAQ पथ है**; अन्य `daq` उप-कमांड जिन्हें आप संदर्भित होते हुए देख सकते हैं, वे एक MAPIR
-आंतरिक स्रोत-केवल सतह हैं और एक स्पष्ट त्रुटि के साथ समाप्त होती हैं जो आपको `pool-*` की ओर इंगित करती है।

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

`lattice` परिवार (45+ उप-कमांड) LATTICE कैमरा के काम को शुरू से अंत तक कवर करता है: डिस्कवरी, एकल कैप्चर, GUI के स्मार्ट-प्रेप कनेक्ट फ्लो के साथ स्थायी समकालिक ऐरे, लाइव ब्राउज़र पूर्वावलोकन, संरेखण, इंडेक्स गणित, और होस्ट-NIC निदान। एक झलक:

```bash
chloros-cli lattice info                                          # discover cameras
chloros-cli lattice capture -o output/                            # one frame, all export types
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4       # persistent synced array
chloros-cli lattice array-capture --processing reflectance -o out/
```

इसके साथ ही: `chloros-cli time-sync` उस PTP ग्रैंडमास्टर की रिपोर्ट करता है जिसे Chloros
होस्ट चलाता है (LATTICE कैमरे और DAQ-E सेंसर क्रॉस-डिवाइस टाइमस्टैम्प के लिए इसके स्लेव होते हैं), और `chloros-cli project` एक सहेजा गया Chloros
प्रोजेक्ट खोलता है और इसके कैमरों, ऐरे और सेंसरों को हेडलेस चलाता है — जिसमें स्क्रिप्टेड YAML कैप्चर रेसिपी भी शामिल हैं।

ये तीन परिवार (`lattice`, `project`, `daq pool-*`) ही एकमात्र ऐसे हैं जो **रिमोट** बैकएंड चलाने के लिए `CHLOROS_BACKEND_URL` का सम्मान करते हैं; कोर कमांड हमेशा स्थानीय मशीन को लक्षित करती हैं।

पूर्ण वॉकथ्रू इस मैनुअल के LATTICE अध्यायों में उपलब्ध हैं; हर फ़्लैग [CLI
संदर्भ](reference/cli-reference.md) में है।

***

## समस्या निवारण: शीर्ष 5

| लक्षण | समाधान |
| --- | --- |
| `Login required`, या एक शेड्यूल किया गया जॉब `Email:` प्रॉम्प्ट पर अटक जाता है | इस मशीन पर एक बार `chloros-cli login EMAIL 'PASSWORD'` चलाएँ — कैश्ड सेशन प्रॉम्प्ट के बिना कमांड तेज़ी से विफल होने के बजाय इंटरैक्टिव रूप से काम करते हैं। |
| `backend unreachable` | Chloros
डेस्कटॉप ऐप शुरू करें, या स्वयं बैकएंड शुरू करें: `"C:\Program Files\Chloros\backend\chloros-backend.exe"` Windows
पर (आपके इंस्टॉल फ़ोल्डर का `backend` फ़ोल्डर; यह `PATH` पर नहीं है), `chloros-backend` Linux
पर। `chloros-cli backend status` दिखाता है कि कोई चल रहा है या नहीं; `lattice`/`project`/`daq pool-*` कभी भी एक को शुरू नहीं करते। यदि आप उन्हें एक रिमोट बैकएंड पर पॉइंट करते हैं, तो `CHLOROS_BACKEND_URL` की जाँच करें। |
| एरे कनेक्ट अवरुद्ध: `FRAMES WILL DROP` / `Reduce ROI to enable` | होस्ट NIC रिसीव रिंग डिफ़ॉल्ट पर रीसेट — पहले से काम कर रहे रिग के कनेक्ट करने से इनकार करने का #1 कारण, आमतौर पर NIC ड्राइवर अपडेट के बाद। **उन्नत** टर्मिनल से `chloros-cli lattice network --fix` चलाएँ (या `ReceiveBufferLen=256`, `PendingReceives=64` सेट करें); संदर्भ के *होस्ट NIC सेटअप और ट्यूनिंग* देखें। |
| `daq` उप-कमांड बाहर निकलता है: &quot;पूर्ण DAQ पैकेज की आवश्यकता है…&quot; | `daq pool-*` कमांड का उपयोग करें, जो बैकएंड के माध्यम से उसी सेंसर को चलाते हैं: कनेक्ट, स्ट्रीम, रिकॉर्ड, कैप चयन, और DAQ-E के लिए इसकी नेटवर्क सेटिंग्स, एक रीस्टार्ट और फर्मवेयर अपडेट (या Python
से `chloros_sdk.connect_daq_sensor()`)। |
| बड़े फ़ोल्डरों से पहले Jetson एक स्वैप चेतावनी प्रिंट करता है | फ़ाइल-समर्थित स्वैप जोड़ें — CLI
चलाने के लिए सटीक `fallocate`/`swapon` कमांड प्रिंट करता है। |

***

## सहायता प्राप्त करना

```bash
chloros-cli --help              # top-level help
chloros-cli process --help      # per-command help
chloros-cli lattice --help
chloros-cli daq --help          # lists the pool-* subcommands
```

* **हर फ़्लैग, हर सबकमांड:** [CLI
संदर्भ](reference/cli-reference.md)
* **Python
समकक्ष:** [Python
SDK
](api-python-sdk.md) और [SDK
संदर्भ](reference/sdk-reference.md)
* **सहायता:** info@mapir.camera · [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
