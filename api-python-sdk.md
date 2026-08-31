# API : Python SDK

{% hint style="info" %}
**पूर्ण API की तलाश में हैं?** यह पेज एक हैंड्स-ऑन ट्यूटोरियल है। हर पब्लिक क्लास, मेथड, सटीक सिग्नेचर, और कॉपी-पेस्ट करने योग्य उदाहरण [SDK Reference](reference/sdk-reference.md) में है, जो AI असिस्टेंट्स के लिए अनुकूलित है।**AI असिस्टेंट के साथ काम कर रहे हैं?** इस URL को चैट में पेस्ट करें ताकि उसके पास पूरा, वर्तमान Chloros 1.2.0 API हो:

`https://mapir.gitbook.io/chloros/reference/sdk-reference.md`

इस मैनुअल का हर पेज उसके लोअरकेस स्लగ్ + `.md` पर कच्चे मार्कडाउन के रूप में उपलब्ध है, और पूरा मैनुअल `https://mapir.gitbook.io/chloros/llms.txt` पर इंडेक्स किया गया है।
{% endhint %}

**Chloros Python SDK** (PyPI पर `chloros-sdk`) Python से डेस्कटॉप ऐप द्वारा की जा सकने वाली हर चीज़ को संचालित करता है: बैच इमेज प्रोसेसिंग, लाइव LATTICE कैमरा और एरे नियंत्रण, DAQ लाइट-सेंसर सत्र, और सहेजे गए-प्रोजेक्ट ऑटोमेशन। यह उसी लोकल बैकएंड के ऊपर एक पतली परत है जिसका उपयोग GUI और CLI करते हैं (HTTP on `127.0.0.1:5000`), इसलिए तीनों सतहों पर व्यवहार एक जैसा है।

## इंस्टॉल करें

इंस्टॉलेशन दो चरणों में होता है: पहले Chloros डेस्कटॉप पैकेज (यह प्रोसेसिंग बैकएंड और हार्डवेयर रनटाइम प्रदान करता है), फिर Python पैकेज।

