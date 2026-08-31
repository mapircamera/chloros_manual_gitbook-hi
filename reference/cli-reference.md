# Chloros CLI संदर्भ

**संस्करण:**

1.2.0**उत्पादित:**2026-07-29 19:19 ·**संशोधित:** 2026-08-30**लक्षित दर्शक:** एलएलएम (LLM) उपयोग के लिए अनुकूलित; मानव-पठनीय।**दायरा:** `chloros-cli` की हर उपयोगकर्ता-सामना करने वाली उप-कमांड, विकल्पों और कॉपी-पेस्ट करने योग्य उदाहरणों के साथ।

यह दस्तावेज़ `chloros-cli` कमांड-लाइन टूल के लिए संपूर्ण संदर्भ है जो MAPIR Chloros के साथ आता है। इसे जानबूझकर संपूर्ण बनाया गया है ताकि एक LLM (या मानव) स्रोत कोड की जाँच किए बिना नीचे दी गई सूचियों से किसी भी समर्थित वर्कफ़्लो को तैयार कर सकता है।

यदि आपको केवल मुख्य बातें जाननी हैं, तो यहाँ जाएँ:
- [पाँच-मिनट का क्विकस्टार्ट](#five-minute-quickstart)
- [LATTICE कैमरा फर्स्ट-कनेक्ट वर्कफ़्लो](#lattice-camera-first-connect-workflow)
- [DAQ सेंसर फर्स्ट-कनेक्ट वर्कफ़्लो](#daq-sensor-first-connect-workflow)
- [स्मार्ट-AE / स्मार्ट-कैप्चर](#smart-ae--smart-capture)
- [कैप्चर मोड, रिकॉर्डर और ऑफ़लाइन रीप्रोसेस](#capture-modes-recorders--offline-reprocess)

---

## कन्वेंशन

- सभी कमांड्स के आगे `chloros-cli` उपसर्ग होता है। Windows पर बाइनरी `chloros-cli.exe` है; Linux/Jetson पर यह `chloros-cli` है।
- वैकल्पिक तर्क `--flag` के रूप में दिखाए जाते हैं। आवश्यक स्थितिगत तर्क कोष्ठकों के बिना दिखाए जाते हैं।
- जहाँ कोई डिफ़ॉल्ट दिया गया है, वहाँ फ़्लैग को छोड़ने पर वह मान उपयोग किया जाता है।
- CLI, Chloros बैकएंड ( पर Flask सर्वर) पर एक थिन HTTP क्लाइंट है।9)। बैकएंड अधिकांश कमांड्स द्वारा स्वचालित रूप से शुरू किया जाता है। `CHLOROS_BACKEND_URL=<url>` **`lattice`**,**`project`**, और**`daq pool-*`** रिमोट बैकएंड पर कमांड परिवार — कोर कमांड्स (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) जानबूझकर `http://127.0.0.1:<port>` को पिन करते हैं और उसे अनदेखा करते हैं (IPv4 लिटरल Windows का `localhost`→`::1` ~2 प्रति-अनुरोध पेनल्टी). [एनवायरनमेंट वेरिएबल्स](#environment-variables) देखें।
- सभी SDK / CLI के लिए एक Chloros+ खाते में लॉगिन आवश्यक है। कॉल करता है (प्रति मशीन एक बार `chloros-cli login` चलाएँ; `~/.chloros/` में कैश किया गया है)।
- उदाहरण Linux पथों का उपयोग करते हैं; Windows पर `/home/user/...` को `C:/Users/.../...` से बदलेंX.

---

## शीर्ष-स्तरीय सारांश

```
chloros-cli [global options] COMMAND [command options]
```

### वैश्विक विकल्प

| फ़्लैग | विवरण |
| --- | --- |
| `--backend-exe PATH` | स्वचालित रूप से पता लगाए गए बैकएंड निष्पादन योग्य को ओवरराइड करें। |
| `--port N` | बैकएंड HTTP पोर्ट (डिफ़ॉल्ट: `5000`). |
| `-v, --verbose` | विस्तृत आउटपुट सक्षम करें। |
| `--restart` | बैकएंड को जबरदस्ती पुनः आरंभ करें (किसी भी चल रहे `backend_server.py`). |
| `--version` | संस्करण प्रिंट करें (`Chloros CLI 1.2.0`). |
| `--help` | शीर्ष-स्तरीय सहायता दिखाएँ। |

### कमांड अनुक्रमणिका

| कमांड | उद्देश्य |
| --- | --- |
| [`process`](#chloros-cli-process) | Survey3 या LATTICE कैप्चर के एक फ़ोल्डर को एंड-टू-एंड प्रोसेस करें। |
| [`login`](#chloros-cli-login) | इस मशीन को एक Chloros+ खाते के साथ प्रमाणीकृत करें। |
| [`logout`](#chloros-cli-logout) | कैश किए गए क्रेडेंशियल साफ़ करें। |
| [`status`](#chloros-cli-status) | वर्तमान लाइसेंस / प्रमाणीकरण स्थिति दिखाएँ। |
| [`export-status`](#chloros-cli-export-status) | `process` रन के दौरान लाइव थ्रेड-4 निर्यात प्रगति। |
| [`language`](#chloros-cli-language) | CLI प्रदर्शन भाषा सेट करें या सूचीबद्ध करें (38 समर्थित)। |
| [`set-project-folder`](#project-folder-commands) / [`get-project-folder`](#project-folder-commands) / [`reset-project-folder`](#project-folder-commands) | डिफ़ॉल्ट प्रोजेक्ट फ़ोल्डर (GUI के साथ साझा)। |
| [`update`](#chloros-cli-update) | CLI अपडेट के लिए जाँच करें और इंस्टॉल करें (Linux /Jetson)। |
| [`selftest`](#chloros-cli-selftest) | सिस्टम डायग्नोस्टिक्स + स्मोक टेस्ट। |
| [`time-sync`](#chloros-cli-time-sync) | PTP ग्रैंडमास्टर स्थिति / नियंत्रण। |
| [`lattice`](#chloros-cli-lattice) | LATTICE कैमरा नियंत्रण और कैप्चर (45+ सबकमांड). |
| [`daq`](#chloros-cli-daq) | DAQ स्पेक्ट्रल-सेंसर नियंत्रण (DAQ-U / DAQ-M / DAQ-E). |
| [`project`](#chloros-cli-project) | सहेजे गए Chloros प्रोजेक्ट (कैमरे + DAQs) को खोलें और चलाएँ। |

---

## स्थापना

`chloros-cli` हर समर्थित प्लेटफ़ॉर्म पर Chloros डेस्कटॉप इंस्टॉलर के अंदर आता है — कोई अलग CLI डाउनलोड नहीं है। प्लेटफ़ॉर्म पैकेज स्थापित करने से डेस्कटॉप ऐप और के साथ `PATH` में `chloros-cli` जुड़ जाता है और जिस बैकएंड बाइनरी को यह चलाता है।

नवीनतम डाउनलोड: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

> इंस्टॉलर सुविधाजनक लॉन्चर स्क्रिप्ट्स (`Chloros_CLI.bat` / `Chloros_CLI.ps1`, `Launch_CLI.*`, `chloros-cli.sh`) जो एक तैयार-से-उपयोग CLI शेल खोलती हैं; इन्हें [CLI उपयोगकर्ता गाइड](../CLI.md) में शामिल किया गया है। और यहाँ दोहराया नहीं गया है।

### Windows (.exe)

1. डाउनलोड पेज से Windows इंस्टॉलर डाउनलोड करें।
2. `Chloros-Setup-x.y.z.exe` चलाएँ और विज़ार्ड का पालन करें। डिफ़ॉल्ट इंस्टॉल पथ  है2 (CLI `C:\Program Files\Chloros\cli\` में उतरता है, जिसे इंस्टॉलर PATH में जोड़ता है)।
3. एक नया टर्मिनल खोलें (`cmd.exe`, PowerShell, या Windows Terminal) ताकि अपडेट किया गया `PATH` पिक हो जाए।

```powershell
chloros-cli --version
```

इंस्टॉलर स्वचालित रूप से आपके सिस्टम `PATH` में `chloros-cli.exe` जोड़ता है और LATTICE कैमरों के लिए आवश्यक Arena SDK रनटाइम को बंडल करता है।

### Linux amd64 (.deb)

उबंटू 22.04 LTS या नए / डेबियन के लिए-आधारित x86_64 वर्कस्टेशनों पर।

> **उबंटू 20.04 समर्थित नहीं है।** पैकेज की निर्भरता सूची उस पर आधारित है
> जिसके खिलाफ बैकएंड वास्तव में लिंक होता है, और इसमें `libc6 (>= 2.34)` शामिल है;
> फोकल glibc 2 प्रदान करता है.31। `apt` रनटाइम पर विफल होने देने के बजाय इंस्टॉलेशन को अस्वीकार कर देता है।

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
```

.deb इंस्टॉल करता है:
- `chloros-cli` से `/usr/bin/chloros-cli` तक
- कंपाइल्ड बैकएंड को `/usr/lib/chloros/chloros-backend` तक
- एरिना रनटाइम (SDK) (LATTICE कैमरों के लिए)
- डेनॉइज़र मॉडल, कैलिब्रेशन बंडल, और अपडेट-चैनल कॉन्फ़िगर

### Linux arm64 — जेटसन (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
```

amd64 .deb जैसा ही लेआउट, जिसमें Jetson Orin / Orin NX / Orin Nano के लिए ट्यून किया गया CUDA बिल्ड है।

### प्रति मशीन एक बार प्रमाणीकरण करें

SDK / CLI कॉल काम करने से पहले हर प्लेटफ़ॉर्म को एक बार Chloros+ लॉगिन की आवश्यकता होती है:

```bash
chloros-cli login user@example.com 'YourPassword'
```

क्रेडेंशियल `~/.chloros/user_session.json` में कैश किए जाते हैं।

### इंस्टॉलेशन सत्यापित करें

```bash
chloros-cli --version           # prints "Chloros CLI 1.2.0"
chloros-cli selftest            # full 7-step diagnostic (backend, GPU, models, CUDA)
chloros-cli status              # shows license tier + logged-in user
```

> **Chloros+ सदस्यता आवश्यक है।**CLI के लिए एक सक्रिय Chloros+ प्लान की आवश्यकता है।**कॉपर**एंट्री-लेवल Chloros+ टियर है — हर पेड Chloros+ टियर में CLI / SDK एक्सेस होता है; केवल मुफ्त**Iron** स्तर में यह सुविधा नहीं है। (प्लान-आईडी मानचित्र: `0`=Iron/मुफ्त, `1`=Copper, `2`=Bronze, `3`=Silver, `4`=Gold.) [`https://cloud.mapir.camera/pricing`] पर अपग्रेड करें(https://cloud.mapir.camera/pricing).
>
> यह सीमा बैकएंड द्वारा लागू की जाती है, केवल CLI द्वारा नहीं, बल्कि बैकएंड द्वारा भी: एक पेड प्लान के बिना SDK / CLI-फ्लैग वाली रिक्वेस्ट को `403 PLAN_UPGRADE_REQUIRED` के साथ रिजेक्ट कर दिया जाता है, चाहे वह `chloros-cli`, Python SDK, या एक हैंड-रोल्ड HTTP क्लाइंट से आई हो। एक लॉग-आउट किए हुए कॉलर को  मिलता है122 के बजाय। एक्सेस प्लान की ग्रेस पीरियड (मासिक 30 दिन, वार्षिक तक समाप्ति तक) के दौरान ऑफ़लाइन काम करता है और वह अवधि समाप्त होने पर रुक जाता है; `chloros-cli status` काम करता रहता है ताकि कारण दिखाई दे (यह वह एकमात्र मार्ग है जो SDK / CLI से छूट प्राप्त है — `GET /api/license-status`)।

---

## पाँच मिनट में त्वरित शुरुआत

```bash
# 1. Sign in once on this machine
chloros-cli login user@example.com 'YourPassword'

# 2. Survey3 / LATTICE folder → finished radiance + NDVI in one call
chloros-cli process "/home/user/captures/flight_001" \
  --vignette --reflectance --indices NDVI NDRE GNDVI

# 3. Take a single LATTICE photo with the first camera found
chloros-cli lattice capture -o output/

# 4. Connect a 4-cam LATTICE array with the GUI's smart-prep flow
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 5. Read a spectrum from a connected DAQ-U
chloros-cli daq pool-connect --port COM3
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F   # id from 'daq pool-list'
```

---

## `chloros-cli process`

छवियों के एक फ़ोल्डर को पूरी Chloros पाइपलाइन (लक्ष्य का पता लगाना → कैलिब्रेशन → विनेट → परावर्तन → इंडेक्स निर्यात) के माध्यम से संसाधित करें।

### सारांश

```
chloros-cli process INPUT [OPTIONS]
```

### पोजिशनल आर्गुमेंट्स

| आर्गुमेंट | विवरण |
| --- | --- |
| `INPUT` | `.raw + .jpg` वाले इनपुट फ़ोल्डर का पथ (Survey3), `.tif/.tiff` (LATTICE), या `.dng` फ़ाइलें। |

### सामान्य विकल्प

| फ़्लैग | डिफ़ॉल्ट | विवरण |
| --- | --- | --- |
| `-o, --output PATH` | आपके डिफ़ॉल्ट प्रोजेक्ट पथ के अंतर्गत एक नया टाइमस्टैम्प किया हुआ फ़ोल्डर (`~/Chloros Projects` जब तक कि कॉन्फ़िगर न किया गया हो) | बनाने या पुन: उपयोग करने के लिए प्रोजेक्ट फ़ोल्डर। यदि फ़ोल्डर में पहले से ही एक `project.json`, एक `_1`/`_2` को ओवरराइट करने के बजाय एक नया सिबलिंग फ़ोल्डर बनाया जाता है। |
| `-n, --project-name NAME` | ऑटो (टाइमस्टैम्प) | प्रोजेक्ट का नाम। |
| `--debayer {standard,texture-aware}` | `standard` | `texture-aware` एक Chloros+ न्यूरल डेबेयर का उपयोग करता है; धीमा लेकिन उच्च गुणवत्ता वाला। |
| `--vignette / --no-vignette` | `--vignette` | विनेट सुधार। |
| `--reflectance / --no-reflectance` | `--reflectance` | परावर्तन कैलिब्रेशन (यदि मिले तो पैनल लक्ष्य का उपयोग करता है, LATTICE के लिए NIST प्रति-सीरियल कैलिब्रेशन)। LATTICE मल्टीस्पेक्ट्रल के लिए यह रिफ्लेक्टेंस **उत्पाद** टॉगल के रूप में भी काम करता है — देखें [प्रति-उत्पाद निर्यात टॉगल](#per-product-export-toggles-lattice-multispectral)। |
| `--ppk` | बंद | साइडकार फ़ाइलों से PPK GNSS सुधार लागू करें। |
| `--exposure-pin-1 MODEL` | बंद | एक Survey3 डुअल-कैमरा रिग के &quot;पिन-1&quot; मॉडल को पिन करें। |
| `--exposure-pin-2 MODEL` | बंद | &quot;pin-2&quot; मॉडल को पिन करें। |
| `--recal-interval SECONDS` | 0 | कैप्चर के हर N सेकंड में कैलिब्रेशन मैथ को फिर से चलाने के लिए मजबूर करें-बार। |
| `--timezone-offset HOURS` | local | आउटपुट मेटाडेटा में अंतर्निहित टाइमज़ोन ऑफ़सेट को ओवरराइड करें। |
| `--format FORMAT` | `TIFF (16-bit)` |  में से एक150, `TIFF (32-bit, Percent)`, `PNG (8-bit)`, `JPG (8-bit)`. |
| `--indices NAME [NAME ...]` | कोई नहीं | वनस्पति सूचकांक (`NDVI`, `NDRE`, `GNDVI`, `EVI`, `SAVI`, `OSAVI`, `CIG`, …). |
| `--input-level {auto,raw,debayered,processed}` | `auto` | LATTICE TIFFs के लिए पाइपलाइन एंट्री पॉइंट को जबरदस्ती लागू करें (Survey3 .raw अप्रभावित रहता है)। साथ ही वह एस्केप हैच जो **नो रॉ** (no raw) के साथ एक कैप्चर को बिल्कुल भी प्रोसेस होने देती है — देखें [एक कैप्चर फ़ोल्डर कैसा दिखता है](#what-a-captures-folder-looks-like). |
| `--debayered / --no-debayered` | चालू | रैखिक डेबयेयर्ड उत्पाद उत्सर्जित करें (`Debayered_Images`)। देखें [प्रति-उत्पाद निर्यात टॉगल](#per-product-export-toggles-lattice-multispectral). |
| `--preview / --no-preview` | चालू | डिस्प्ले पूर्वावलोकन उत्सर्जित करें (`Preview_Images`): RGB = व्हाइट बैलेंस (उपलब्ध होने पर DAQ-इल्यूमिनेंट, अन्यथा ग्रे-वर्ल्ड) + गामा; multispec = false-रंग स्ट्रेच। |
| `--radiance / --no-radiance` | चालू | फ्लोट32 रेडियंस उत्सर्जित करें (`Radiance_Images`, W/m²/sr/nm). |
| `--reflectance-source {daq,target,auto}` | `auto` | LATTICE प्रतिबिंब उत्पाद के लिए संदर्भ: `auto` = QA-पासिंग इन-फ्रेम लक्ष्य है परम संदर्भ, DAQ-डाउनवेलिंग (ρ = π·L/E) फॉलबैक; `target` = सख्त (कोई DAQ प्रतिस्थापन नहीं); `daq` = DAQ-प्राधिकृत। [प्रति-उत्पाद निर्यात टॉगल](#per-product-export-toggles-lattice-multispectral) देखें. |
| `--target-reflectance-dir DIR` | कोई नहीं | प्रति-इकाई **मापे गए** लक्ष्य परावर्तन स्कैन की निर्देशिका (`<serial>.csv`); विफल होने पर नाममात्र T3/T4P स्पेक्ट्रा पर वापस चला जाता है। |
| `--array-alignment / --no-array-alignment` | चालू | LATTICE ऐरे: प्रत्येक कैप्चर के  में अंकित मॉड्यूल-से-मॉड्यूल संरेखण लागू करें000178 प्रत्येक संसाधित उत्पाद (डेबॉयर्ड / प्रीव्यू / रेडियंस / रिफ्लेक्टेंस / इंडेक्स) पर XMP। टैग के बिना छवियों के लिए नो-ऑप। |
| `--array-alignment-crop / --no-array-alignment-crop` | क्रॉप | क्रॉप संरेखित एक्सपोर्ट्स एरे के सामान्य-ओवरलैप क्षेत्र ताकि सभी मॉड्यूल एक ही फुట్‌प्रिंट साझा करें; `--no-…` पूरे सेंसर कैनवास को बरकरार रखता है (स्रोत के बाहर ब्लैक फिल)। |
| `--array-alignment-interp {bilinear,nearest,cubic}` | `bilinear` | के लिए रीसैंपलिंग संरेखण विकृति। `nearest` सटीक स्रोत DNs को संरक्षित करता है (रेडियोमेट्रिक मानों का अंतर-पिक्सेल मिश्रण नहीं)। |

### लक्ष्य-पहचान विकल्प

| फ़्लैग | विवरण |
| --- | --- |
| `--min-target-size PIXELS` | न्यूनतम पैनल-लक्ष्य आकार (px) डिटेक्टर के लिए। |
| `--target-clustering 0-100` | क्लस्टरिंग संवेदनशीलता। |
| `--target / --targets` | इनपुट फ़ोल्डर को केवल-टारगेट-पैनल के रूप में मानें (सर्वेक्षण डिटेक्शन छोड़ें)। |

### उदाहरण

```bash
# Simplest: defaults are good for most surveys
chloros-cli process "/home/user/images/survey_001"

# Multi-index with explicit format
chloros-cli process "/home/user/images/survey_001" \
  --vignette \
  --reflectance \
  --format "TIFF (32-bit, Percent)" \
  --indices NDVI NDRE GNDVI OSAVI

# Texture-aware debayer for highest quality (Chloros+ only)
chloros-cli process "/home/user/images/survey_001" \
  --debayer texture-aware \
  --indices NDVI

# Process LATTICE captures explicitly (auto-detects from EXIF normally)
chloros-cli process "/home/user/captures/lattice_flight" \
  --input-level processed

# LATTICE multispectral → float32 radiance only (no DAQ downwelling needed)
chloros-cli process "/home/user/captures/lattice_flight" \
  --no-debayered --no-preview --no-reflectance

# LATTICE reflectance anchored to an in-frame target (strict, no DAQ fallback),
# with per-unit measured target scans looked up by serial
chloros-cli process "/home/user/captures/lattice_flight" \
  --reflectance-source target --target-reflectance-dir "/home/user/target_scans"

# LATTICE array capture: keep native geometry (ignore stamped alignment)
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment

# Aligned, uncropped, value-preserving resampling
chloros-cli process "/home/user/captures/array_flight" \
  --no-array-alignment-crop --array-alignment-interp nearest

# Save to a custom output location with a project name
chloros-cli process "C:/input" -o "C:/output" -n "Field_A_2026-05-26"
```

### प्रति-उत्पाद एक्सपोर्ट टॉगल (LATTICE मल्टीस्पेक्ट्रल)

LATTICE प्रोसेसिंग **एक ही पास में हर लागू उत्पाद**में फैलती है। चार प्रति-प्रकार के टॉगल — `--debayered`, `--preview`, `--radiance`, `--reflectance` — सभी**डिफ़ॉल्ट रूप से चालू** होते हैं; किसी एक को बंद करने के लिए `--no-<type>` फ़ॉर्म का उपयोग करें। RGB मास्टर कैम केवल कभी भी डेबयेर किया हुआ + प्रीव्यू उत्सर्जित करते हैं (प्रति-बैंड रेडियंस/रिफ्लेक्टेंस नहीं), इसलिए `--radiance`/`--reflectance` उनके लिए नो-ऑप्स हैं। Survey3 `.raw` (जो मानक प्रतिबिंबन/लक्ष्य पथ का अनुसरण करता है) के लिए ये टॉगल्स अनदेखे कर दिए जाते हैं।. *(पुराने `--radiometric-output {reflectance,radiance,sensor-response}` फ्लैग को **हटा दिया गया** और इन टॉगल से बदल दिया गया; अब कोई `sensor-response` स्तर नहीं है।)*

| उत्पाद | आउटपुट | क्या DAQ डाउनवेलिंग आवश्यक है? |
| --- | --- | --- |
| `--debayered` | रैखिक डेमोसाइक (`Debayered_Images`). | नहीं. |
| `--preview` | डिस्प्ले पूर्वावलोकन (`Preview_Images`): RGB = WB + gamma; multispec = false-colour stretch. | नहीं. |
| `--radiance` | पूर्ण रेडियोमेट्रिक श्रृंखला से float32 W/m²/sr/nm (`Radiance_Images`). | नहीं. |
| `--reflectance` | uint16 परावर्तन ρ (`32768` = 1.0), Pix4D-तैयार। | **हाँ**, जब तक कि कोई QA-पासिंग इन-फ्रेम लक्ष्य इसे एंकर न करे (नीचे देखें)। |

`--reflectance-source` परावर्तन संदर्भ चुनता है:**`auto`**(डिफ़ॉल्ट) एक QA-पास करने वाले इन-फ्रेम लक्ष्य को**परम संदर्भ**बनाता है — लक्ष्य-एंकरेड अनुभवजन्य-लाइन श्रृंखलाओं को रखे गए पैनलों पर क्रॉस-स्कोर किया जाता है और मापा गया विजेता लागू किया जाता है — DAQ डाउनवेलिंग विभाजन पर वापस लौटता है (ρ = π·L/E) जब कोई लक्ष्य मौजूद नहीं होता है या QA विफल हो जाता है;**`target`**सख्त है (कोई DAQ प्रतिस्थापन नहीं);**`daq`**DAQ-प्राधिकृत व्यवहार को चुनता है। लक्ष्य ज्यामिति (ArUco / fixed-ROI / strip) प्रोजेक्ट लक्ष्य कॉन्फ़िग से आती है; `--target-reflectance-dir DIR` प्रति-इकाई**मापे गए** स्कैन को होल्ड करता है (`<serial>.csv`) को लक्ष्य यूनिट के सीरियल/QR द्वारा देखा जाता है, जिसमें नाममात्र T3/T4P स्पेक्ट्रा फॉलबैक के रूप में होते हैं।

DAQ रिफ्लेक्टेंस पथ **टाइमस्टैम्प-मैच्ड डाउनवेलिंग**को हल करता है रिकॉर्ड किए गए**`.daq`**(DAQ-U/M/E)**या इमेजरी के साथ पाए गए DAQ-M नेटिव `.csv`**से स्वचालित रूप से। यदि प्रति-कैमरा या DAQ कैलिब्रेशन बंडल मौजूद नहीं हैस्थानीय रूप से कैश किया गया हो, तो पाइपलाइन पहली बार उपयोग पर**इसे AWS से स्वचालित रूप से प्राप्त करती है** (एक बार इंटरनेट की आवश्यकता; `~/.chloros/` के तहत कैश किया गया)।

#### रिफ्लेक्टेंस पिक्सल पढ़ना (Pix4D / Metashape / आपकी अपनी स्क्रिप्ट)

रिफ्लेक्टेंस को पूर्णांक DN के रूप में संग्रहीत किया जाता है, और **ρ = 1.0 का अर्थ रखने वाला DN स्रोत कैमरे पर निर्भर करता है**:

| स्रोत | ρ = 1.0 है | कैसे जानें |
| --- | --- | --- |
| LATTICE (M3C / M3M) | `32768` (ρ 2.0 तक की गुंजाइश) | XMP `Chloros:PixelScale=32768` फ़ाइल पर स्टैम्प किया गया है। |
| Survey3 | `65535` (ρ 1.0 पर क्लिप किया गया) | कोई `Chloros:*` XMP टैग नहीं — यह अनुपस्थिति ही संकेत है। |

`Chloros:PixelScale` को पढ़ें और उससे विभाजित करें** एक स्थिरांक मानने के बजाय। यह टैग uint16 डोमेन में परिभाषित है, इसलिए यह उन आउटपुट प्रारूपों में भी `32768` ही रहता है जो पुनः पैमाने को समायोजित करते हैं — `TIFF (16-bit)`, `PNG (8-bit)`, `JPG (8-bit)` और `TIFF (32-bit, Percent)` सभी स्व-वर्णनात्मक हैं (पहले संग्रहीत dtype को वापस uint16 में सामान्य करें: 8-बिट से ×257, फ्लोट से ×65535)।

> **एक मामला, डिज़ाइन के अनुसार, कोई स्केल नहीं रखता है।** जब एक 8-बिट-सोर्स कैप्चर (BayerRG8) को 8-बिट TIFF के रूप में लिखा जाता है, तो पाइपलाइन *पुनः-स्केल करने के बजाय 0..255 तक क्लिप करता है, इसलिए ρ≈0.008 से ऊपर का हर मान 255 पर समतल हो जाता है और कोई स्केल फ़ाइल का वर्णन नहीं करता है। Chloros जानबूझकर `Chloros:PixelScale` और `MicaSense:RadiometricCalibration` दोनों को छोड़ देता है टपल वहाँ होता है, और लॉग करता है कि क्यों। **यदि LATTICE रिफ्लेक्टेंस फ़ाइल पर टैग अनुपस्थित है, तो स्केल का अनुमान न लगाएं — पिक्सलों को विभाजित करने के बजाय 16-बिट या 32-बिट पर पुनः-निर्यात करें** जो कभी विभाज्य नहीं थे।

#### एक्सपोर्ट तक EXIF का वहन

`process` स्रोत कैप्चर के **GPS ब्लॉक और उसके ExifIFD** की प्रतिलिपि हर उत्पाद पर डालता है, तो एक एक्सपोर्ट `FocalLength`, `FNumber`, `ExposureTime`, `ISO`, को साथ ले जाता है, `DateTimeOriginal` और
`CameraSerialNumber` भू-संदर्भन के साथ।

**फोटोग्रामेट्री के लिए `FocalLength` वैकल्पिक नहीं है।** Pix4D फोकल लंबाई और ऊँचाई के आधार पर ग्राउंड सैंपल दूरी की गणना करता है; टैग के अनुपस्थित होने पर यह एक बहुत ही गलत पैमाने पर वापस चला जाता है। एक 49-कैप्चर ऑरेंज-ग्रोव उड़ान पर, गायब टैग ने 411 मीटर × 160 मीटर के स्थल को एक पुनर्निर्मित में बदल दिया
47.8 किमी × 13 किमी का — जो कि ज्यादातर नोडेटा (nodata) का एक 455 एमपी ऑर्थो था, जिसे बाद में किसी के भी जीएसडी (GSD) की जाँच करने से पहले एक टाइलिंग समस्या और एक बिगटिफ (BigTIFF) समस्या के रूप में पढ़ा गया। यदि आपका ऑर्थो एक अविश्वसनीय
स्केल पर आता है, तो पहले निर्यात किए गए उत्पाद पर `exiftool -FocalLength` चलाएँ।

यह कॉपी जानबूझकर `-all:all` **नहीं** है: IFD0 के स्ट्रक्चरल टैग्स LATTICE आउटपुट को तोड़ देते हैं जब
नकल किए गए, और `ExifImageWidth` / `ExifImageHeight` को बाहर रखा गया है क्योंकि वे का वर्णन करते हैं
*स्रोत* कैप्चर — एक एक्सपोर्ट जिसका कभी भी आकार बदला गया हो, अन्यथा ऐसे आयाम रखेगा जो उसके अपने रास्टर के विरोधी होंगे। XMP को कॉपी करने के बजाय सीधे लिखा जाता है, क्योंकि ExifTool
XMP ब्लॉक को कॉपी करने पर एक ही-इंकेशन XMP टैग को हटा देता है (जिससे MAPIR
कैलिब्रेशन टैग हट जाएंगे).

### आउटपुट कहाँ जाते हैं

उत्पाद **प्रोजेक्ट फ़ोल्डर के अंतर्गत, कैमरे और फिर फ़ाइल प्रारूप के अनुसार समूहीकृत** करके लिखे जाते हैं:

```
<project>/
└── LATT-M3M-L41-F550/                  # one folder per camera model+lens+filter
    ├── tiff16/
    │   ├── Reflectance_Calibrated_Images/
    │   ├── Debayered_Images/
    │   ├── Preview_Images/
    │   └── <INDEX>_Index_Images/        # e.g. NDVI_Index_Images
    └── tiff32/
        └── Radiance_Images/             # float32 radiance always lands here
```

कैमरा फ़ोल्डर LATTICE के लिए `LATT-<sensor>-<lens>-F<filter>` (कैप्चर के EXIF `Model` से मेल खाता है) और Survey3 के लिए `<model>_<filter>` है। — दो कैमरे जो एक सेंसर और फ़िल्टर साझा करते हैं लेकिन लेंस में भिन्नता रखते हैं, वे अलग-अलग ट्री रखते हैं, क्योंकि विनेट, फ़ील्ड ऑफ़ व्यू और विकृति भिन्न होती है। फ़ॉर्मेट फ़ोल्डर `--format` का अनुसरण करता है: `tiff16`, `tiff8`, `png8`, `jpg8`, या `tiff32` के लिए
`TIFF (32-bit, Percent)`.

> **हर निर्यात किया गया उत्पाद SOURCE फ़ाइल का नाम रखता है।** `capture_…_raw.tif` का एक रेडियंस एक्सपोर्ट अभी भी `capture_…_raw.tif` ही कहा जाता है — यह बस
> `tiff32/Radiance_Images/` में रहता है। **फ़ोल्डर उत्पाद की पहचान करता है, फ़ाइल का नाम नहीं**, इसलिए `*radiance*.tif` के लिए ग्लॉबिंग कुछ नहीं ढूंढता; इसके बजाय निर्देशिका पर मिलान करें।

### लाइट-सेंसर रिकॉर्डिंग — कैलिब्रेटेड `.daq` + `.csv`

`process` आपके इनपुट में `.daq` रिकॉर्डिंग को भी संभालता है फ़ोल्डर को संभालता है, और इसे ऐसा करने के लिए किसी इमेजरी की **आवश्यकता नहीं** है: अपने आप उड़ाया गया एक DAQ-U / DAQ-M / DAQ-E एक पूर्ण कैप्चर है, और केवल `.daq` फ़ाइलों वाला एक फ़ोल्डर एक मान्य इनपुट है।

एक DAQ को इसके कैलिब्रेशन **के बिना** रिकॉर्ड किया जा सकता है — यही सार्वजनिक
[`chloros_scripts`](https://github.com/mapircamera/chloros_scripts) रिकॉर्डर करते हैं
(`record_daq.py`) डिफ़ॉल्ट रूप से ऐसा करते हैं: वे कच्चे सेंसर काउंट लिखते हैं और फ़ाइल पर स्टैम्प लगाते हैं ताकि Chloros उस सेंसर का फैक्ट्री कैलिब्रेशन **सीरियल** द्वारा प्राप्त कर सके (पहले लोकल कैश, फिर MAPIR क्लाउड) और उसे लागू कर सके। `process` परिणाम को वापस लिखता है:

```
<project>/
└── Light Sensor/
    ├── <name>_calibrated.daq        # reprocessable archive, declares its bundle
    └── <name>_calibrated.csv        # W/m^2/nm per reading + photometric columns
```

`.csv` प्रत्येक रीडिंग के लिए एक पंक्ति ले जाता है: UTC टाइमस्टैम्प, इंटीग्रेशन समय, कुल शक्ति,
फोटोपिक/स्कोटोपिक लक्स, PPFD (और इसका नीला/हरा/लाल विभाजन), पीक तरंगदैर्घ्य, फिर सेंसर के अपने तरंगदैर्घ्य ग्रिड पर पूरा स्पेक्ट्रम। `.daq` बिना होने के पुनः आयात करता है
दूसरी बार कैलिब्रेट किया जाता है।

सफलता पर रन `Light-sensor products written: N (calibrated .daq + .csv)` रिपोर्ट करता है।
कोष्ठक में दिया गया विवरण वास्तव में लिखी गई चीज़ का वर्णन करता है, इसलिए यह इस प्रकार है
एक बंडल-लेस सेंसर के लिए `(RAW COUNTS — this sensor has no calibration bundle)` और
`(N calibrated, M raw counts)` दोनों को रखने वाले फ़ोल्डर के लिए। बैकएंडके अपने `[DAQ-EXPORT]` और `[RUN-SUMMARY]` शीर्षक अपनी शब्दावली उसी तरह प्राप्त करते हैं — इनमें से कोई भी तीन एक कच्चे निर्यात को कैलिब्रेटेड नहीं कह सकता।

एक DAQ-U / DAQ-M / DAQ-E रिकॉर्डिंग जिसका कैलिब्रेशन बंडल प्राप्त नहीं किया जा सकता — आप ऑफ़लाइन हैं, या उस सेंसर की फ़ाइल में कोई कैलिब्रेशन नहीं है — को एक `[DAQ-EXPORT]` लाइन पर **एक कारण के साथ छोड़ दिया जाता है**, और इसे कभी भी कच्चे काउंट्स रखने वाली &quot;कैलिब्रेटेड&quot; फ़ाइल के रूप में नहीं लिखा जाता है।
इंटरनेट से कनेक्ट करें और फिर से चलाएँ। कारण वह है जो रीडर वास्तव में
उस फ़ाइल के लिए स्थापित नहीं किया जा सका (अनपठनीय स्कीमा, कोई बंडल नहीं, एक लेखन त्रुटि), और रन सारांश **विशिष्ट** कारणों को सूचीबद्ध करता है — बीस फ़ाइलें एक कारण से छोड़ी गईं, एक कारण के रूप में पढ़ी गईं, न कि उसका बीस दोहराव।

#### DAQ-A रिकॉर्डिंग्स का कच्चे काउंट्स के रूप में निर्यात

**DAQ-A**परिवार per से पहले का है-सीरियल बंडल सिस्टम से पहले का है और इसमें फ़ेच करने के लिए कोई कैलिब्रेशन बंडल नहीं है — इसके बजाय इसे फ़ील्ड में एक रिफ्लेक्टेंस टारगेट के खिलाफ कैलिब्रेट किया जाता है, और यही कारण है कि इसे कभी इसकी ज़रूरत नहीं पड़ी। उन रिकॉर्डिंग को अस्वीकार करने से उनके पास अपने नंबर निकालने का कोई तरीका नहीं बचा, इसलिए वे एक**अलग नाम** के तहत एक्सपोर्ट करते हैं:

```
<project>/
└── Light Sensor/
    ├── <name>_raw.daq        # NOT _calibrated
    └── <name>_raw.csv        # raw spectral sensor counts, NOT irradiance
```

फ़ाइल के अंदर एक फ़्लैग के बजाय एक अलग फ़ाइलनाम, क्योंकि यह दावा ईमेल किए जाने पर भी एक सादे नाम के रूप में बरकरार रहना चाहिए। `.csv` हेडर में लिखा है
`raw spectral sensor counts (NOT irradiance)` और चेतावनी देता है कि मान फ़ाइल के **अंदर**तुलनीय हैं — जो ठीक वही है जिसके लिए लक्ष्य-आधारित कैलिब्रेशन उनका उपयोग करता है — और सेंसरों के बीच नहीं। पावर-निर्भर फोटोमेट्रिक कॉलम (कुल पावर, फोटोपिक और स्कोटॉपिक लक्स, PPFD) लिखे जाते हैं**NULL** काउंट्स से एकीकृत होने के बजाय, और रन सारांश `RAW COUNTS` कहता है, इसलिए एक लॉग में &quot;निर्यात&quot; किया गया इसे विकिरण के रूप में नहीं पढ़ा जा सकता।

लेगेसी **v1.01 / v1.02** रिकॉर्डिंग्स (जिन्हें DAQ-A-SD लिखता है) में प्रति-रीडिंग एपोक नहीं होता, केवल फ़ाइल का लिखने का समय होता है। इमेज↔डाउनवेलिंग मैचर अभी भी उन्हें अस्वीकार करता है — एक फ़्रेम का लिखने के समय से मिलान करना अदृश्य रूप से गलत होगा — लेकिन एक्सपोर्टर उन्हें पढ़ता है, और
CSV `clock=daq_created_on` को प्रिंट करता है ताकि उत्पाद यह बता सके कि यह किस घड़ी पर है।

### नोट्स

- `process` स्वचालित रूप से पता लगाता है कि आपका फ़ोल्डर Survey3, LATTICE, या मिश्रित है।
- सर्वर-सेंट इवेंट्स पर प्रगति स्ट्रीम; CLI प्रत्येक थ्रेड की लाइव प्रगति (डिटेक्टिंग, एनालाइज़िंग, प्रोसेसिंग, एक्सपोर्टिंग) दिखाता है।
- के लिए Linux /जेत्सन पर, CLI स्वैप की जाँच करता है और बड़े फ़ोल्डरों को प्रोसेस करने से पहले चेतावनी दे सकता है। टेक्सचर-अवेयर डेबायर कम-पॉवर वाले जेत्सन (नैनो, ओरिन नैनो) पर स्वचालित रूप से एक GPU फ़्रीक्वेंसी कैप भी लागू करता है।
- सफलता पर रन यह रिपोर्ट करता है कि उसने कितने इमेज उत्पाद लिखे (`Image products written: N`)।

#### कोई छवि न लिखने वाला रन विफल हो जाता है

यदि आपने उत्पादों के लिए अनुरोध किया है और रन ने * लिखा है*कोई नहीं** — केवल `project.json` और
`calibration_data.json` — `process` इसे एक विफलता के रूप में मानता है: यह प्रिंट करता है
`Processing finished but wrote no image products.` और **गैर-शून्य पर समाप्त** करता है, ताकि एक स्क्रिप्ट कर सके
इसे पहचान सकता है। संदेश में प्रोजेक्ट फ़ोल्डर और सामान्य कारण बताए जाते हैं:

- इनपुट फ़ोल्डर को कैप्चर के रूप में पहचाना नहीं गया था (लेआउट और `--input-level` की जाँच करें), या
- उन कैमरों के लिए अनुपयुक्त होने के कारण अनुरोधित प्रत्येक उत्पाद को छोड़ दिया गया था (उदा. का अनुरोध करना
  RGB-केवल कैमरों से रेडियंस/रिफ्लेक्टेंस)।

`--verbose` के साथ फिर से चलाएँ और `[LATTICE-EXPORT]` / `[EXPORT-CHECK]` लाइनों के लिए बैकएंड लॉग की जाँच करें,
जो उन कैमरों के लिए अप्रासंगिक होने के कारण छोड़े गए चरणों को समझाता है, जो अन्यथा CLI के आउटपुट तक नहीं पहुँचते।

एक जानबूझकर किया गया केवल-मेटाडेटा रन — सभी उत्पाद टॉगल बंद और कोई `--indices` नहीं — फिर भी एक **सफलता** है, क्योंकि वहाँ एक खाली इमेज आउटपुट सही परिणाम है।

इसी तरह एक **लाइट-सेंसर-केवल रन**भी**सफल** है: `.daq` रिकॉर्डिंग वाले फ़ोल्डर में एक्सपोर्ट करने के लिए कोई इमेजरी नहीं है
परिभाषा के अनुसार, और रन को इसके लिखे गए कैलिब्रेटेड `.daq` / `.csv` के आधार पर आंका जाता है।

---

## `chloros-cli login`

इस मशीन को एक Chloros+ क्लाउड खाते से प्रमाणीकृत करें। क्रेडेंशियल `~/.chloros/user_session.json` में सुरक्षित रूप से कैश किए जाते हैं।

```
chloros-cli login EMAIL PASSWORD
```

### उदाहरण

```bash
chloros-cli login user@example.com 'YourPassword'

# Passwords containing $ should use SINGLE quotes
chloros-cli login user@example.com 'my$ecret$pass'
```

> **PowerShell `$$` mangling is auto-corrected.** In double quotes PowerShell expands `$$` (पासवर्ड से इसे हटाने, या उसके हिस्सों की नकल करने). 401 पर, पासवर्ड के साथ `$$` को स्वचालित रूप से फिर से जोड़ा जाता है, फिर पासवर्ड के एक आधे हिस्से के साथ पुनः प्रयास किया जाता है; यदि पुनः प्रयास सफल हो जाता है तो यह आपको लॉग इन करता है और अगली बार उपयोग करने के लिए सही सिंगल-क्वोट सिंटैक्स प्रिंट करता है।

> *हेडलेस/स्क्रिप्टेड उपयोग: कोई कैश्ड सत्र नहीं होने का मतलब है एक इंटरैक्टिव प्रॉम्प्ट, न कि तेज़ असफलता।** कोई भी बैकएंड-स्पॉन करने वाला कमांड (`process`, `status`, `export-status`, `time-sync`, …) बिना कैश्ड लाइसेंस/सेशन के चलाने पर, आगे बढ़ने से पहले stdin पर एक इंटरैक्टिव `Email:` / `Password:` प्रॉम्प्ट पर आ जाता है। इसलिए, बिना कैश्ड सेशन वाली कोई अनअटेंडेड जॉब इनपुट का इंतज़ार करते हुए हैंग हो जाएगी — `chloros-cli login EMAIL PASSWORD` चलाएँहेडलेस कार्य शेड्यूल करने से पहले प्रति मशीन एक बार  चलाएँ।

---

## `chloros-cli logout`

कैश्ड सत्र को साफ़ करता है और अगली कॉल पर एक नए लॉगिन को मजबूर करता है।

```bash
chloros-cli logout
```

---

## `chloros-cli status`

वर्तमान लाइसेंस स्तर (आयरन/कॉपर/ब्रांज/सिल्वर/गोल्ड) दिखाएँ, प्रमाणित उपयोगकर्ता, और डिवाइस-बाइंडिंग की संख्या।

```bash
chloros-cli status
```

---

## `chloros-cli export-status`

लाइव थ्रेड-4 निर्यात प्रगति की जानकारी प्राप्त करें। किसी दूसरे शेल से `process` रन के **दौरान** कॉल करना सुरक्षित है।

```bash
chloros-cli export-status
```

---

## `chloros-cli language`

CLI की डिस्प्ले भाषा सेट करें (38 समर्थित, जिसमें CJK, RTL, और इंडिक शामिल हैं)। जो लेगेसी कंसोल स्क्रिप्ट को रेंडर नहीं कर सकते, उन पर यह स्वचालित रूप से अंग्रेजी पर वापस आ जाता है।

```
chloros-cli language [LANG_CODE] [--list]
```

### उदाहरण

```bash
# List all available languages
chloros-cli language --list

# Switch to Spanish
chloros-cli language es

# Show the currently-active language
chloros-cli language
```

---

## प्रोजेक्ट फ़ोल्डर कमांड

ये डिफ़ॉल्ट प्रोजेक्ट फ़ोल्डर स्थान को प्रबंधित करते हैं (GUI के साथ साझा किया गया)।

```bash
chloros-cli set-project-folder "/home/user/Chloros Projects"
chloros-cli get-project-folder
chloros-cli reset-project-folder
```

---

## `chloros-cli update` केवल

Linux/ Jetson पर। `/etc/chloros/update.conf` से `version_url` की जाँच करता है और मिलान करने वाले `.deb` को डाउनलोड + इंस्टॉल करने का प्रस्ताव देता है।

```bash
chloros-cli update            # check + install
chloros-cli update --check    # check only
```

Linux/Jetson पर CLI भी एक **चलाता हैहर स्टार्टअप पर स्वचालित अपडेट की जाँच** (नॉन-ब्लॉकिंग, कमांड में कभी देरी नहीं करता): यह `/etc/chloros/update.conf` को पढ़ता है, परिणाम को 1 घंटे के लिए `~/.chloros/update_cache.json` में कैश करता है, और `Update available: vX.Y.Z / Run: chloros-cli update` प्रिंट करता हैX जब कोई नया संस्करण मौजूद हो। किसी भी त्रुटि पर और Windows पर चुपचाप छोड़ दिया जाता है।

---

## `chloros-cli selftest`

एक 7-चरणीय स्मोक टेस्ट चलाता है: संस्करण, पोर्ट की उपलब्धता, बैकएंड स्टार्टअप, `/api/test`, `/api/system-info` (GPU/CUDA/PyTorch), डिसनोइज़र मॉडल की उपस्थिति, CUDA+डिसनोइज़र की तत्परता।

```bash
chloros-cli selftest
```

---

## `chloros-cli time-sync`

PTP ग्रैंडमास्टर स्थिति और नियंत्रण। Chloros होस्ट PTP ग्रैंडमास्टर चलाता है; LATTICE कैम और DAQ-E इकाइयाँ क्रॉस-डिवाइस टाइमस्टैम्प के लिए इसके स्लेव के रूप में काम करती हैं।

| उप-कमांड | विवरण |
| --- | --- |
| `status` | ग्रैंडमास्टर की स्थिति, BMCA प्राथमिकताएँ, क्लॉक पहचान दिखाएँ। |
| `peers` | Delay_Req के माध्यम से देखे गए स्लेव्स की सूची दें (कैमरे + DAQ-E सेंसर). |
| `cameras` | प्रति-कैमरा PTP स्वास्थ्य (`PtpStatus`, `PtpOffsetFromMaster`, `PtpMeanPathDelay`). |
| `restart` | ग्रैंडमास्टर प्रक्रिया को पुनः आरंभ करें। |
| `set-priority --priority1 N --priority2 N` | बीएमसीए प्राथमिकताओं को ओवरराइड करें। |

### उदाहरण

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
chloros-cli time-sync cameras
chloros-cli time-sync restart
chloros-cli time-sync set-priority --priority1 1 --priority2 1
```

---

## `chloros-cli lattice`

लैटिस कैमरा नियंत्रण। हर सबकमांड &#x27;Chloros&#x27; बैकएंड के माध्यम से राउट होता है; बैकएंड कैम पूल का मालिक है, इसलिए बाद की &#x27;CLI&#x27; कॉल्स उसी खुले हैंडल का पुन: उपयोग करती हैं।

### सामान्य विकल्प (अधिकांश उप-कमांडों द्वारा साझा)

| फ़्लैग | विवरण |
| --- | --- |
| `-d, --device N` | कैमरा अनुक्रमणिका (डिफ़ॉल्ट: 0)। |
| `-s, --serial SN` | विशिष्ट सीरियल; `--device` को ओवरराइड करता है। |
| `--serials SN1,SN2,…` | मल्टी के लिए अल्पविराम-से-अलग किए गए सीरियल-कैम संचालन। |
| `--all` | प्रत्येक खोजे गए कैमरे पर संचालित करें। |
| `--exposure US` | माइक्रोसेकंड में एक्सपोज़र समय। |
| `--gain DB` | गेन डेसीबल (dB) में। |
| `--pixel-format FMT` | उदाहरण के लिए `BayerRG8`, `BayerRG12`। |
| `--width N` / `--height N` | छवि का आकार। |
| `--preset {default,high_quality,high_speed,triggered}` | सेटिंग्स प्रीसेट लागू करें। `triggered` को छोड़कर सभी फ्री-रन, जो लाइन 2 पर हार्डवेयर एज के लिए कैमरा तैयार करता है — यदि उस लाइन पर कुछ भी ड्राइव नहीं कर रहा है तो यह कैप्चर करने के बजाय हमेशा के लिए इंतजार करेगा। |
| `-o, --output DIR` | आउटपुट डायरेक्टरी (डिफ़ॉल्ट: `output`)। |
| `--packet-size {auto,jumbo,standard,N}` | GVSP पैकेट आकार। `auto` ICMP+GVSP प्रोब चलाता है; `jumbo` = 9000; `standard` = 1500. |

### लैटिस कैमरा प्रथम-कनेक्ट वर्कफ़्लो

```bash
# 1. Discover cameras on the network
chloros-cli lattice info

# 2. Single-cam smoke test: capture one frame.
#    By default this saves EVERY export type applicable to the cam
#    (raw, debayered, radiance, reflectance, preview). Pass e.g.
#    `--processing debayered` to save just one.
chloros-cli lattice capture -o output/

# 3. Connect a synchronized array (RECOMMENDED ENTRY POINT for arrays).
#    This is the same "smart-prep" flow the Chloros GUI uses:
#      - Network capability probe (ICMP DF ping + GVSP probe)
#      - Tier auto-pick (sim-emit / ftd-stagger / slip)
#      - Auto-shrink frame size to fit the wire
#      - PTP enabled by default
#      - Per-cam pixel format auto-pick
#      - AE seeding from the cam's saved state
#      - GPIO trigger config on Line2
chloros-cli lattice array-connect \
  --serials 213800234,214000533,214701288,214701292

# 4. Capture one synced frame group from the live array.
#    Defaults to --processing all (one file per export type per cam);
#    pass a single level to narrow it, e.g. --processing reflectance.
chloros-cli lattice array-capture --processing reflectance -o output/

# 5. Live-preview one cam in your browser
chloros-cli lattice viewer --serial 213800234

# 6. Tear down when done
chloros-cli lattice array-disconnect
```

### उप-कमांड संदर्भ

#### डिस्कवरी और जानकारी

| उप-कमांड | उद्देश्य |
| --- | --- |
| `lattice info` | जुड़े कैमरों की सूची (वेंडर, मॉडल, सीरियल, आईपी, MAC). |
| `lattice probe [--pixel-format FMT] [--json] [--no-discover]` | सर्वोत्तम कैमरा कॉन्फ़िगरेशन के लिए होस्ट सिस्टम का विश्लेषण करें। `--no-discover` कैमरा डिस्कवरी छोड़ता है (तेज़, केवल NIC विश्लेषण)। |
| `lattice network [--fix] [--estimate] [--cameras N]` | जाँच/NIC सेटिंग्स ठीक करें; बैंडविड्थ/FPS का अनुमान लगाएँ। |
| `lattice network-analysis --master SN --slaves SN1,SN2,… [--width N] [--height N] [--pixel-format FMT] [--binning N] [--force-tier TIER] [--backend-url URL] [--json]` | स्थिर-स्कीमा बैकएंड नेटवर्क-क्षमता + एरे सिफारिश (`status` ∈ `ok` / `auto_shrunk` / `auto_capped_fps` / `needs_force_slip` / `error`). `auto_capped_fps` अनुरोधित रिज़ॉल्यूशन को बनाए रखता है लेकिन लक्ष्य fps को सीमित करता है — `recommended.recommended_target_fps` पढ़ें और इसे कनेक्ट लक्ष्य के रूप में पास करें; इसे त्रुटि न मानकर सफलता मानें। |
| `lattice analyze-array [--models M1,M2,…] [--binning N] [--n-active N] [--width N] [--height N] [--pixel-format FMT] [--force-tier TIER] [--json]` | कैमरों को खोले बिना व्हाट-इफ़ विश्लेषण। **`--n-active` वायर पर कैमरों की कुल संख्या है, न कि केवल इस एरे की**— इसे तब बढ़ाएँ जब स्टैंडअलोन कैमरे एक साथ स्ट्रीम करते हैं, या वायर बजट की गणना के खिलाफ की जाती है एक मांग जो उन्हें कम गिनती है (डिफ़ॉल्ट: `len(--models)`)। हमेशा समेकित `Wire budget:` प्रिंट करता है (मांगी गई MB/s बनाम टकराव-सुरक्षित सीमा) और `Max cameras:` लाइनें, और जब एरे वायर पर ओवर-सब्सक्राइब करता है तो `** OVER-SUBSCRIBED**` को फ्लैग करता है — देखें [एरे fps &amp; बर्स्ट मॉडल](#array-fps--burst-model). |
| `lattice gpu` | GPU स्थिति दिखाएँ। |
| `lattice firmware [--update] [--force] [-y\|--yes]` | कैमरा फर्मवेयर की जाँच करें या अपडेट करें। स्थानीय `.fwa` चयन पिन किया गया है:  में फ़ाइल364 बिल्ड के `MIN_FIRMWARE_VERSION` से मेल खाता है, जब मौजूद हो तो फ्लैश किया जाता है (केवल सबसे ऊँचे संस्करण को फॉलबैक के रूप में), इसलिए डिस्क पर रखी गई एक नई विक्रेता इमेज तब तक निष्क्रिय रहती है जब तक कि उस पिन को बम्प नहीं किया जाता — जानबूझकर नए रिलीज़ हस्ताक्षरित AWS मैनिफेस्ट के माध्यम से यूनिट्स तक पहुँचते हैं, जो नए होने पर प्राथमिकता दी जाती है। |
| `lattice presets [--apply NAME]` | कैमरा प्रीसेट सूचीबद्ध करें या लागू करें। |
| `lattice status` | लाइव कैमरा स्थिति। |

#### कैप्चर

| उप-कमांड | उद्देश्य |
| --- | --- |
| `lattice capture [--format tiff\|png\|jpg] [--jpeg-quality N] [--processing LEVEL] [--levels L1,L2,…] [--force-daq]` | एकल फ्रेम। **डिफ़ॉल्ट रूप से हर एक्सपोर्ट प्रकार को सहेजता है** (`--processing all`); देखें [कैप्चर एक्सपोर्ट लेवल](#capture-export-levels-the-all-default). `--levels` एक विशिष्ट उपसमूह सहेजता है (`--processing` को ओवरराइड करता है); `--force-daq` निर्दिष्ट DAQ रीडिंग को एक  के रूप में लिखता है000373 साइडकार, कच्ची-केवल ग्रैब पर भी। `--jpeg-quality` = JPEG गुणवत्ता 1–100 (डिफ़ॉल्ट 95). |
| `lattice continuous [--format tiff\|png\|jpg] [--jpeg-quality N] [--queue-depth N]` | Ctrl+C तक डिस्क पर स्ट्रीम करें। |
| `lattice viewer [--brightness N] [--ae-damping F] [--frame-rate FPS]` | ब्राउज़र-आधारित लाइव MJPEG पूर्वावलोकन। `--ae-damping` ऑटो-एक्सपोजर डैंपिंग (0.4–100) सेट करता है। |

#### सेंसर ट्यूनिंग

| उप-कमांड | उद्देश्य |
| --- | --- |
| `lattice configure [--get N1 N2…] [--set N=V N=V…] [--dump] [--json]` | किसी भी GenICam नोड को पढ़ें/लिखें। |
| `lattice exposure [--auto] [--auto-once] [--off] [--set US] [--brightness N] [--damping F] [--upper-limit US]` | एक्सपोज़र और AE। |
| `lattice gain [--auto] [--off] [--set DB]` | गेन और ऑटो-गेन। |
| `lattice resolution [--set WxH] [--offset X,Y] [--binning N] [--binning-mode Sum\|Average]` | सेंसर ROI और बिनिंग। |
| `lattice format [--set FMT] [--list]` | पिक्सेल प्रारूप। |
| `lattice trigger [--mode On\|Off] [--source SRC] [--delay-us US] [--activation EDGE] [--list-sources] [--software]` | हार्डवेयर/सॉफ्टवेयर ट्रिगर। |
| `lattice white-balance [--auto] [--off] [--red R] [--blue B]` (कोई फ़्लैग नहीं = वन-शॉट WB) | WB संचालन। RGB /केवल बायर कैम पर; मोनो M3M पर एक नो-ऑप (छोड़ दिया गया)। |
| `lattice color-profile [--set raw\|linear\|natural\|enhanced\|custom_temp] [--cct K] [--get]` | RGB डिस्प्ले रंग पाइपलाइन। `natural` (डिफ़ॉल्ट) सस्ता लाइव फ़िनिश है; `enhanced` डिफ़्रिंज + वाइब्रेंस + CLAHE स्थानीय कंट्रास्ट जोड़ता है, जिससे पूर्ण हब-पैरिटी लुक मिलता है, लगभग 2× प्रति-फ़्रेम फ़िनिश लागत पर, इसलिए एक कम **लाइव** फ्रेमरेट — सेव की गई कैप्चर को हर हाल में पूरा फिनिश मिलता है। केवल RGB/Bayer कैम्स के लिए; मोनो M3M पर स्किप किया गया। |
| `lattice color [--saturation N] [--contrast N] [--reset] [--get]` | डिस्प्ले सैचुरेशन/कंट्रास्ट (RGB फ़िल्टर कैम्स)। मोनो M3M पर छोड़ा गया। |
| `lattice filter [--set NAME] [--list]` | कैम का फ़िल्टर मॉडल सेट करें (`RGN-IMX265`, `OCN`, `NGB`, …). |
| `lattice power [--sleep]` | प्रोब पावर/थर्मल नोड्स; लो-पावर आइडल टॉगल करें। |

#### कैलिब्रेशन और सेंसर

| उप-कमांड | उद्देश्य |
| --- | --- |
| `lattice calibrate [--filter NAME] [--attempts N] [--save PATH]` | परावर्तन लक्ष्य से कैलिब्रेट करें। |
| `lattice dls [--connect] [--spectrum] [--irradiance] [--mac MAC] [--filter NAME] [--json]` | निर्मित-डाउनवेलिंग-लाइट-सेंसर कमांड्स में। |
| `lattice vignette --input DIR --output DIR [--lens-model KEY]` | मौजूदा छवियों पर विनेट सुधार लागू करें। |

#### मल्टी-कैमरा (अस्थायी सत्र)

| उप-कमांड | उद्देश्य |
| --- | --- |
| `lattice multi-info` | सिंक भूमिकाओं वाले सभी कैमरों की सूची दें। |
| `lattice multi-capture [--format FMT] [--jpeg-quality N] [--processing LEVEL]` | प्रत्येक कैम से एक सिंक किया गया फ्रेम। जब एक स्थायी एरे (persistent array) जुड़ा होता है तो **सभी एक्सपोर्ट प्रकार डिफ़ॉल्ट रूप से सहेजे जाते हैं**; अस्थायी नो-एरे (transient no-array) फ़ॉलबैक केवल**डेबेयर्ड** होता है (बाकी के लिए पहले `array-connect` चलाएँ)। |
| `lattice multi-stream [--fps F] [--count N] [--format FMT] [--jpeg-quality N]` | सिंक किए गए फ़्रेम स्ट्रीम करें (ट्रांज़िएंट)। |
| `lattice multi-test [--count N]` | GPIO सिंक टाइमिंग परीक्षण। |
| `lattice multi-detect [--line LINE] [--json]` | GPIO मास्टर/स्लेव वायरिंग का स्वतः पता लगाएँ। |

#### संरेखण

| उप-कमांड | उद्देश्य |
| --- | --- |
| `lattice align-calibrate [--method orb\|akaze\|phase\|checkerboard\|manual] [--model translation\|rigid\|affine\|homography] [--frames N] [--checkerboard RxC] [--points PATH] [--reference SN] [--save PATH] [--preview] [--vignette] [--prefilter none\|gradient\|clahe\|blur\|hist_match] [--rms-threshold-px N]` — प्लस डिटेक्टर/मैचर नॉब्स `[--max-features N] [--ratio-threshold F] [--matcher bf\|flann] [--knn-k N]`, RANSAC नॉब्स `[--ransac-threshold-px F] [--ransac-iters N] [--ransac-confidence F]`, मल्टी-फ्रेम कॉम्बिनेशन `[--averaging mean\|median\|inlier_weighted]`, ज्यामितीय प्रतिबंध `[--lock-rotation] [--lock-scale] [--lock-axis x\|y]`, स्थानिक प्रतिबंध `[--roi X0,Y0,X1,Y1] [--mask PATH]`, और प्रति-स्लेव ओवरराइड `[--per-cam-override SN:KEY=VALUE]` (दोहराने योग्य) | लाइव कैमरों से संरेखण प्रोफ़ाइल की गणना करें। `--prefilter` डिफ़ॉल्ट रूप से `gradient` (एज मैप; GUI/एरे अलाइनर से मेल खाता है — स्पेक्ट्रल बैंड्स में एज बनी रहती हैं) होता है। `--matcher flann` ~5000 फ़ीचर्स से ऊपर लाभदायक होता है; `--averaging median` एक खराब कैप्चर के प्रति प्रतिरोधी है, `inlier_weighted` मिलान की संख्या के अनुसार भार देता है; `--lock-scale` निकटतम घूर्णन की ओर प्रक्षेपित करता है (कोई स्केल नहीं), `--lock-axis` एक ट्रांसलेशन घटक को शून्य कर देता है; `--mask` हर कैमरे पर लागू होता है (प्रति-कैम सेटिंग्स के लिए `--per-cam-override` का उपयोग करें, जैसे `--per-cam-override 214701292:method=phase`). `--rms-threshold-px` उस कैलिब्रेशन को सहेजने से इनकार करता है जिसकी रिप्रोज़ेक्शन RMS गेट से अधिक हो। |
| `lattice align-apply --profile PATH [--format tiff\|png] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-camera] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode constant\|replicate\|reflect\|wrap] [--border-value N]` | एक संरेखित मल्टी-बैंड फ्रेम कैप्चर करें। `--bit-depth` डिफ़ॉल्ट रूप से कैमरा से मेल खाता है; `--no-crop` पूरे फ्रेम को रखता है (काले से भरें); `--interpolation` (डिफ़ॉल्ट `linear`) और `--border-mode`/`--border-value` (डिफ़ॉल्ट `constant`/0) CPU वॉर्प को नियंत्रित करते हैं — GPU पथ हमेशा बाइलिनियर रहता है। |
| `lattice align-stream --profile PATH [--fps F] [--count N] [--bit-depth 8\|12\|16] [--bands NAMES] [--order NAMES] [--gpu\|--no-gpu] [--no-crop] [--per-band] [--vignette] [--interpolation nearest\|linear\|cubic\|lanczos] [--border-mode MODE] [--border-value N]` | स्ट्रीम संरेखित मल्टी-बैंड फ़्रेम (`align-apply` के समान वॉर्प नॉब्स)। |
| `lattice align-info --profile PATH [--json]` | प्रोफ़ाइल विवरण प्रदर्शित करें। |
| `lattice align-reorder --profile PATH [--order NAMES] [--enable SERIALS] [--disable SERIALS]` | परत क्रम बदलें। |

#### सूचकांक / वनस्पति गणित

```bash
# Offline: compute NDVI from an aligned multi-band TIFF
chloros-cli lattice index --input aligned.tif --preset NDVI \
  --output ndvi.tif --colorize --gradient RdYlGn

# Live: discover array, calibrate alignment, capture, compute index, in one go
chloros-cli lattice index --live --profile align.json --preset NDVI \
  --save-multiband -o output/
```

पूर्ण फ़्लैग सेट: `--input PATH | --live --profile PATH`, `--preset NAME` (NDVI / NDRE / EVI / SAVI / GNDVI /…), `--formula EXPR`, `--channel SYM=BAND` (दोहराया जा सकता है), `--capture-level raw|debayered|radiance|reflectance|unknown` (स्रोत में रिकॉर्ड किए गए कैप्चर स्तर को ओवरराइड करें TIFF; डिफ़ॉल्ट: TIFF मेटाडेटा से पढ़ें), `--output PATH`, `--output-format all|raw|tif|colorized|lut|png`, `--gradient NAME|JSON`, `--vmin/--vmax/--percentile LO,HI`, `--bg-mode clip|transparent|indexColor|backgroundColor`, `--colorize`, `--list-presets`, `--list-gradients`। `--live` के साथ संरेखण वार्प नॉब्स भी लागू होते हैं: `--save-multiband`, `--gpu/--no-gpu`, `--no-crop`, `--bit-depth 8|12|16`, `--vignette`, `--interpolation nearest|linear|cubic|lanczos`, `--border-mode constant|replicate|reflect|wrap`, `--border-value N`.

> **`--channel` प्रतीक केस-संवेदी होते हैं।** सymbol side को preset के channel names से बिल्कुल मेल खाना चाहिए (presets lowercase का उपयोग करते हैं, जैसे NDVI = `red`,`nir` — `--list-presets` की जाँच करें), और बैंड साइड को संरेखित स्टैक में एक बैंड नाम से मेल खाना चाहिए (या ऑफ़लाइन मोड में 0-आधारित बैंड इंडेक्स होना चाहिए)। `--channel red=Red_660 --channel nir=NIR_850` काम करता है; `--channel RED=660` एक `channel_map missing entries` त्रुटि के साथ विफल हो जाता है।

#### स्थायी कनेक्शन (स्मार्ट-प्रैप, GUI-समतुल्य फ्लो)

ये कमांड्स CLI इनवोकेशन के पार बैकएंड पूल में कैमरों को खुला रखते हैं।

| सबकमांड | उद्देश्य |
| --- | --- |
| `lattice cam-connect [--serial SN]` | पूल में एक कैम जोड़ें (एकल-कैम, कोई एरे नहीं)। |
| `lattice cam-disconnect [--serial SN] [--all]` | रिलीज़ करें। |
| `lattice cam-list` | पूल में कैम्स की सूची प्राप्त करें। |
| **`lattice array-connect`**|**कनेक्ट करें एक स्थायी सिंक्रनाइज़्ड एरे (अनुशंसित एंट्री पॉइंट)।** पूर्ण GUI स्मार्ट-प्रैप फ्लो चलाता है। |
| `lattice array-disconnect [--array-id ID] [--all]` | एक एरे रिलीज़ करें। |
| `lattice array-list` | कनेक्टेड एरे की सूची दें। |
| `lattice array-status [--array-id ID]` | लाइव fps, PTP, पिछली त्रुटि। |
| `lattice array-capture [--processing LEVEL\|all] [--levels L1,L2,…] [--aligned\|--no-aligned] [--index\|--no-index] [--force-daq] [--smart] [--fastest] [--compression deflate\|none] [--continuous\|--interval S] [--count N] [--duration S]` | लाइव एरे से एक सिंक किया गया कैप्चर — सिंगल / निरंतर / अंतराल / सबसे तेज़। **डिफ़ॉल्ट `all` है** (प्रति कैम प्रति लागू एक्सपोर्ट प्रकार एक फ़ाइल)। छोड़े गए कैम (जैसे कि RGB, रेडियंस/रिफ्लेक्टेंस से बाहर रखा गया) `Skipped: SN:<serial> (<reason>)` के साथ रिपोर्ट किया जाता है; रिफ्लेक्टेंस के लिए उपयोग की गई DAQ रीडिंग को साथ में सहेजा जाता है और `DAQ: <path>` के साथ रिपोर्ट किया जाता है। देखें [कैप्चर मोड, रिकॉर्डर और ऑफ़लाइन रीप्रोसेस](#capture-modes-recorders--offline-reprocess). |
| `lattice array-record [--fps F] [--duration S] [--gif] [--gif-only]` | लाइव संयुक्त-सूचकांक दृश्य को वीडियो/GIF में रिकॉर्ड करें (निगरानी-ग्रेड; संयुक्त स्ट्रीम खुली होने की आवश्यकता है)। |
| `lattice array-burst [--duration S] [--max-frames N] [--build] [--products …]` | उच्च-fps कच्चा-बायर बर्स्ट (विश्लेषण-ग्रेड; ऑफ़लाइन पुनःप्रसंस्करण)। |
| `lattice array-build-video --burst-dir DIR [--products …] [--fps F] [--save-tiffs] [--gif]` | एक सहेजे गए कच्चे बर्स्ट को कैलिब्रेटेड वीडियो(ओं) में पुनःप्रसंस्करण करें। |

##### `array-connect` विकल्प

| फ़्लैग | डिफ़ॉल्ट | विवरण |
| --- | --- | --- |
| `--serials SN1,SN2,…` | सभी LATTICE कैम को स्वतः-खोजें (कम से कम 2 की आवश्यकता) | पहला सीरियल मास्टर है। जब इसे छोड़ दिया जाता है, तो खोज LATTICE (`TRI032*`) मॉडलों तक सीमित हो जाती है और उन सभी को जोड़ देती है। |
| `--line {Line0,Line2,Line3}` | `Line2` | GPIO सिंक लाइन। |
| `--target-fps F` | ऑटो | मास्टर ट्रिगर फायर दर। |
| `--force-tier {sim-capture-sim-emit, sim-capture-ftd-stagger, slip-emit-and-capture}` | ऑटो | टियर पिकर को ओवरराइड करें। |
| `--wire-ceiling-mbps MB_PER_S` | ऑटो-डिटेक्टेड | **होस्ट का निरंतर वायर बजट, एमबी/से में — वह संख्या जिस पर पूरी एरे आवंटन निर्भर करता है।** जब एरे GVSP-करप्ट फ्रेम रिपोर्ट करता है तो इसे कम करें: ऑटो मान NIC की विज्ञापित लिंक दर से लिया जाता है, जो USB एडाप्टर, पतले PCIe लेन और व्यस्त साझा फैब्रिक को अधिक बताता है। प्रोजेक्ट के एरे कैप्चर ब्लॉक में संग्रहीत होता है, इसलिए एक पुनः खोलना / CLI / SDK पुनः कनेक्ट करने पर यह बहाल हो जाता है। [Array health](#array-health--which-subsystem-is-losing-frames) देखें। |
| `--binning {1,2,4}` | ऑटो | हार्डवेयर बिनिंग। |
| `--no-recommend` | बंद | नेटवर्क-विश्लेषण चरण को छोड़ें। |
| `--no-ptp` | बंद | PTP अक्षम करें (क्रॉस-कैम टाइमस्टैम्प तब **तुलनीय** नहीं होते)। |

### स्मार्ट-AE / स्मार्ट-कैप्चर

LATTICE ऐरे कनेक्ट होते ही बैकग्राउंड में निरंतर AE चलाते हैं, लेकिन एक ताज़ा-पॉइंट किए गए सीन को अभिसरण (converge) करने में थोड़ा समय लगता है। `array-capture --smart` **पैकेज्ड सुविधा**: यह एरे में हर कैम पर AE के स्थिर होने का इंतज़ार करता है, फिर कैप्चर ट्रिगर करता है। इसका उपयोग तब करें जब आप सेशन के बीच में सीन बदलते हैं।

```bash
# Connect once, then take settled captures whenever you re-point the rig
chloros-cli lattice array-connect --serials SN1,SN2,SN3,SN4
chloros-cli lattice array-capture --smart --processing reflectance -o pose_a/
# (move the rig)
chloros-cli lattice array-capture --smart --processing reflectance -o pose_b/
```

सेटल पॉलिसी डिफ़ॉल्ट रूप से रूढ़िवादी होती है: 5 s टाइमआउट, 1.5 s स्थिरता विंडो, ±5 % एक्सपोज़र स्प्रेड सहनशीलता। यदि आपको ऑटोमेशन से अलग व्यवहार की आवश्यकता है, तो इसे SDK (`ArrayHandle.capture_smart(settle_timeout_s=…, stability_window_s=…, exposure_tolerance_pct=…)`) के माध्यम से ट्यून करें।

### कैप्चर एक्सपोर्ट लेवल (`all` डिफ़ॉल्ट)

इस रिलीज़ के अनुसार, `lattice capture`, `lattice multi-capture`, और `lattice array-capture` **डिफ़ॉल्ट `--processing all`** — प्रत्येक कैमरा पर लागू होने वाले प्रत्येक एक्सपोर्ट प्रकार के लिए एक सहेजी गई फ़ाइल, जो GUI के &quot;कैप्चर ऑल&quot; व्यवहार से मेल खाती है। स्तर इस प्रकार हैं:

| स्तर | आउटपुट | किस पर लागू होता है |
| --- | --- | --- |
| `raw` | सिंगल-चैनल बायर (मोनो कैम्स: सिंगल बैंड) सीधे सेंसर से। | सभी कैम्स। |
| `debayered` | 3-चैनल BGR डेमोसाइक (मोनो कैम: 1-चैनल ग्रेस्केल)। | सभी कैम। |
| `radiance` | पूर्ण रेडियोमेट्रिक श्रृंखला के माध्यम से float32 W/m²/sr/nm। | केवल मल्टीस्पेक्ट्रल (M3C/M3M) — **RGB-फ़िल्टर कैम के लिए छोड़ दिया गया**। |
| `reflectance` | uint16 ρ (`32768` = 1.0), Pix4D-तैयार। | केवल बहु-स्पेक्ट्रल, और **केवल जब कोई DAQ बंधा हो + कैम कैलिब्रेट किया गया हो**; अन्यथा छोड़ दिया जाता है। |
| `preview` / `display` | पूर्ण GUI-पूर्वावलोकन श्रृंखला (CCM + WB + प्रति के अनुसार गामा कैम की प्रोफ़ाइल)। `lattice capture` इस `preview` का नामकरण करता है; `array-capture`/`multi-capture` `display` का उपयोग करते हैं. | सभी कैमरे। |

केवल उसी को सहेजने के लिए एक स्तर पास करें (`--processing debayered`)। जब आप `all` का अनुरोध करते हैं, तो दिए गए कैमरे पर लागू न होने वाले स्तरों को छोड़ दिया जाता है (और रिपोर्ट किया जाता है), त्रुटि नहीं दिखाई जाती — एक असंयोजित या अकैलिब्रेटेड कैम को फिर भी `raw` / `debayered` / `preview` मिलता है।

किसी भी परावर्तन फ़्रेम के लिए, वास्तव में उपयोग की गई DAQ डाउनवेलिंग रीडिंग इमेजरी के बगल में एक **`.daq`** साइडकार में लिखी जाती है (ताकि कैप्चर को बाद में फिर से प्रोसेस किया जा सके) और एक `DAQ:` लाइन पर रिपोर्ट की जाती है।

### कैप्चर फ़ोल्डर कैसा दिखता है

प्रत्येक एक्सपोर्ट प्रकार `-o` के अंतर्गत अपने **स्वयं के सबफ़ोल्डर** में जाता है, इसलिए एक मल्टी-लेवल कैप्चर कभी भी प्रकारों को मिलाता नहीं है:

```
output/
├── raw/           capture_<ts>_SN<serial>_raw.tif
├── debayered/     capture_<ts>_SN<serial>_debayered.tif
├── radiance/      capture_<ts>_SN<serial>_radiance.tif
├── reflectance/   capture_<ts>_SN<serial>_reflectance.tif
├── preview/       capture_<ts>_SN<serial>_display.tif
├── index/         per-camera vegetation-index (LUT) render, when --index is on
├── composite/     array foreground/background live-view composite, when produced
└── *.daq          the downwelling reading matched to the capture
```

`<ts>` कैप्चर टाइमस्टैम्प है और `<serial>` कैमरा सीरियल है, इसलिए एक सिंक किया गया समूह कैमरों के बीच एक टाइमस्टैम्प साझा करता है। **एक विषमता पर ध्यान दें:** `display` स्तर एक फ़ोल्डर में संग्रहीत होता है
जिसका नाम `preview/` है, जबकि फ़ाइलें स्वयं नाम में `_display` रखती हैं — उस स्तर के लिए केवल फ़ोल्डर और उपसर्ग अलग होते हैं। अज्ञात स्तर अपने ही नाम के फ़ोल्डर पर वापस चले जाते हैं, और यदि सबफ़ोल्डर
बनाया नहीं जा सकता तो फ़ाइल खो जाने के बजाय आउटपुट रूट पर लिखी जाती है।

**कैप्चर फ़ोल्डर को फिर से प्रोसेस करना:** `chloros-cli process` को * पर इंगित करें*कैप्चर रूट**
(`output/`). `process` सामान्य रूप से केवल वह फ़ोल्डर आयात करता है जिसका आप नाम देते हैं, लेकिन जब उस फ़ोल्डर में कोई छवियाँ नहीं होती हैं और उसमें सबफ़ोल्डर होते हैं तो यह स्वचालित रूप से नीचे जाता है — इसलिए रूट के स्तर के सबफ़ोल्डर और
रूट `.daq` सभी एक साथ चुने जाते हैं। कैप्चर का प्रत्येक स्तर एक ही छवि के रूप में इम्पोर्ट होता है, जिसमें अन्य स्तर मोड के रूप में उपलब्ध होते हैं, न कि प्रत्येक स्तर के लिए एक अलग छवि के रूप में।

सीधे **लेवल सबफ़ोल्डर** का नामकरण करना (जैसे `output/raw/`) भी काम करता है। ऐसा करने से रूट
`.daq` पीछे रह जाता है, इसलिए जब आप `raw/` से एक रेडियोमेट्रिक उत्पाद फिर से प्राप्त करते हैं तो DAQ रीडिंग को साथ में कॉपी करें या पॉइंट करें — अन्यथा टाइमस्टैम्प मैच के लिए कोई मिलान नहीं होता है।

**प्रोसेसिंग हमेशा `raw` से शुरू होती है।** प्रत्येक कैप्चर के भीतर कच्चा फ्रेम पाइपलाइन स्रोत होता है;
`debayered`, `radiance`, `reflectance` और `preview` देखने योग्य मोड के रूप में आते हैं लेकिन इन्हें कभी भी पाइपलाइन के माध्यम से वापस नहीं भेजा जाता। पुन:- एक व्युत्पन्न उत्पाद को प्रोसेस करने से विनेट, CCM और रेडियंस मैथ फिर से लागू होंगे जो पहले से ही इसके पिक्सल में बेक किए गए हैं, इसलिए Chloros डबल-प्रोसेसिंग के बजाय अस्वीकार कर देता है। जानने योग्य दो परिणाम:

- `index/` और `composite/` रेंडर कभी भी संसाधित नहीं होते हैं। वे आउटपुट हैं, कैप्चर नहीं — एक NDVI LUT रेंडर का कोई सार्थक रेडियंस व्याख्या नहीं होती है।
- एक कैप्चर फ़ोल्डर जो **बिना** एक्सपोर्ट किया गया हो `raw` (उदाहरण के लिए `array-capture --processing reflectance`) का कोई वैध पाइपलाइन स्रोत नहीं है। वे कैप्चर सामान्य रूप से इम्पोर्ट और डिस्प्ले होते हैं, लेकिन `process` उन्हें स्किप कर देता है और ऐसा होने की सूचना देता है:

  ```
  [IMPORT-LEVEL] Skipping 4 already-processed file(s) with no raw source: capture_…_reflectance.tif
  [IMPORT-LEVEL] Processing starts from raw. Re-capture with --processing raw, or force an entry
                 point with --input-level.
  ```

  यदि आपको वास्तव में किसी व्युत्पन्न उत्पाद को आगे भेजने की आवश्यकता है — तो `demosaic` चालू होने पर कैप्चर किया गया कोई हब सत्र, या कोई लेगेसी फ़ोल्डर — `--input-level {raw,debayered,processed}` प्रविष्टि बिंदु को मजबूर करता है और स्किप को ओवरराइड करता है। वह फ़्लैग जानबूझकर बनाया गया एक बचाव का रास्ता है; `auto` (डिफ़ॉल्ट) कभी भी ऐसी कैप्चर को प्रोसेस नहीं करता जिसमें कोई रॉ न हो।

### मिश्रित-फ़िल्टर ऐरे में छोड़ी गई कैप्चर

जब आप एक ही ऐरे में RGB और मल्टीस्पेक्ट्रल कैम को मिलाते हैं, `array-capture --processing radiance` (या `reflectance`) मल्टीस्पेक्ट्रल फ्रेम को सहेजता है और RGB कैम्स को **छोड़ देता है** — एक ब्रॉडबैंड सेंसर के लिए प्रति-बायर रेडियंस का कोई अर्थ नहीं है। CLI प्रत्येक सहेजी गई फ़ाइल को (उसके एक्सपोर्ट स्तर के साथ) प्रिंट करता है, प्रत्येक लिखे गए `.daq`, और प्रत्येक स्किप को स्पष्ट रूप से, ताकि फ़ाइलों की संख्या आश्चर्यजनक न हो:

```
  Saved: output/sync_…_SN213800234.tif [reflectance] (SN:213800234, fid:1)
  Saved: output/sync_…_SN214000533.tif [reflectance] (SN:214000533, fid:1)
  Saved: output/sync_…_SN214701288.tif [reflectance] (SN:214701288, fid:1)
  DAQ:   output/sync_…_daq-e-54b5e0.daq
  Skipped: SN:214701292 (reflectance-not-applicable-to-rgb-cam filter=RGB)

  3 synchronized frames captured. (1 skipped)
```

स्किप-reason टोकन `<level>-not-applicable-to-rgb-cam` पैटर्न का पालन करते हैं। रिफ्लेक्टेंस `reflectance-skipped-no-fresh-dls` / `reflectance-skipped-bound-daq-unavailable (…)`, और `dls-uncalibrated-band-<nm>` के साथ भी स्किप कर सकता है जब बैंड अधिकांशतः DAQ लाइट सेंसर की रेडियोमेट्रिक रूप से कैलिब्रेटेड रेंज (~374–974 nm) के बाहर होता है — शिपिंग SKUs में केवल F988, जिसका समर्थित पथ रिफ्लेक्टेंस-पैनल वर्कफ़्लो है।

`--processing debayered` का उपयोग करें (या `display`) फ़िल्टर के प्रकार की परवाह किए बिना हर कैम को शामिल करने के लिए, या डिफ़ॉल्ट `all` हर कैम के लिए एक ही बार में हर लागू स्तर प्राप्त करने के लिए।

---

## कैप्चर मोड, रिकॉर्डर और ऑफ़लाइन रीप्रोसेस

ये सभी एक **स्थायी एरे** (पहले `array-connect` चलाएँ) पर काम करते हैं। वे GUI कैप्चर पैनल को दर्शाते हैं।

### `array-capture` मोड

`array-capture` एक एकल कमांड है जिसमें चार शटर मोड और एक्सपोर्ट टॉगल का एक सेट है:

| मोड | फ़्लैग | व्यवहार |
| --- | --- | --- |
| **एकल** *(डिफ़ॉल्ट)* | (कोई नहीं) | एक सिंक किया गया कैप्चर समूह, फिर बाहर निकलें। |
| **सतत** | `--continuous` | `Ctrl+C`, `--count N`, या `--duration S` तक लगातार पास। |
| **अंतराल** | `--interval S` | हर `S` सेकंड में एक पास (प्रत्येक पास की शुरुआत से मापा गया), एक ही सीमाएँ। |
| **सबसे तेज़** | `--fastest` | केवल कच्चा + असाइन किया गया DAQ रीडिंग + संयुक्त-सूचकांक कंपोजिट; रेडियंस/रिफ्लेक्टेंस/डिस्प्ले मैथ को छोड़ देता है ताकि फ्रेम तेज़ी से आए। यह `--processing raw --force-daq`। बाद में सहेजे गए `.daq` को कैलिब्रेटेड उत्पादों में पुनः संसाधित करें। |

एक्सपोर्ट टॉगल (किसी भी मोड के साथ संयोजित करें; सभी GUI/SDK एंडपॉइंट साझा करते हैं):

| फ़्लैग | प्रभाव |
| --- | --- |
| `--processing LEVEL` | एकल निर्यात स्तर, या `all` (डिफ़ॉल्ट)। |
| `--levels L1,L2,…` | निर्यात प्रकारों का स्पष्ट उपसमूह (जैसे `raw,radiance,reflectance`); **`--processing` को ओवरराइड करता है**। |
| `--aligned` / `--no-aligned` | प्रत्येक सदस्य के गैर-कच्चे निर्यात को एरे के [संरेखण प्रोफ़ाइल](#alignment) (सह-पंजीकृत) के अनुसार वॉर्प करता है. कच्चा अनवॉर्प्ड रहता है लेकिन मेटाडेटा में ट्रांसफॉर्म को ले जाता है। यदि एरे के पास कोई प्रोफ़ाइल नहीं है तो यह अनअलाइनड पर वापस चला जाता है (एक चेतावनी के साथ)। |
| `--index` / `--no-index` | प्रति-कैम वेजिटेशन-इंडेक्स ओवरले को सहेजें / छोड़ें जहाँ एक कॉन्फ़िगर किया गया है। डिफ़ॉल्ट: इसे रेंडर करें। |
| `--force-daq` | असाइन की गई DAQ/DLS रीडिंग को एक `.daq` साइडकार के रूप में सहेजें, भले ही किसी चुने हुए स्तर को इसकी आवश्यकता न हो (जैसे कि केवल-कच्चा ग्रैब), ताकि फ्रेम को फिर सेऑफ़लाइन रिफ्लेक्टेंस/इंडेक्स में संसाधित किए जा सकें। |
| `--smart` | ट्रिगर करने से पहले सभी कैम्स पर AE के स्थिर होने का इंतज़ार करें (देखें [Smart-AE / Smart-Capture](#smart-ae--smart-capture)). |
| `--compression {deflate,none}` | TIFF पिक्सेल संपीड़न। `deflate` (डिफ़ॉल्ट) = लॉसलेस zlib L1 + हॉरिज़ॉन्टल प्रिडिक्टर, ~4.1 एमबी प्रति फुल-रेज़ोल्यूशन फ़्रेम; `none` = अनकंप्रेस्ड, ~6.3 एमबी प्रति फ़्रेम पर ~5× तेज़ी से लिखना — जब डिस्क अनुमति दे तो अधिकतम निरंतर दर के लिए उपयोग करें। दोनों लॉसलेस हैं और इम्पोर्ट पर समान रूप से पढ़े जाते हैं। |

> **सिंगल-राइट TIFF + सस्टेन्ड-रेट मॉडल।** कैप्चर * में लिखे जाते हैं*एक**tifffile पास जो पिक्सल + XMP + IFD0 मेक/मॉडल ले जाता है (पूर्ण-रिज़ॉल्यूशन Mono12 पर मापा गया: 36 ms संपीड़ित / 6.5 ms असंपीडित, बनाम पुराने write-then-ExifTool rewrite के लिए ~148 ms); अकेला बचा हुआ ExifTool का काम (EXIF सब-IFD पॉलिश) एक एसिंक बैकग्राउंड वर्कर पर चलता है, और एक फ्रेम पूरा और इम्पोर्ट-तैयार हो जाता है भले ही यह कभी न चले। ध्यान दें कि DEFLATE कंप्रेशन Python GIL को होल्ड करता है, इसलिए कंप्रेशन्ड राइट्स कैमरा-प्रति राइटर थ्रेड्स में समानांतर नहीं होते — निरंतर 8-कैम फुल-सेंसर दर पर -res कैप्चर (~10.4 fps) के लिए `--compression none`**और** NVMe-क्लास डिस्क (~500 MB/s का निरंतर लेखन) की आवश्यकता होती है। यही नॉब  पर `compression` के रूप में उपलब्ध है।00578.

```bash
# Interval timelapse: one reflectance pass every 10 s for 5 minutes
chloros-cli lattice array-capture --interval 10 --duration 300 \
  --processing reflectance -o timelapse/

# Fastest grab for a moving rig — raw + .daq now, calibrate later
chloros-cli lattice array-capture --fastest -o flightline/

# Co-registered multi-band export (drop the index overlay)
chloros-cli lattice array-capture --processing reflectance --aligned --no-index -o out/
```

### `array-record` — संयुक्त-सूचकांक वीडियो/GIF (निगरानी-ग्रेड)

**लाइव संयुक्त-सूचकांक दृश्य** जो कुछ भी दिखा रहा है, उसे एक `.avi` में रिकॉर्ड करता है (और वैकल्पिक रूप से एक `.gif`)। चूँकि यह लाइव कंपोजिट को टैप करता है, इसलिए फ्रेम के आने के लिए संयुक्त स्ट्रीम खुला होना चाहिए (जैसे कि एरे का GUI में पूर्वावलोकन किया जा रहा है)। यह हर 2 सेकंड में प्रगति की जाँच करता है और `--duration`, `Ctrl+C` पर, या जब रिकॉर्डर अपने आप समाप्त हो जाता है, तब रुक जाता है।

```bash
# 30-second combined-index clip at 10 fps, plus a GIF
chloros-cli lattice array-record --duration 30 --fps 10 --gif -o monitoring/
```

| फ़्लैग | डिफ़ॉल्ट | विवरण |
| --- | --- | --- |
| `--array-id ID` | केवल ऐरे | लक्ष्य ऐरे (यदि केवल एक जुड़ा हो तो छोड़ दें)। |
| `-o, --output DIR` | `output` | आउटपुट निर्देशिका (बैकएंड-लोकल)। |
| `--fps F` | `10` | रिकॉर्डिंग फ्रेम दर। |
| `--duration S` | Ctrl+C तक | `S` सेकंड के बाद स्वचालित रूप से रुकें। |
| `--gif` | बंद | एक एनिमेटेड GIF भी लिखें। |
| `--gif-only` | बंद | केवल एक GIF लिखें (कोई `.avi` नहीं)। |

### `array-burst` — रॉ-बायर हाई-एफपीएस बर्स्ट (विश्लेषण-ग्रेड)

ग्रेब लूप के सिंक-ग्रुप बफ़र को सीधे पढ़ता है — **कैलिब्रेशन चेन, एक्सआईएफटूल, लाइव व्यू की आवश्यकता नहीं** — इसलिए यह कैमरे की पूरी ग्रैब दर पर चलता है। `<output>/bursts/<base>/` के तहत रॉ फ्रेम + एक प्रति-फ्रेम मैनिफेस्ट + प्रत्येक अलग DLS रीडिंग के लिए एक `.daq` लिखता है।। ऑफ़लाइन पुनः संसाधित करें (अगली कमांड), या इसे तुरंत रोकने पर करने के लिए `--build` पास करें।

```bash
# 5-second raw burst, then build the combined index video in one shot
chloros-cli lattice array-burst --duration 5 --build \
  --products combined:index --fps 10 -o capture/
```

| फ़्लैग | डिफ़ॉल्ट | विवरण |
| --- | --- | --- |
| `--array-id ID` | केवल ऐरे | लक्ष्य ऐरे। |
| `-o, --output DIR` | `output` | आउटपुट निर्देशिका (बर्स्ट `<DIR>/bursts/<base>/` में उतरता है). |
| `--duration S` | Ctrl+C तक | `S` सेकंड के बाद स्वचालित रूप से रुक जाता है। |
| `--max-frames N` | अनियंत्रित | `N` के बाद स्वचालित रूप से रुक जाता है।X000605 कच्चे फ़्रेम। |
| `--build` | बंद | रुकने के बाद, बर्स्ट को तुरंत फिर से संसाधित करें (`array-build-video` के समान)। |
| `--products …` | `combined:index`000605 कच्चे फ्रेम के बाद स्वचालित-रोक। |
| `--max-frames N` | असीमित | `N` कच्चे फ्रेम के बाद स्वचालित-रोक। |
| `--build` | `--fps F` | कौन सा वीडियो (कौन से वीडियो) बनाना है (नीचे देखें)। |
| `10` | `--build` | बस्ट (burst) के बाद तुरंत पुनः संसाधित करें (`array-build-video` के समान)। |
| `--save-tiffs` | `--build` | `--gif` | `--build` | `array-build-video` | `.daq` | `--products` | `kind:level` | `kind` | `per_cam` | `combined` | `level` | X | `--build` के साथ: कौन सा वीडियो (या वीडियो) बनाना है (नीचे देखें)। |
| `--fps F` | `10` | `--build` के साथOTX: आउटपुट वीडियो fps. |
| `--save-tiffs` | बंद | `--build` के साथ: प्रति-फ्रेम कैलिब्रेटेड TIFFs भी सहेजें। |
| `--gif` | बंद | `--build` के साथ: एनिमेटेड GIF(s) भी लिखें। |

### `array-build-video` — एक सहेजे गए बर्स्ट को ऑफ़लाइन पुनः संसाधित करें

प्रत्येक कच्चे फ्रेम को सबसे करीबी सहेजी गई `.daq` रीडिंग से समय के अनुसार मिलाता है और इसे **इम्पोर्ट पाइपलाइन के समान रेडियंस / रिफ्लेक्टेंस / इंडेक्स चेन** से गुज़ारता है, जिससे एक या अधिक वीडियो रेंडर होते हैं।

`--products`, `kind:level` आइटम्स की एक कॉमा सूची है, जहाँ `kind` ∈ `per_cam` | `combined` और `level` ∈ `radiance` | `reflectance` | `index`। एक खाली `level` (`kind:` नहीं) डिफ़ॉल्ट रूप से `per_cam` है। डिफ़ॉल्ट `combined:index` है।

```bash
# Per-cam reflectance video for every member + one combined NDVI video
chloros-cli lattice array-build-video \
  --burst-dir "capture/bursts/2026-06-24_141500" \
  --products per_cam:reflectance,combined:index \
  --fps 10 --save-tiffs
```

| फ़्लैग | डिफ़ॉल्ट | विवरण |
| --- | --- | --- |
| `--burst-dir DIR` | (आवश्यक) | बस्ट फ़ोल्डर का पथ (`…/bursts/<base>/`). |
| `--products …` | `combined:index` | `kind:level` सूची, जैसा कि ऊपर है। |
| `--fps F` | `10` | आउटपुट वीडियो fps. |
| `--save-tiffs` | बंद | वीडियो(ओं) के साथ-साथ प्रति-फ्रेम कैलिब्रेटेड TIFFs भी सहेजें. |
| `--gif` | बंद | एनिमेटेड GIF(s) भी लिखें। |

> **सही रिकॉर्डर चुनें।** `array-record` *निगरानी-ग्रेड* है — यह लाइव कंपोजिट को डिस्प्ले के अनुसार कैप्चर करता है और इसके लिए स्ट्रीम का खुला रहना आवश्यक है। `array-burst` → `array-build-video` *विश्लेषण-ग्रेड* है — यह पूर्ण दर पर कच्चा सेंसर डेटा सहेजता है और कैलिब्रेटेड रेडियंस/रिफ्लेक्टेंस/वीडियो को बाद में इंडेक्स करता है, और इसके लिए लाइव व्यू की आवश्यकता नहीं होती।

### मोनो (M3M) सिंगल-बैंड कैमरे

**M3M**लाइन, बायर**M3C**का मोनो समकक्ष है: प्रत्येक कैमरे के लिए एक संकीर्ण-बैंड इंटरफेरेंस फ़िल्टर (`M3M-<lens>-F<wavelength>`, उदाहरण के लिए `M3M-L87-F685`), इसलिए सेंसर एक**एक ग्रेस्केल बैंड* प्रदान करता है जिसमें कोई बायर मोज़ेक नहीं होता। डेमोसाइक करने के लिए कुछ नहीं है, इंटर-चैनल क्रॉसटॉक को अनमिक्स करने के लिए कुछ नहीं है, और सेट करने के लिए कोई व्हाइट बैलेंस नहीं है — पूरी RGB-डिस्प्ले कलर पाइपलाइन बस लागू नहीं होती है।

CLI पर इसका क्या मतलब है:

- **`lattice white-balance`, `lattice color-profile`, `lattice color`**एक मोनो कैम का पता लगाते हैं और बेमतलब सेटिंग्स देने के बजाय**एक-लाइन संदेश के साथ स्किप कर देते हैं**। वे अभी भी उसी सत्र में RGB/Bayer M3C कैम के साथ सामान्य रूप से चलते हैं।
- **`lattice calibrate` / `process --reflectance` / `array-capture --processing radiance`** अभी भी काम करते हैं — रेडियंस और रिफ्लेक्टेंस *प्रति-बैंड* रेडियोमेट्रिक मानचित्र होते हैं और एक बैंड के लिए पूरी तरह से परिभाषित होते हैं। मोनो फ्रेम एक **पहचान** सेंसर-प्रतिक्रिया मैट्रिक्स (कोई 3×3 अनमिक्स नहीं) ले जाते हैं, इसलिए विमान बिना किसी छेड़छाड़ के कैलिब्रेशन गणित से गुजरता है।
- **एक अकेला मोनो कैम वनस्पति सूचकांक नहीं बना सकता है।**NDVI / NDRE /etc. के लिए कम से कम दो बैंड की आवश्यकता होती है (जैसे Red + NIR)। मोनो हार्डवेयर से सूचकांक प्राप्त करने के लिए,**कई** M3M कैम को विभिन्न तरंगदैर्घ्यों पर इंगित करें, उन्हें एक मल्टी-बैंड स्टैक में संरेखित करें, और *उसका* सूचकांक निकालें:

```bash
# Red (660) + NIR (850) mono pair -> aligned 2-band stack -> NDVI
chloros-cli lattice array-connect --serials SN_RED,SN_NIR
chloros-cli lattice index --live --profile align.json \
  --preset NDVI --channel red=Red_660 --channel nir=NIR_850 \
  --save-multiband -o output/
```

`--channel` प्रतीक **ठीक-ठीक* preset के चैनल नामों से मेल खाने चाहिए* (अक्षर-संवेदनशील; NDVI छोटे अक्षरों वाले `red`,`nir` — `--list-presets` देखें), और बैंड साइड नाम संरेखित स्टैक में एक बैंड है (ऑफ़लाइन मोड 0-आधारित बैंड सूचकांकों को भी स्वीकार करता है, उदा. `--channel red=0 --channel nir=1`).

पूरे स्टैक में डिस्क्रिमिनेटर मॉडल स्ट्रिंग में `M3M` टोकन है (यह कभी भी `M3C` स्ट्रिंग में नहीं आता है), जो GUI तक पहुँचाया जाता है/ SDK को `is_mono` के रूप में दिखाता है।

---

## होस्ट NIC सेटअप और ट्यूनिंग (LATTICE ऐरे)

LATTICE कैमरे होस्ट के ईथरनेट एडाप्टर पर GVSP को स्ट्रीम करते हैं, इसलिए मल्टी-कैमरा ऐरे के लिए, लिंक रेट जितना महत्वपूर्ण है, उतना ही महत्वपूर्ण एडॉप्टर का **ड्राइवर**और**रिसीव-रिंग साइज़** भी होता है। गलत सेटिंग्स ऐरे सेटिंग्स पैनल में एक `FRAMES WILL DROP` / `Reduce ROI to enable` गेट के रूप में दिखाई देती हैं। (और `lattice network-analysis` / SDK के `analyze_array_network()` में), भले ही कैमरे स्वयं ठीक हों।

### यूएसबी 10GbE एडाप्टर — रियलटेक RTL8157 (&quot;रियलटेक यूएसबी 10GbE फैमिली कंट्रोलर&quot;)

| आइटम | आवश्यक मान | यह क्यों मायने रखता है |
| --- | --- | --- |
| **ड्राइवर संस्करण**|**≥ v10.67 (जनवरी 2026)**, INF `rtump64x64sta.inf` | पुराना**2016**ड्राइवर (v10.65, `rtump64x64.inf`) पावर-डाउन को ठीक से संभालता नहीं है और**`DRIVER_POWER_STATE_FAILURE`**के साथ बगचेक करता है। (BSOD `0x9F`)** शटडाउन/रीस्टार्ट/स्लीप पर होता है। ट्रांज़िशन अटक जाता है (~5 मिनट टाइमआउट), उपयोगकर्ता जबरदस्ती पावर-ऑफ कर देता है, और बार-बार होने वाले गंदे शटडाउन**WMI रिपॉजिटरी को भ्रष्ट कर देते हैं**(PowerShell/टूल्स `Invalid class` के साथ काम करना बंद कर देते हैं) और अगले बूट पर**USB स्टैक को अटका देते हैं** (NIC सक्षम नहीं होगा; USB ड्राइव्स की सूची बनना बंद कर देती हैं)। realtek.com से अपडेट करें। (या डोंगल विक्रेता) से संपर्क करें, इससे पहले कि आप क्लीन रीस्टार्ट पर निर्भर करें। |
| **रिसिव बफ़र्स**— कीवर्ड `ReceiveBufferLen` |**256**(ड्राइवर अधिकतम) | NIC RX रिंग।**32**का ड्राइवर डिफ़ॉल्ट केवल ~0.26 MB का उपयोग योग्य रिंग छोड़ता है — जो एक मल्टी-कैम बर्स्ट के लिए बहुत छोटा है — इसलिए एरे पैनल `Sim-emit burst … exceeds NIC RX ring usable capacity 0.26 MB` रिपोर्ट करता है और कनेक्ट को ब्लॉक कर देता है।**256**पर रिंग बड़ी होती है (**लैब 10GbE होस्ट पर मापा गया ~13.5 MB**), जिससे RX पाइपलाइन को मल्टी-कैम GVSP बर्स्ट के लिए वास्तविक हेडरूम मिलता है। (कि कोई दिया गया कॉन्फ़िग वास्तव में *कनेक्ट* होता है या नहीं, यह दो जांचों द्वारा तय किया जाता है — **ड्रेन-अवेयर**एडमिट्टेंस जांच और**एग्रीगेट ओवर-सब्सक्रिप्शन** जांच — न कि एक कच्चा बर्स्ट-बनाम-रिंग तुलना; देखें [एरे fps और बर्स्ट मॉडल](#array-fps--burst-model).) |
| **Receive URBs**— कीवर्ड `PendingReceives` |**64** (अधिकतम) | प्रगत में USB अनुरोध ब्लॉक; बर्स्ट अवशोषण के लिए Receive Buffers के साथ बढ़ाएँ। |
| **जंबो फ्रेम** — कीवर्ड `*JumboPacket` | **9014** | 9000-बाइट के GVSP पैकेट के लिए आवश्यक (1500 की तुलना में प्रति फ्रेम 6× कम पैकेट). |

> ⚠️ **एक NIC ड्राइवर अपडेट इन उन्नत गुणों को डिफ़ॉल्ट पर रीसेट कर देता है।**एडाप्टर ड्राइवर को अपडेट या बदलने के बाद, `ReceiveBufferLen=256` और `PendingReceives=64` को**दोबारा लागू करें**, अन्यथा ऐरे पैनल फिर से गेट करेगा, भले ही &quot;हार्डवेयर में कुछ भी नहीं बदला हो।&quot; यह पहले से काम कर रहे रिग के अचानक कनेक्ट होने से इनकार करने का #1 कारण है।

एक **उन्नत (elevated)** PowerShell से लागू करें (अपने एडाप्टर का नाम बदलें, जैसे `"Ethernet 5"`):

```powershell
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen -RegistryValue 256
Set-NetAdapterAdvancedProperty -Name "Ethernet 5" -RegistryKeyword PendingReceives  -RegistryValue 64
Get-NetAdapterAdvancedProperty  -Name "Ethernet 5" -RegistryKeyword ReceiveBufferLen,PendingReceives   # verify
```

> **`lattice network --fix` USB 10GbE एडाप्टरों को कवर करता है।**यह अब एडाप्टर के प्रकार का पता लगाता है और सही रिसीव-रिंग कीवर्ड को ट्यून करता है: PCIe NICs (Intel I219, आदि), या Realtek**USB* के लिए `ReceiveBufferLen`→256 + `PendingReceives`→64।* 10GbE कंट्रोलर (जो `*ReceiveBuffers` को एक्सपोज़ नहीं करता है)। टारगेट प्रत्येक ड्राइवर द्वारा रिपोर्ट किए गए अधिकतम (`NumericParameterMaxValue`) पर क्लैम्प किए जाते हैं, ताकि यह कभी भी सीमा से बाहर का मान न लिखे। इसे एक **उन्नत (elevated)** टर्मिनल से चलाएँ; किसी भी रजिस्ट्री-आधारित ट्यून की तरह, यह बदलाव एक एडॉप्टर रीस्टार्ट या रीबूट के बाद प्रभावी होता है। ऊपर दिए गए मैन्युअल `Set-NetAdapterAdvancedProperty` कमांड एक अच्छा विकल्प बने हुए हैं — वे बिना रीस्टार्ट के लाइव लागू होते हैं (एडाप्टर को फिर से बाइंड करें)।

### नेटवर्क की बुनियादी बातें (सभी LATTICE लिंक)

- **पताकरण:** लिंक-लोकल `169.254.0.0/16` (GigE Vision LLA)। होस्ट एक स्थैतिक `169.254.x.x/16` लेता है; कैमरे + DAQ-E उसी रेंज में स्व-नियुक्त करते हैं। DHCP/गेटवे की आवश्यकता नहीं है।
- **पैकेट आकार:**जंबो (9000) को प्राथमिकता दें, लेकिन ऑटो-प्रोब को इसे खोजने दें — यह पुन-हर कनेक्ट पर मापता है और पहले से ही एक GVSP प्रोब के माध्यम से कैमरे की 1500-बाइट ICMP सीमा को पार कर देखता है, इसलिए यह जंबो पर तब पहुँचता है जब भी तार वास्तव में इसे ले जा सकता है। `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` के साथ पिन तभी करें जब आप प्रोब से बेहतर जानते हों, और स्थायी की बजाय प्रति-कमांड को प्राथमिकता दें: एक पिन प्रोब को छोड़ देता है, इसलिए यदि पथ वास्तव में 9000 को ले नहीं जा सकता है तो `SC_ERR_TIMEOUT -1011` के साथ**हर** कैप्चर टाइम आउट हो जाता है (देखें [पर्यावरण चर](#environment-variables)).
- **RX रिंग `ReceiveBufferLen` के साथ स्केल होती है:**डिफ़ॉल्ट `32` पर उपयोग योग्य रिंग ~0.26 MB है (किसी भी मल्टी-कैम बर्स्ट के लिए बहुत छोटी); अधिकतम `256` पर यह बड़ा होता है (~13.5 MB, लैब 10GbE होस्ट पर मापा गया), जो वास्तविक हेडरूम प्रदान करता है। फिर यह तय किया जाता है कि कोई कॉन्फ़िग कनेक्ट होता है या नहीं, यह ड्रेन-अवेयर एडमिटेन्स चेक**और** ओवर के समेकन द्वारा निर्धारित होता है- नीचे सदस्यता जाँच — यह एक कच्चे बर्स्ट-बनाम-रिंग की तुलना नहीं है।

### एरे fps और बर्स्ट मॉडल

एरे सेटिंग्स पैनल (और `lattice analyze-array` / SDK के `analyze_array_network`) को कैसे पढ़ें:

- **बर्स्ट प्रत्येक कैमरे के वास्तविक पिक्सेल फ़ॉर्मेट पर प्रति-कैमरा जमा किया जाता है।**मोनो**M3M**कैमरे**Mono12 (2 B/px)**स्ट्रीम करते हैं;**M3C**बायर कैमरे 8- या 12-बिट स्ट्रीम करते हैं (TRI032S चुपचाप BayerRG12 उत्सर्जित करता है, भले ही BayerRG8 का अनुरोध किया गया हो)। इसलिए 4-कैम पूरा-रिज़ॉल्यूशन वाला फ़्रेम**~12.6 एमबी है यदि सभी 8-बिट हों, लेकिन तीन 12-बिट मोनो कैम के साथ ~25 एमबी**। प्रोजेक्शन प्रत्येक कैम के फ़ॉर्मेट को उसके मॉडल (आईडेंटिटी कैश) से निर्धारित करता है।, इसलिए बर्स्ट वास्तव में वायर द्वारा वहन किए जाने वाले डेटा से मेल खाता है — न कि एक-आकार वाले BayerRG8 अनुमान से।
- **एक USB ईथरनेट एडाप्टर अपनी नाममात्र क्षमता की परवाह किए बिना 200 MB/s पर सीमित होता है।**दक्षता तालिका जो लिंक दर को एक स्थिर आंकड़े में बदलती है, वह PCIe-व्युत्पन्न है; एक USB NIC अपनी *ईथरनेट* लिंक दर का विज्ञापन करती है लेकिन यह USB बस और उसके ड्राइवर द्वारा सीमित होती है। एक USB 10GbE डोंगल ने ~1063 MB/s स्कोर करने के लिए इस्तेमाल किया गया था। &quot;स्थिर&quot; — एक ऐसा नंबर जिसकी कभी जांच नहीं की गई — और इस वजह से हुई रफ्तार ने 6–18% फ्रेम को खराब कर दिया, जबकि यह अभी भी एक स्वस्थ लक्ष्य fps की रिपोर्टिंग कर रहा था। USB-संलग्न NICs अब एक निश्चित **200 MB/s** पर सीमित हैं (यह सीमा बस की है, इसलिए यह नाम-प्लेट के साथ नहीं बढ़ती है; एक USB 1 GbE एडाप्टर ~80 MB/s प्राप्त करता है और इससे प्रभावित नहीं होता है). क्षमता रिकॉर्ड पर `wire_ceiling_source` शब्दों में ऐसा कहता है, और `nic_is_usb` इसे फ़्लैग करता है। किसी भी तरह से `--wire-ceiling-mbps` के साथ इसे ओवरराइड करें।
- **एडमिट्टेंस ड्रेन-अवेयर है, न कि पूरा-बर्स्ट-बनाम-रिंग।** एक समवर्ती बर्स्ट को केवल *अस्थायी बैकलॉग* = `max(0, Σ per-cam arrival − host drain) × emit_window` में फिट होना होता है, पूरे बर्स्ट में नहीं। एक तेज़-होस्ट / धीमी-कैम फैब्रिक (एक **PCIe**10G होस्ट + 4× 1 GbE कैमरे: आगमन ≈ 320 MB/s, ड्रेन ≈ 1063 MB/s) होस्ट कैमरों के भरने से तेज़ी से ड्रेन करता है, बैकलॉग ≈ 0, इसलिए फुल-रेज़ सिम-एमिट**admits**भले ही 25 MB का बर्स्ट 13.5 MB रिंग से अधिक हो। उसी चार कैमरों को एक**USB**के पीछे लगाएं 10GbE एडाप्टर और ड्रैन 1063 नहीं बल्कि 200 MB/s है — आगमन ड्रैन से आगे निकल जाता है, और नुकसान कम फ्रेम दर के बजाय भ्रष्ट फ्रेम के रूप में दिखता है। 1 GbE होस्ट पर कैम्स का 31.25 MB/DLThr फ़्लोर के कारण आगमन, निकासी से आगे निकल जाता है → यह सही ढंग से**ब्लॉक** कर देता है (*इस* प्रकार के ब्लॉक के लिए, ROI कम करें या ≥ 2 पर बिनिंग का उपयोग करें)। एडमिटेंस **दो** कनेक्ट गेट्स में से एक है — दूसरा नीचे दिया गया एग्रीगेट ओवर-सब्सक्रिप्शन चेक है।
- **अनुमानित fps एक रूढ़िवादी सीरियल-रीट्रीव सीमा है।**होस्ट ग्रैब लूप वर्तमान में प्रत्येक कैम के बफ़र को**सीरियल रूप से**खींचता है (~प्रत्येक कैम के लिए एक-एक इमिट विंडो), इसलिए चक्र `max(readout+emit, N × emit)` द्वारा सीमित है, जिसमें प्रति-कैम इमिट को कैमरे के**एक्सेस लिंक**पर क्लैम्प किया गया है। (1 GbE ≈ 80 MB/s), न कि होस्ट अपलिंक। 4-कैम फुल-रेज़ 12-बिट एरे के लिए यह**~2.8 fps**है, जो मापे गए ~2.7–3.0 से मेल खाता है। fps जानबूझकर**एक्सपोज़र-स्वतंत्र** है, इसलिए कम रोशनी वाले दृश्यों में एक्सपोज़र बढ़ने पर वास्तविक दर थोड़ी सी सीमा से नीचे जा सकती है। सीरियल रिट्रीव ही असली fps लिमिटर है; इसे समानांतर करने से सीमा सिंगल-एमिट दर की ओर बढ़ जाएगी।
- **कुल ओवर-सब्सक्रिप्शन एक हार्ड कनेक्ट ब्लॉकर है।**प्रति-कैम बैंडविड्थ आवंटन फर्श पर**8 MB/s**(`ARRAY_PER_CAM_FLOOR_BPS`), इसलिए एक बार जब फ़्लोर क्लैंप हो जाता है, तो कुल मांग (`per_cam × N`)**कोलिज़न-सुरक्षित वायर सीलिंग**(`sustained × sim_emit_factor`) से अधिक हो सकती है। व्यावहारिक 1 GbE पर पूर्ण-रिज़ॉल्यूशन सीमाएँ:**1500 MTU पर 6 कैमरे, जंबो के साथ 9**। यह सीमा केवल वायर और फ्लोर की एक विशेषता है — यह**फ्रेम के आकार से स्वतंत्र**है, इसलिए**बिनिंग और छोटे ROI से मदद नहीं मिलती** (वे प्रति *फ्रेम* बाइट्स को कम करते हैं, न कि GevSCPD-गति से बाइट्स प्रति *सेकंड*); एकमात्र उपाय हैं कम कैमरे, एंड-टू-एंड जंबो फ्रेम, या एक तेज़ NIC। लक्षण GVSP पैकेट हानि होगा, न कि एक सुचारू fps में कमी, इसलिए `analyze-array` प्राप्त-fps आंकड़ों को शून्य कर देता है और `**OVER-SUBSCRIBED**`, और `array-connect` प्रिंट करता हैपिन किए गए रिज़ॉल्यूशन के साथ  **कनेक्ट होने से इनकार करता है** (वॉक-डाउन अन्यथा फ्रेम को डाउन-बिन करता है, जो इस प्रकार की ब्लॉक को भी साफ़ नहीं करता है)। `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` इस इनकार को बेंच वर्क के लिए एक ज़ोरदार चेतावनी में डाउनग्रेड करता है — देखें [एनवायरनमेंट वेरिएबल्स](#पर्यावरण-चरों).

### एरे की स्वास्थ्य — कौन सा सबसिस्टम फ्रेम खो रहा है

एक जुड़े हुए एरे का `GET /api/camera/array/<array_id>/capability` एक लाइव
`health` ब्लॉक, जिसे एक चलती **10-सेकंड** विंडो पर फिर से आंका जाता है। यह फ्रेम हानि को दो कारणों में विभाजित करता है जिनके लिए विपरीत समाधानों की आवश्यकता होती है, बजाय इसके कि वह एक &quot;अपूर्ण&quot; दर रिपोर्ट करे जो दोनों में से किसी का भी नाम न ले:

| फ़ील्ड | इसका क्या मतलब है | कौन सा सबसिस्टम |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (प्रति सीरियल) | फ्रेम **पहुँच गया और संरचनात्मक रूप से खराब था**— GVSP पैकेट हानि। |**नेटवर्क**: वायर बजट, पेसिंग, NIC RX रिंग, MTU |
| `never_arrived_rate_pct` (प्रति सीरियल) | फ्रेम **कभी आया ही नहीं**— कैमरा फायर नहीं हुआ, या कुछ भी उससे बाहर नहीं गया। |**ट्रिगर / सिंक**: M8 केबल, `--line`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | प्रत्येक के लिए सबसे खराब कैमरा की दर। | — |
| `per_cam_rate_pct` | प्रति कैमरा संयुक्त अपूर्ण दर (दोनों कारण एक साथ)। | — |
| `stable_for_seconds` | प्रत्येक कैमरा 0.01 % से नीचे कितनी देर तक रहा. | — |

5% से ऊपर होने पर बैकएंड एक `[array-health <id>] WARN` लाइन लॉग करता है जो स्प्लिट का नाम बताती है — पहली बार उल्लंघन होने पर, गंभीरता-बैंड में बदलाव पर, जब तक यह बना रहता है तब तक हर मिनट एक बार, और जब यह ठीक हो जाता है तो एक बार। दूषित आधा हिस्सा  प्रिंट करता हैप्रत्येक कैमरा और कारण पर पहली हिट पर 16, फिर हर 60 सेकंड में एक रोल-अप। प्रत्येक मूल्यांकन अभी भी बैकएंड लॉग फ़ाइल में दर्ज होता है; काउंटर्स प्रिंट होने के बावजूद हर बफ़र पर आगे बढ़ते रहते हैं।

वही रिकॉर्ड उस संख्या की रिपोर्ट करता है जिससे पूरी आवंटन जुड़ी होती है:

| फ़ील्ड | इसका क्या मतलब है |
| --- | --- |
| `wire_ceiling_mbps` | होस्टसस्टेन्ड वायर बजट प्रचलन में, एमबी/सेकंड। |
| `wire_ceiling_source` | वह संख्या कहाँ से आई, शब्दों में — जैसे `USB-capped 200 MB/s (was theoretical 1062; PnPDeviceID=USB\VID_0BDA&PID_815A)` या `user override 120 MB/s (auto said 200)`। |
| `wire_ceiling_is_user_set` | `true` जब `--wire-ceiling-mbps` (या GUI का **Wire Budget** फ़ील्ड) सेट करें। |
| `nic_is_usb` | `true` एक USB ईथरनेट एडाप्टर के लिए — ऊपर दिया गया 200 MB/s का कैप देखें। |

**इसे पढ़ना:** शून्य-नहीं `gvsp_corrupt_rate_pct` के साथ `never_arrived_rate_pct` पर 0 का मतलब है कि ट्रिगरिंग और केबल सिंक एकदम सही हैं और 100 % नुकसान नेटवर्क पर है
path — `--wire-ceiling-mbps` को कम करें और फिर से कनेक्ट करें। इसके विपरीत पैटर्न सिंक केबल या ट्रिगर लाइन की ओर इशारा करता है।

> **`--target-fps` खराब फ्रेमों के लिए जिम्मेदार नहीं है।** GevSCPD पेसिंग लिखी गई है
> कनेक्ट होने पर एक बार, इसलिए ट्रिगर दर को कम करने से ड्यूटी साइकिल बदलता है, न कि
> समवर्ती-उत्सर्जन बर्स्ट दर। मापी गई 5× मांग कटौती से कोई सुधार नहीं हुआ;
> वायर सीलिंग को 240 से 200 एमबी/सेकंड तक कम करने से वही रिग 10.4 % से
> भ्रष्ट होकर 0.00 % पर ला दिया।

> **TRI032S फर्मवेयर पर मिड-स्ट्रीम ऑटो-श्रिंक उपलब्ध नहीं है।** एक चल रहा एरे
> स्वयं इसे ठीक नहीं कर सकता; डिस्कनेक्ट करें और फिर से कनेक्ट करें ताकि कनेक्ट-टाइम पिकर
> नई सीमा के साथ फिर से योजना बना सके।

### लक्षण → समाधान

| लक्षण (एरे सेटिंग्स / कनेक्ट / `analyze_array_network`) | कारण | समाधान |
| --- | --- | --- |
| `FRAMES WILL DROP … exceeds NIC RX ring usable capacity 0.26 MB`, `Reduce ROI to enable` | `ReceiveBufferLen` को 32 पर रीसेट करें (आमतौर पर ड्राइवर अपडेट के बाद) | `ReceiveBufferLen`→256 सेट करें, `PendingReceives`→64; पैनल को फिर से खोलें (यदि बैकएंड ने पुराने रिंग आकार को कैश किया है तो उसे रीस्टार्ट करें) |
| रीस्टार्ट/शटडाउन अटक जाता है; बाद में `Invalid class` WMI त्रुटियाँ, NIC सक्षम नहीं हो रहा है, USB ड्राइव गायब हैं | पुराना 2016 Realtek USB 10GbE ड्राइवर → BSOD `0x9F` → जबरन पावर-ऑफ | एडाप्टर ड्राइवर को ≥ v10.67 (2026) में अपडेट करें, फिर ऊपर दी गई रिसीव-रिंग सेटिंग्स को फिर से लागू करें |
| कनेक्ट सफल हो जाता है लेकिन सब-नेटिव रिज़ॉल्यूशन लौटाता है | स्मार्ट-प्रैप ने वायर में फिट होने के लिए फ्रेम को ऑटो-श्रैंक कर दिया | लिंक को अपग्रेड करें / संकुचन स्वीकार करें / `--force-tier slip-emit-and-capture` |
| एरे एक स्वस्थ टारगेट fps रिपोर्ट करता है लेकिन एक अंश प्रदान करता है उसका एक अंश; `health.gvsp_corrupt_rate_pct` गैर-शून्य, `never_arrived_rate_pct` 0 | होस्ट का अनुमानित वायर बजट वास्तव में वहन की जाने वाली क्षमता से अधिक बताता है (USB ईथरनेट एडाप्टर, एक पतली PCIe लेन पर आम, या एक साझा फैब्रिक) | एक कम `--wire-ceiling-mbps` के साथ फिर से कनेक्ट करें और स्वास्थ्य ब्लॉक को फिर से जांचें। **नहीं** `--target-fps` — GevSCPD पेसिंग कनेक्ट पर तय होती है |
| प्रकाशित समूहों से कैमरे अनुपस्थित हैं; `health.never_arrived_rate_pct` गैर-शून्य, `gvsp_corrupt_rate_pct` 0 | ट्रिगर / सिंक पथ — कैमरे फायर नहीं हो रहे हैं, नेटवर्क की समस्या नहीं है | M8 सिंक केबल और `--line` की जाँच करें; पुष्टि करें कि हर सदस्य आर्म्ड है (`TriggerMode=On`) |
| `**OVER-SUBSCRIBED**` / `Wire budget` का `analyze-array` में उल्लंघन, या पिन किए गए रिज़ॉल्यूशन (`array over-subscribes the wire`) के साथ कनेक्ट करने से इनकार | Aggregप्रति-कैम मांग (8 MB/s न्यूनतम × N कैमरे) कोलैज़न-सेफ वायर सीमा से अधिक हो जाती है — 1 GbE @1500 MTU पर 6 कैमरे फुल-रेज़ में, जंबो के साथ 9 | कम कैमरे, एंड-टू-एंड जंबो फ्रेम, या एक तेज़ NIC। **ROI/बिनिंग मदद नहीं करेगा** (सीमा फ्रेम-आकार-स्वतंत्र है)। `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` बेंच पर ओवरराइड करता है (पैकेट हानि को स्वीकार करता है) |

---

## `chloros-cli daq`

स्पेक्ट्रल-सेंसर कमांड। दो क्लास:
- **`pool-*`**— पतले HTTP क्लाइंट जो बैकएंड के पर्सिस्टेंट पूल के माध्यम से सेंसर को चलाते हैं।**यह समर्थित पाथ है, और शिप किए गए CLI में मौजूद एकमात्र पाथ है।** बैकएंड ट्रांसपोर्ट का मालिक है, इसलिए GUI, CLI और SDK स्क्रिप्ट सभी सीरियल पोर्ट के लिए प्रतिस्पर्धा करने के बजाय एक ही लाइव हैंडल साझा करते हैं।
- **बाकी सब कुछ**(`test`, `record`, `live`, `stream`, `connect`, `info`, `net`, `ota`, `sample-rate`, `calibrate`, `serve`, `ws`, `udp`, `mqtt`, `reflectance`, `login`, `logout`, `status`) — प्रत्यक्ष हार्डवेयर एक्सेस, पूर्णता के लिए नीचे दस्तावेजीकृत। इनके लिए `daq` Python पैकेज की आवश्यकता है, जो**किसी भी शिप किए गए आर्टिफैक्ट में शामिल नहीं है**: संकलित CLI इसे बाहर रखता है (`scripts/Build-CLI.ps1` `--nofollow-import-to=daq` सेट करता है, और ट्रांसपोर्ट `pyserial` / `bleak` / `zeroconf` इसके साथ होते हैं), और PyPI SDK पैकेज ऐसा नहीं करता इसे भी शामिल नहीं करते। वे केवल एक सोर्स चेकआउट से ही चलते हैं, इसलिए उन्हें किसी चीज़ को पाने के लिए इस्तेमाल करने के बजाय एक MAPIR-आंतरिक विकास पथ के रूप में मानें।
- **`discover` / `list`** दोनों को जोड़ते हैं: वे सीधे हार्डवेयर कमांड हैं एक स्रोत चेकआउट से होते हैं, लेकिन एक शिप किए गए बिल्ड पर वे `pool-discover` पर वापस चले जाते हैं और बैकएंड स्कैन करता है। इसलिए स्कैनिंग हर जगह काम करती है — जो मायने रखता है क्योंकि यह DAQ-M के BLE MAC को जानने का एकमात्र तरीका है।

> **`chloros-cli daq --help`** (और `-h` / `help`) `pool-*` उप-कमांडों को सूचीबद्ध करता है — मदद जानबूझकर पूल क्लाइंट पर भेजी जाती है ताकि यह उन कमांड्स को दर्शाए जो वास्तव में चलते हैं। यदि आप किसी शिप किए गए बिल्ड पर एक डायरेक्ट-हार्डवेयर उप-कमांड चलाते हैं, तो यह एक स्पष्ट त्रुटि के साथ समाप्त हो जाता है जो गायब पैकेज का नाम बताती है और आपको वापस  की ओर निर्देशित करती है।0785; कुछ भी चुपचाप विफल नहीं होता है। (`discover` / `list` अपवाद हैं — वे `pool-discover` पर पुनर्निर्देशित करते हैं और बस काम कर जाते हैं।)
>
> **एक ग्राहक को जो कुछ भी चाहिए वह सब `pool-*` के माध्यम से पहुँच योग्य है** — कनेक्ट, स्ट्रीम, कैलिब्रेटेड `.daq` फ़ाइलों को रिकॉर्ड करें, और कैप प्रोफाइल स्वैप करें। DAQ को `chloros_sdk.connect_daq_sensor()` के साथ Python से भी चलाया जा सकता है, जो उसी पूल्ड पाथ का उपयोग करता है।

### DAQ सेंसर फर्स्ट-कनेक्ट वर्कफ़्लो

```bash
# 1. Smart-detect any DAQ on this machine (Ethernet → BLE → USB precedence)
chloros-cli daq connect

# 2. Detailed scan: every transport, showing the address to connect with.
#    This is how you find a DAQ-M's BLE MAC — unlike a DAQ-E hostname or a
#    DAQ-U COM port, a MAC isn't printed on the device or listed by the OS.
chloros-cli daq discover                      # or: daq pool-discover
chloros-cli daq discover --only ble           # BLE only
chloros-cli daq discover --json               # machine-readable

# 3. Open a persistent pool session (handle stays alive across CLI calls)
chloros-cli daq pool-connect           # smart-detect
chloros-cli daq pool-connect --port COM3                       # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF           # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-xxx.local        # DAQ-E by hostname

# 4. List what's in the pool, including the sensor_id you'll use next
#    (DAQ-U ids look like 'CB-7C-A8-2E-5F'; DAQ-E ids like 'daq-e-def330')
chloros-cli daq pool-list

# 5. Read the latest spectrum frame
chloros-cli daq pool-latest --sensor-id CB-7C-A8-2E-5F

# 6. Record a calibrated .daq file for 60s
chloros-cli daq pool-record --sensor-id CB-7C-A8-2E-5F --duration 60 \
  -o ~/Documents/spectra --device-name "field-A"

# 7. Release
chloros-cli daq pool-disconnect --sensor-id CB-7C-A8-2E-5F
```

### `pool-*` संदर्भ

| उप-कमांड | उद्देश्य |
| --- | --- |
| `daq pool-connect` (smart-detect) | बैकएंड पूल में एक सेंसर खोलें। |
| `daq pool-connect --port PORT` | एक विशिष्ट सीरियल पोर्ट पर DAQ-U। |
| `daq pool-connect --ble` | BLE पर DAQ-M, MAC स्वतः स्कैन किया गया। |
| `daq pool-connect --mac MAC` | ज्ञात MAC पर BLE के माध्यम से DAQ-M (`--ble` का अर्थ)। |
| `daq pool-connect --eth-host HOST` | ज्ञात होस्ट पर ईथरनेट पर DAQ-E। |
| `daq pool-connect --eth` | ईथरनेट पर DAQ-E, होस्ट स्वतः खोजा गया (mDNS + ARP फॉलबैक; Windows और Linux पर खाली ARP कैश से काम करता है)। |
| `daq pool-connect --integration-time MS --frame-avg N --no-ae` | ट्यून इंटीग्रेशन विंडो / AE स्थिति। |
| `daq pool-connect --no-stream` | कनेक्ट करें लेकिन अभी स्ट्रीमिंग शुरू न करें (`pool-stream --start` के साथ फिर से शुरू करें)। |
| `daq pool-connect --cap-id {none, fov_15, fov_30, fov_45, fov_60, fov_90, sunshine_cosine}` | कैप-करेक्शन प्रोफ़ाइल। बैकएंड पर डिफ़ॉल्ट `sunshine_cosine` है। |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | कनेक्ट किए बिना, उन सेंसरों के लिए हर ट्रांसपोर्ट को स्कैन करें जिनसे आप जुड़ सकते हैं। **इस तरह आप एक DAQ ढूंढते हैं-M का BLE MAC।** `daq discover` / `daq list` शिप किए गए बिल्ड्स में स्वचालित रूप से यहाँ राउट करते हैं। पूल में पहले से खुले सेंसर सूचीबद्ध नहीं होते हैं — एक कनेक्टेड DAQ-M विज्ञापन करना बंद कर देता है — इसलिए  का उपयोग करेंउनके लिए `pool-list` का उपयोग करें। |
| `daq pool-list` | बैकएंड पूल में हर सेंसर दिखाएँ। |
| `daq pool-disconnect --sensor-id ID [--all]` | रिलीज़ करें। |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | सबसे हालिया N स्पेक्ट्रम फ्रेम। |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | स्ट्रीमिंग फिर से शुरू / विराम करें। |
| `daq pool-record --sensor-id ID [--duration SEC] [--output DIR] [--device-name NAME] [--stop]` | एक . शुरू / रोकें।DAQ रिकॉर्डिंग शुरू/रोकें। |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | रनटाइम पर कैप-करेक्शन प्रोफ़ाइल स्वैप करें। |

### डायरेक्ट-हार्डवेयर सबकमांड (केवल सोर्स चेकआउट के लिए — शिप किए गए बिल्ड्स में नहीं)

> पूर्णता के लिए सूचीबद्ध। इनके लिए  की आवश्यकता होती है15 Python पैकेज के साथ `pyserial` / `bleak` / `zeroconf`, जिनमें से कोई भी संकलित CLI या PyPI SDK में शामिल नहीं है — वे केवल MAPIR स्रोत चेकआउट से ही चलते हैं। **यदि आप एक रिलीज़ किए गए Chloros बिल्ड का उपयोग कर रहे हैं, तो इसके बजाय ऊपर दिए गए `pool-*` कमांड का उपयोग करें**; वे कनेक्ट, स्ट्रीम, रिकॉर्ड और कैप चयन को कवर करते हैं।

```bash
chloros-cli daq test --port COM3                           # Verify connection
chloros-cli daq connect --eth                              # Smart-detect over ETH
chloros-cli daq info --eth-host daq-e-xxx.local            # Device summary as JSON
chloros-cli daq discover --only usb,ble --timeout 5        # Scan local interfaces
chloros-cli daq list                                       # Alias of discover
# ^ discover/list are the exception in this section: in a shipped build they
#   fall back to `pool-discover` (the backend does the scan), so they work
#   without a source checkout. The only difference is that the fallback needs
#   the Chloros backend running, as all pool-* commands do.

# Streaming JSON Lines to stdout (pipeable)
chloros-cli daq stream --port COM3 --format jsonl --photometrics

# Record to .daq for 60 seconds
chloros-cli daq record --port COM3 --duration 60 -o ~/Documents/spectra/

# Live spectrum visualization in a window
chloros-cli daq live --port COM3 --record

# Dual-sensor reflectance (ambient + object) → JSON Lines
chloros-cli daq reflectance \
  --ambient-eth-host daq-e-field.local \
  --object-eth-host daq-e-canopy.local \
  --record -o ~/Documents/reflectance/

# Convenience: pick integration_time + frame_avg for a target rate
chloros-cli daq sample-rate --port COM3 --target-hz 5

# Calibration profile management
chloros-cli daq calibrate --port COM3 --list
chloros-cli daq calibrate --port COM3 --set field_calibration_2026_05

# DAQ-E network config (mDNS auto-discovers the host)
chloros-cli daq net --eth-host daq-e-xxx.local set-ip --mode static --ip 192.168.2.20
chloros-cli daq net --eth-host daq-e-xxx.local set-name "sky-sensor"
chloros-cli daq net --eth-host daq-e-xxx.local set-ptp --enabled true --domain 0
chloros-cli daq net --eth-host daq-e-xxx.local set-auto-stream true          # auto-stream on boot
chloros-cli daq net --eth-host daq-e-xxx.local set-require-signature         # require factory-signed cal (fw v1.6.0+; refused while the held cal is unsigned)
chloros-cli daq net --eth-host daq-e-xxx.local set-time                      # push host clock (refused when PTP SLAVE)
chloros-cli daq net --eth-host daq-e-xxx.local set-auth-token --current "" --new "s3cret"   # control-channel auth ("" new = disable)
chloros-cli daq net --eth-host daq-e-xxx.local set-ota-password "newpass"    # change OTA password (min 4 chars)
chloros-cli daq net --eth-host daq-e-xxx.local factory-reset                 # clear all NVS settings and reboot
chloros-cli daq net --eth-host daq-e-xxx.local reboot

# OTA firmware update
chloros-cli daq ota --eth-host daq-e-xxx.local \
  --firmware daq_e_1.21.bin --password mapir-daq-e

# Bridge spectra to other protocols
chloros-cli daq serve --port COM3 --tcp-port 9000           # TCP JSON-lines
chloros-cli daq ws    --port COM3 --ws-port 9001            # WebSocket
chloros-cli daq udp   --port COM3 --udp-port 9002           # UDP broadcast
chloros-cli daq mqtt  --port COM3 --broker mqtt.example.com --topic daq/spectrum
```

---

## `chloros-cli project`

एक सहेजे गए Chloros प्रोजेक्ट (`cameras.json` + `sensors.json` + `project.json` वाला एक फ़ोल्डर) को खोलें, कनेक्ट करें और चलाएँ। सब कुछ बैकएंड के माध्यम से जाता है ताकि GUI और CLI समान हार्डवेयर स्थिति उत्पन्न करें।

### उप-कमांड संदर्भ

| उप-कमांड | उद्देश्य |
| --- | --- |
| `project open PATH` | प्रोजेक्ट का डिवाइस मैनिफेस्ट (कैमरे, ऐरे, सेंसर) प्रिंट करें। |
| `project devices PATH [--reconnect]` | डिस्कवरी सूचीबद्ध करें या फिर से चलाएँ। |
| `project connect PATH [--cameras-only] [--sensors-only]` | हर सहेजे गए कैमरा / एरे / सेंसर को कनेक्ट करें। |
| `project capture PATH NAME [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | नामित कैमरा या एरे से एकल कैप्चर। |
| `project burst PATH NAME [-n N] [-i S] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--prefix P]` | नामित कैमरा या एरे से एन-फ्रेम बर्स्ट (`-n/--count` डिफ़ॉल्ट 5; `-i/--interval` फ्रेमों के बीच सेकंड, डिफ़ॉल्ट 0). एरे बर्स्ट दोहराए गए सिंक किए गए समूहों को डुप्लिकेशन-मुक्त करता है (स्टेलनेस वॉचडॉग) ताकि एक आंशिक-साइकिलिंग एरे एक फ्रेम की N प्रतियां वापस नहीं कर सकता; प्रत्येक पुनरावृत्ति के परिणाम प्रिंट करता है। |
| `project stream PATH NAME [-n N] [--fps F] [-o DIR] [--format FMT] [--exposure US] [--gain DB] [--poll-interval S]` | बैकएंड जॉब के माध्यम से स्ट्रीम-टू-डिस्क। `--poll-interval` = `/stats` पोल के बीच सेकंड (डिफ़ॉल्ट 2.0)। |
| `project sensor read PATH NAME [--json]` | नवीनतम स्पेक्ट्रम फ्रेम। |
| `project sensor log PATH NAME --seconds SEC [-o DIR] [--device-name NAME]` | रिकॉर्ड .daq। |
| `project run PATH RECIPE.yaml` | एक YAML/JSON कैप्चर रेसिपी निष्पादित करें। `--dry-run` बिना चलाए मान्य करता है। |
| `project align calibrate PATH NAME [--method M] [--model M] [--frames N] [--reference SN] [--max-features N] [--ratio-threshold F] [--ransac-threshold-px F] [--min-matches N] [--max-reproj-err-px F] [--checkerboard RxC] [--name PROFILE]` | एक एरे के लिए संरेखण की गणना करें — [नीचे दी गई फ़्लैग तालिका देखें](#project-align-calibrate-options)। |
| `project align status PATH NAME [--json]` | वर्तमान संरेखण प्रोफ़ाइल प्रिंट करें। |
| `project align clear PATH NAME` | कैश्ड प्रोफ़ाइल को हटाएँ। |
| `project align tweak PATH NAME --serial SN --dx N --dy N --rotation-deg N --scale N` | एक स्लेव के ट्रांसफ़ॉर्म को नudge करें। |
| `project align export PATH NAME --to FILE` | प्रोफ़ाइल को JSON में सहेजें। |
| `project align import PATH NAME --from FILE [--no-validate]` | सहेजी गई प्रोफ़ाइल लोड करें। |

#### `project align calibrate` विकल्प

| फ़्लैग | डिफ़ॉल्ट | विवरण |
| --- | --- | --- |
| `--method {feature_orb, feature_akaze, phase_correlation, checkerboard, manual}` | `feature_orb` | संरेखण विधि। **ये वर्तनी `lattice align-calibrate` से भिन्न हैं**, जो संक्षिप्त रूप `orb` / `akaze` / `phase` लेता है; इस फ़्लैग पर ये दोनों कमांड परस्पर प्रतिस्थापन योग्य नहीं हैं। |
| `--model {translation, rigid, affine, homography}` | `affine` | मॉडल को फिट करने के लिए रूपांतरित करें। |
| `--frames N` | `1` | सिंक किए गए फ्रेम स्नैपऔसत तक हॉट्स। |
| `--reference SN` | मास्टर | संदर्भ कैमरा सीरियल; प्रत्येक अन्य सदस्य को उस पर विकृत किया जाता है। |
| `--max-features N` | `5000` | ORB फ़ीचर-काउंट कैप. |
| `--ratio-threshold F` | `0.75` | लोवे का अनुपात परीक्षण. |
| `--ransac-threshold-px F` | `3.0` | RANSAC इनलायर थ्रेशोल्ड। |
| `--min-matches N` | `15` | **क्वालिटी गेट** — इस संख्या से कम इनलायर मैच होने पर समाधान अस्वीकार करें। |
| `--max-reproj-err-px F` | `4.0` | **क्वालिटी गेट** — इस RMS पुनःप्रक्षेपण त्रुटि से अधिक होने पर समाधान अस्वीकार करें। |
| `--checkerboard RxC` | — | `--method checkerboard` के लिए बोर्ड ज्यामिति, जैसे `9x6`। |
| `--name PROFILE` | खाली | सहेजे गए JSON में एम्बेड किया गया प्रोफ़ाइल नाम। **एरे का नाम नहीं** — यानी स्थिति संबंधी `NAME`। |

दो क्वालिटी गेट्स ही कारण हैं कि एक कैलिब्रेट समाधान करने में सफल हो सकता है और फिर भी सेव करने से इनकार कर सकता है: कोई प्रोफ़ाइल जो इनमें से किसी एक में भी विफल हो जाती है, वह हर बाद की कैप्चर को चुपचाप ग़लत तरीके से रजिस्टर कर देगी, इसलिए इसे पर्सिस्ट करने के बजाय अस्वीकार कर दिया जाता है।

### उदाहरण

```bash
# Open a project and see what it knows about
chloros-cli project open "/home/user/Chloros Projects/Field_A"

# Connect everything saved in the project
chloros-cli project connect "/home/user/Chloros Projects/Field_A"

# Capture from a named camera (defined in cameras.json)
chloros-cli project capture "/home/user/Chloros Projects/Field_A" FrontLeft \
  -o output/ --format tiff

# Capture from a named array
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  -o output/ --format tiff

# Capture with overrides
chloros-cli project capture "/home/user/Chloros Projects/Field_A" main_rig \
  --exposure 5000

# Read a spectrum
chloros-cli project sensor read "/home/user/Chloros Projects/Field_A" Sky --json

# Record a DAQ log
chloros-cli project sensor log "/home/user/Chloros Projects/Field_A" Sky \
  --seconds 120 -o ~/Documents/spectra/

# Align an array (live)
chloros-cli project align calibrate "/home/user/Chloros Projects/Field_A" main_rig
chloros-cli project align status "/home/user/Chloros Projects/Field_A" main_rig

# Run a recipe
chloros-cli project run "/home/user/Chloros Projects/Field_A" recipe.yaml
```

### रेसिपी डीएसएल

`project run RECIPE.yaml` एक YAML या JSON फ़ाइल स्वीकार करता है जो क्रियाओं की एक श्रृंखला का वर्णन करती है:

```yaml
# recipe.yaml
overrides:
  cameras:
    FrontLeft:
      exposure_us: 5000
      target_brightness: 80

stop_on_error: true
actions:
  - apply:
      name: FrontLeft
      settings:
        exposure_auto: "Off"
        gain: 6.0
        gain_auto: "Off"
  - wait: 2s
  - capture:
      name: FrontLeft
      output: pose_a/
      format: tiff
  - stream:
      name: main_rig
      count: 60
      fps: 5
      output: stream/
  - burst:
      name: main_rig
      count: 10
      interval: 0.5
      output: burst_a/
      format: tiff
  - sensor:
      name: Sky
      action: read
```

समर्थित क्रियाएँ: `apply`, `wait`, `capture`, `stream`, `burst`, `sensor`। `burst` क्रिया `name` (आवश्यक), `count` लेती है (डिफ़ॉल्ट 5), `interval` (सेकंड, डिफ़ॉल्ट 0), `output`, `format`, और `settings` (`apply` के समान प्रति-कैमरा सेटिंग्स का आकार); ऐरे बर्स्ट्स `project burst` की तरह ही वही फ्रेश-सिंकड-ग्रुप वॉचडॉग का उपयोग करते हैं।

इसे चलाएँ:

```bash
chloros-cli project run "/path/to/project" recipe.yaml

# Dry-run to validate without firing hardware
chloros-cli project run "/path/to/project" recipe.yaml --dry-run
```

---

## पर्यावरण चर

| चर | प्रभाव |
| --- | --- |
| `CHLOROS_BACKEND_URL` | बैकएंड को ओवरराइड करें URL (डिफ़ॉल्ट `http://127.0.0.1:5000`) — **केवल `lattice`, `project` द्वारा ही मान्य किया जाता है, और `daq pool-*` कमांड परिवारों द्वारा ही मान्य है।** कोर कमांड (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) पिन `http://127.0.0.1:<port>` और अनदेखा करें इस चर (IPv4 लिटरल साइडस्टेप्स द Windows `localhost`→`::1` ~2 s-per-request पेनल्टी) को, इसलिए वे हमेशा लोकल मशीन को टारगेट करते हैं। |
| `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED` | `1` एरे ओवर-सब्सक्रिप्शन कनेक्ट इनकार (प्रति-कैम कुल मांग &gt; `pin_resolution` के साथ कोलैजन-सेफ वायर सीमा) को एक जोरदार चेतावनी-और-आगे-बढ़ें स्थिति में डाउनग्रेड करता है, जिसमें GVSP पैकेट हानि स्वीकार की जाती है। बेंच उपयोग केवल — देखें [एरे fps और बस्ट मॉडल](#array-fps--burst-model)। |
| `CHLOROS_CLI_MODE` | स्वयं CLI द्वारा सेट किया जाता है; बैकएंड को समानांतर प्रसंस्करण सक्षम करने के लिए बताता है। |
| `CHLOROS_GVSP_PROBE_FALLBACK` | `0` GVSP फॉलबैक प्रोब को छोड़ता है (केवल ICMP परिणाम)। **यह जंबो को बंद कर देता है, यह सिर्फ लॉग को शांत नहीं करता है** — कैमरा हर पथ पर केवल 1500 तक DF पिंग का जवाब देता है, इसलिए यह प्रोब ही एकमात्र चीज़ है जो जंबो का पता लगा सकती है। यह प्रति कैमरा प्रति कनेक्ट ~1 s बचाता है; यदि नेटवर्क जंबो ले जा सकता था तो इसका खर्च ~1.45× वायर सीलिंग होता है। जब आप इसे सेट करते हैं तो SDK चेतावनी देता है। |
| `CHLOROS_GVSP_PACKET_SIZE_FORCE` | जीवीएसपी (GVSP) पैकेट आकार को N बाइट्स पर पिन करता है; प्रोबिंग को पूरी तरह से छोड़ देता है। प्रति-कमांड का उपयोग करना पसंद करें (`CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 chloros-cli …`) को स्थायी रूप से सेट करने पर: एक पिन किया गया आकार सामने के नेटवर्क के अनुसार अनुकूलन करना बंद कर देता है, और एक ऐसे पथ पर 9000 को पिन करना जो जंबो नहीं ले जा सकता, **हर** कैप्चर को `SC_ERR_TIMEOUT -1011` के साथ टाइम आउट करा देता है।. |
| `TMPDIR` (Linux) | Nuitka onefile एक्सट्रैक्शन डायरेक्टरी को ओवरराइड करें। CLI यदि मौजूद हो तो `/mnt/ssd/tmp` का स्वचालित रूप से उपयोग करता है। |

---

## एग्जिट कोड

| कोड | अर्थ |
| --- | --- |
| `0` | सफलता। |
| `1` | सामान्य विफलता (अधिकांश उप-कमांड त्रुटियाँ)। |
| `2` | तर्क त्रुटि। |
| `130` | Ctrl+C द्वारा बाधित। |

---

## समस्या निवारण संकेत

- **&quot;लॉगिन आवश्यक&quot;** → इस मशीन पर एक बार `chloros-cli login EMAIL PASSWORD` चलाएँ।
- **&quot;बैकएंड अविश्वसनीय&quot;** → Chloros डेस्कटॉप ऐप शुरू करें, या बैकएंड बाइनरी को सीधे चलाएँ (`chloros-backend`), या यदि रिमोट है तो `CHLOROS_BACKEND_URL` की जाँच करें।
- **`lattice` कमांड &quot;LATTICE कैमरा ड्राइवर नहीं मिले&quot;** → एरेना SDK रनटाइम इंस्टॉल नहीं है; CLI के साथ `win32api` Windows पर बंडल आता है लेकिन C रनटाइम GUI इंस्टॉलर का हिस्सा है।
- **एरे कनेक्ट / एरे सेटिंग्स &quot;FRAMES WILL DROP&quot; या &quot;सक्षम करने के लिए ROI कम करें&quot; दिखाता है** → होस्ट NIC रिसीव रिंग बहुत छोटी है (आमतौर पर NIC ड्राइवर अपडेट के बाद 32 पर रीसेट हो जाती है)। [होस्ट NIC सेटअप और ट्यूनिंग](#host-nic-setup--tuning-lattice-arrays) देखें — `ReceiveBufferLen=256`,  सेट करें23.
- **मशीन रीस्टार्ट/शटडाउन पर हैंग हो जाती है, फिर WMI `Invalid class` / NIC सक्षम नहीं होती / USB ड्राइव्स गायब हैं** → पुराना USB 10GbE एडाप्टर ड्राइवर `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`) का कारण बन रहा है। एडाप्टर ड्राइवर को अपडेट करें — देखें [Host NIC Setup &amp; Tuning](#host-nic-setup--tuning-lattice-arras).
- **जेसन स्वैप चेतावनी** → फ़ाइल-समर्थित स्वैप जोड़ें; CLI सटीक `fallocate` / `swapon` कमांड प्रिंट करता है।
- **DAQ डायरेक्ट कमांड्स अनुपस्थित** → अपेक्षित: शिप किए गए `chloros-cli` में जानबूझकर `daq` पैकेज को बाहर रखा गया है, इसलिए केवल `pool-*` मौजूद है (PyPI SDK में भी यह शामिल नहीं है)।  का उपयोग करें00932, जो बैकएंड के माध्यम से उसी सेंसर को ड्राइव करता है, या Python से `chloros_sdk.connect_daq_sensor()`।

---

## यह भी देखें

- [Python SDK संदर्भ](sdk-reference.md) — हर CLI कमांड का प्रोग्रामैटिक समकक्ष।
- [DAQ सेंसर गाइड](../daq/README.md) — सेंसर-विशिष्ट वायरिंग + कैलिब्रेशन।
- ऑनलाइन दस्तावेज़: `https://mapir.gitbook.io/chloros/cli`