**चरण 1 — Chloros इंस्टॉल करें।** Windows: [डाउनलोड](download.md) पृष्ठ से डेस्कटॉप इंस्टॉलर (डिफ़ॉल्ट पथ `C:\Program Files\MAPIR\Chloros\`) चलाएँ। Linux: `.deb` पैकेज स्थापित करें ([Linux इंस्टॉलेशन](linux/linux-installation.md)).**चरण 2 — SDK इंस्टॉल करें** (Python 3.7+):

```bash
pip install chloros-sdk
```

आपको pip की भी आवश्यकता नहीं हो सकती है: हर इंस्टॉलर एक मिलान करने वाला SDK व्हील के साथ आता है। Windows इंस्टॉलर इसे आपके सिस्टम में स्वचालित रूप से इंस्टॉल कर देता है Python; Linux `.deb` इसे `/usr/lib/chloros/sdk/` पर रखता है और सटीक `pip install --user` कमांड प्रिंट करता है। रिलीज़ बिल्ड पर PyPI अपडेट किया जाता है, इसलिए `pip install chloros-sdk` नवीनतम स्थिर रिलीज़ से मेल खाता है।

**चरण 3 — प्रति मशीन एक बार लॉग इन करें:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

क्रेडेंशियल `~/.chloros/` में कैश किए जाते हैं (दोनों प्लेटफ़ॉर्म पर)। Windows पर आप इसके समकक्ष डेस्कटॉप ऐप के यूज़र <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> टैब के माध्यम से साइन इन कर सकते हैं। SDK के लिए एक सशुल्क Chloros+ प्लान की आवश्यकता होती है — नीचे [लाइसेंस आवश्यकता](#license-requirement) देखें।

| आवश्यकता | विवरण |
| --- | --- |
| **Chloros स्थापित** | Windows: डेस्कटॉप इंस्टॉलर; Linux: `.deb` पैकेज (बैकएंड बाइनरी प्रदान करता है) |
| **Python** | 3.7 या उच्चतर (3.10 पर विकसित/परीक्षित) |
| **ऑपरेटिंग सिस्टम** | Windows 10/11 64-बिट, उबंटू 22.04 LTS या नया, या NVIDIA Jetson (JetPack 6) |
| **लाइसेंस** | सक्रिय Chloros+ लॉगिन, कोई भी सशुल्क टियर (कॉपर या उच्चतर) |

## 60-सेकंड में जीत

एक कॉल एक प्रोजेक्ट बनाती है, एक फ़ोल्डर आयात करती है, प्रोसेसिंग कॉन्फ़िगर करती है, और पाइपलाइन चलाती है — यदि बैकएंड पहले से चल नहीं रहा है तो उसे स्वचालित रूप से शुरू करती है:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
)
```

(Linux पर, Linux पथों का उपयोग करें: `/home/user/drone_images/flight001`। SDK दोनों प्लेटफ़ॉर्म पर समान रूप से काम करता है।)

LATTICE कैप्चर फ़ोल्डर को प्रोसेस कर रहे हैं? LATTICE-अनुकूल रैपर का उपयोग करें — यह सही डिफ़ॉल्ट लागू करता है (कोई पैनल-टारगेट डिटेक्शन नहीं, मानक डेबायर):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — पूर्ण पाइपलाइन नियंत्रण

एक-लाइनर से परे किसी भी चीज़ के लिए, `ChlorosLocal` का उपयोग करें। यह पहली बार उपयोग करने पर बैकएंड (`auto_start_backend=True`) को स्पॉन करता है, प्रोजेक्ट बनाता और कॉन्फ़िगर करता है, प्रगति की निगरानी करता है, और रन के बाद का सारांश लौटाता है।

```python
ChlorosLocal(
    api_url="http://127.0.0.1:5000",   # backend URL (also: backend_url=)
    auto_start_backend=True,            # spawn backend if not running
    backend_exe=None,                   # override backend binary path
    timeout=30,                         # request timeout seconds
    backend_startup_timeout=60,         # backend boot timeout
    processing_timeout=14400,           # hard cap on process() (4 h)
    processing_stuck_timeout=1800,      # no-progress threshold (30 min)
)
```

{% hint style="info" %}
`localhost` को प्रतिस्थापित करने के बजाय डिफ़ॉल्ट `http://127.0.0.1:5000` रखें — Windows पर, `localhost` पहले `::1` में रिज़ॉल्व होता है और IPv4-केवल बैकएंड के खिलाफ प्रति अनुरोध ~2 सेकंड का खर्च आता है।
{% endhint %}

इसे गारंटीकृत सफ़ाई के लिए एक संदर्भ प्रबंधक के रूप में उपयोग करें:

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

`configure()` इन कीवर्ड्स को स्वीकार करता है: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation`, और `custom_settings`। मुख्य मान:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"                  # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
```

LATTICE-विशिष्ट नॉब्स (`input_level`, `radiometric_output`, `array_alignment*` परिवार) को उनके पूर्ण मान तालिकाओं के साथ [SDK संदर्भ](reference/sdk-reference.md#supported-values) में प्रलेखित किया गया है।.

### प्रगति देखना

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### पोस्ट-रन सारांश पढ़ना — और खाली रन पकड़ना

पूरा होने पर, `process()` बैकएंड का प्रोसेसिंग सारांश `result["summary"]` के रूप में संलग्न करता है। `summary["hints"]` में प्रत्येक प्रविष्टि किसी भी उल्लेखनीय बात को समझाने वाला एक पूरा वाक्य है — उदाहरण के लिए, किसी रन से शून्य आउटपुट क्यों मिला — और हर संकेत को एक Python `UserWarning` के रूप में भी फिर से प्रसारित किया जाता है, इसलिए खाली रन स्वयं निदान करते हैं, भले ही आप कभी भी dict की जाँच न करें:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**`process()` तब उत्पन्न नहीं होता जब एक रन कोई छवि उत्पन्न नहीं करता।** यह एकमात्र जगह है जहाँ SDK और CLI जानबूझकर अलग हैं: `chloros-cli process` &quot;उत्पादों का अनुरोध किया गया था, कोई नहीं लिखा गया&quot; को एक विफलता मानता है और गैर-शून्य पर बाहर निकलता है, जबकि SDK सामान्य रूप से लौटता है और `summary` / संकेतों के माध्यम से स्थिति की रिपोर्ट करता है। यदि आपकी पाइपलाइन को एक खाली रन पर रुकना चाहिए, तो स्वयं इसकी जाँच करें — किसी अपवाद पर निर्भर रहने के बजाय `summary` की जाँच करें (या प्रोजेक्ट फ़ोल्डर के अंतर्गत फ़ाइलों को गिनें)।
{% endhint %}

## स्मार्ट कनेक्ट — लाइव हार्डवेयर

तीन हेल्पर बैकएंड के हार्डवेयर पूल में स्थायी सत्र खोलते हैं — यह वही पूल है जिसका उपयोग GUI करता है, इसलिए SDK स्क्रिप्ट्स डेस्कटॉप ऐप के साथ सीरियल पोर्ट या नेटवर्क बैंडविड्थ के लिए संघर्ष किए बिना सह-अस्तित्व में रहती हैं। यदि कोई स्थानीय बैकएंड चल नहीं रहा है, तो तीनों स्वचालित रूप से एक स्थानीय बैकएंड शुरू करते हैं।

### सिंगल लैटिस कैमरा — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

### सिंक्रनाइज़्ड एरे — `connect_array`

`connect_array` मल्टी-कैमरा रिग्स के लिए अनुशंसित एंट्री पॉइंट है। यह GUI की तरह ही स्मार्ट-प्रैप फ्लो चलाता है: नेटवर्क विश्लेषण, सिंक-टियर ऑटो-पिक, PTP टाइम-सिंक, प्रति-कैमरा पिक्सेल-फॉर्मेट चयन, AE सीडिंग, और GPIO ट्रिगर आर्मिंग। **पहला सीरियल मास्टर होता है** (यह हार्डवेयर ट्रिगर पल्स को फायर करता है); बाकी स्लेव होते हैं।

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

ट्रिगर करने से पहले सभी कैमरों पर ऑटो-एक्सपोज़र के स्थिर होने की प्रतीक्षा करने के लिए किसी भी एरे कैप्चर में `smart=True` जोड़ें। कैप्चर मोड (सिंगल / कंटीन्यूअस / इंटरवल / फास्टेस्ट), रिकॉर्डर, बर्स्ट-टू-वीडियो, और एरे एलाइनमेंट के लिए, [SDK संदर्भ](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep) देखें।

### DAQ लाइट सेंसर — `connect_daq_sensor`

बिना किसी तर्क के, `connect_daq_sensor()` स्वचालित रूप से ट्रांसपोर्ट का पता लगाता है (प्राथमिकता: ईथरनेट → BLE → USB):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

प्रत्येक फ्रेम में 135-पॉइंट `spectrum` (कैलिब्रेट होने पर W/m²/nm), एक `is_saturated` फ्लैग, और CIE `x`, `y`, `z`. किसी विशिष्ट सेंसर या ट्रांसपोर्ट को पिन करने के लिए — कई नेटवर्क इंटरफ़ेस वाले होस्ट पर यह एक विश्वसनीय विकल्प है, जहाँ ईथरनेट ऑटो-डिस्कवरी पहली कोशिश में एक स्वस्थ DAQ-E को मिस कर सकती है — एक स्पष्ट संकेत पास करें:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

ध्यान दें कि कैप-करेक्शन प्रोफाइल (`cap_id`) **एक SDK** नॉब नहीं हैं — इसके बजाय उन्हें `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap` के माध्यम से चुनें।

### सहेजे गए प्रोजेक्ट — `open_project`

एक सहेजा गया Chloros प्रोजेक्ट अपने कनेक्टेड हार्डवेयर (`cameras.json` + `sensors.json` साथ ही `project.json`) को बनाए रखता है, और `chloros_sdk.open_project(path)` एक साथ सब कुछ फिर से कनेक्ट कर सकता है और डिवाइस के नाम से कैप्चर चला सकता है। संदर्भ में [प्रोजेक्ट ऑटोमेशन](reference/sdk-reference.md#project-automation--chlorosproject) देखें।

## पाइप-केवल इंस्टॉल से क्या मिलता है

हार्डवेयर सतहों का उपयोग करने से पहले मॉड्यूल-स्तर की उपलब्धता फ़्लैग की जाँच करें:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)       # True iff daq_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

ऐसे होस्ट पर जिसमें **केवल** `pip install chloros-sdk` हो और कोई Chloros डेस्कटॉप पैकेज न हो:

* `ChlorosLocal`, `process_folder`, और `process_lattice_capture` काम **नहीं** करते — उन्हें उस बैकएंड बाइनरी की आवश्यकता है जो डेस्कटॉप इंस्टॉलर के साथ आती है।
* स्मार्ट-कनेक्ट हेल्पर्स (`connect_camera`, `connect_array`, `connect_daq_sensor`) शुद्ध HTTP क्लाइंट हैं, इसलिए वे किसी दूसरी मशीन पर मौजूद बैकएंड के साथ काम करते हैं — लेकिन शिप किए गए बैकएंड केवल लूपबैक से बांधते हैं, इसलिए आपको स्वयं पोर्ट फॉरवर्ड करना होगा (जैसे `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) और `backend_url="http://127.0.0.1:5000"` को `auto_start_backend=False` के साथ पास करना होगा। [रिमोट-बैकएंड मोड](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel) देखें।
* डायरेक्ट-हार्डवेयर LATTICE क्लासेस (`LatticeCamera`, `CameraPool`, …) आयात करते हैं, लेकिन डेस्कटॉप पैकेज से Arena SDK रनटाइम की आवश्यकता होती है — इसके बिना `CAMERA_AVAILABLE`, `False` है।
* `daq_sdk` (डायरेक्ट DAQ क्लासेस) डेस्कटॉप इंस्टॉल के साथ आता है, PyPI पैकेज के साथ नहीं, इसलिए `DAQ_AVAILABLE` एक pip-only होस्ट पर `False` है — इसके बजाय (tunneled) बैकएंड के खिलाफ `connect_daq_sensor()` के माध्यम से DAQ सेंसर चलाएँ।

## लाइसेंस की आवश्यकता

SDK तक पहुँच के लिए किसी भी सशुल्क टियर — **कॉपर या उससे ऊपर**(कॉपर / ब्रॉन्ज़ / सिल्वर / गोल्ड) पर एक सक्रिय Chloros+ लॉगिन होना आवश्यक है; मुफ़्त आयरन टियर में कोई SDK / CLI पहुँच नहीं है। अनुपालन**सर्वर-साइड** है: हर SDK अनुरोध के साथ एक लाइव सत्र और एक सशुल्क योजना दोनों होनी चाहिए, अन्यथा बैकएंड `403` / `PLAN_UPGRADE_REQUIRED` लौटाता है (`ChlorosLocal` द्वारा `ChlorosLicenseError` के रूप में, और `connect_*` सहायकों द्वारा `ChlorosConnectError` के रूप में उठाया गया)। लॉग-आउट किए हुए कॉलर को `401` / `AUTH_REQUIRED` मिलता है (`ChlorosAuthenticationError`) के बजाय — `chloros-cli login` को फिर से चलाने से पहला मामला ठीक हो जाता है लेकिन दूसरा नहीं।

ऑफ़लाइन उपयोग प्लान की छूट अवधि के भीतर काम करता है: टियर सर्वर-प्रमाणीकरण कैश (5 मिनट) या हस्ताक्षरित, मशीन-बंधित लाइसेंस कैश (मासिक प्लान के लिए 30 दिन; वार्षिक के लिए सदस्यता समाप्ति तक) से पढ़ा जाता है। जब ग्रेस पीरियड समाप्त हो जाता है, तो प्लान फ्री पर वापस चला जाता है और SDK एक्सेस तब तक बंद हो जाता है जब तक मशीन एक बार सर्वर तक नहीं पहुँच जाती। `chloros-cli status` फ्री टियर पर एक्सेसिबल बना रहता है ताकि कारण हमेशा दिखाई दे। [Chloros+ Login](chloros+-login.md) देखें।

## अपवाद

&quot;Chloros में कुछ भी गलत हुआ&quot; को संभालने के लिए बेस क्लास को कैच करें:

```python
import chloros_sdk

try:
    chloros_sdk.process_folder("/path/to/folder")
except chloros_sdk.ChlorosAuthenticationError:
    print("Run `chloros-cli login` first.")
except chloros_sdk.ChlorosLicenseError:
    print("Chloros+ subscription required.")
except chloros_sdk.ChlorosError as e:
    print(f"Chloros error: {e}")
```

सभी पाइपलाइन अपवाद (`ChlorosBackendError`, `ChlorosConnectionError`, `ChlorosLicenseError`, `ChlorosAuthenticationError`, `ChlorosConfigurationError`, `ChlorosProcessingError`) `ChlorosError` से व्युत्पन्न होते हैं। एक फंदा: `ChlorosConnectError` — केवल `connect_camera` / `connect_array` / `connect_daq_sensor` द्वारा उत्पन्न होता है — साधारण `Exception` से व्युत्पन्न है, **न कि** `ChlorosError` से, इसलिए `except ChlorosError` इसे कैच नहीं करेगा। पूरी पदानुक्रम [SDK संदर्भ](reference/sdk-reference.md#exceptions) में है।

## यह भी देखें

* [SDK संदर्भ](reference/sdk-reference.md) — एआई सहायकों के लिए अनुकूलित, पूरी API सतह।
* [CLI संदर्भ](reference/cli-reference.md) — प्रत्येक CLI उप-कमांड एक SDK कॉल को दर्शाता है।
* [डाउनलोड](download.md) — Windows और Linux के लिए इंस्टॉलर।
