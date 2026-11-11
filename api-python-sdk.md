# API: Python SDK

{% hint style="info" %}
**पूर्ण API की तलाश में हैं?** यह पेज एक हैंड्स-ऑन ट्यूटोरियल है। हर पब्लिक क्लास, मेथड, सटीक सिग्नेचर, और कॉपी-पेस्ट करने योग्य उदाहरण [SDK रेफरेंस](reference/sdk-reference.md) में हैं, जो AI असिस्टेंट्स के लिए अनुकूलित है।**AI असिस्टेंट के साथ काम कर रहे हैं?** इस URL को चैट में पेस्ट करें ताकि उसके पास पूरी, वर्तमान Chloros 1.3.0 API हो:

`https://mapir.gitbook.io/chloros/reference/sdk-reference.md`

इस मैनुअल का हर पेज उसके लोअरकेस स्लग + `.md` पर कच्चे मार्कडाउन के रूप में उपलब्ध है, और पूरा मैनुअल `https://mapir.gitbook.io/chloros/llms.txt` पर इंडेक्स किया गया है।
{% endhint %}

**Chloros**Python ** (PyPI पर `chloros-sdk`) डेस्कटॉप ऐप को Python से करने योग्य सभी चीज़ों को संचालित करता है: बैच इमेज प्रोसेसिंग, लाइव LATTICE कैमरा और एरे नियंत्रण, DAQ लाइट-सेंसर सत्र, और सहेजे गए-प्रोजेक्ट ऑटोमेशन। यह उसी लोकल बैकएंड के ऊपर एक पतली परत है जिसका उपयोग GUI और CLI करते हैं (`127.0.0.1:5000` पर HTTP), इसलिए तीनों सतहों पर व्यवहार एक जैसा होता है।

## इंस्टॉल करें

SDK, Chloros डेस्कटॉप पैकेज के साथ आता है, जो प्रोसेसिंग बैकएंड और हार्डवेयर रनटाइम भी प्रदान करता है, और हमेशा इसके संस्करण से मेल खाता है।

**चरण 1 — Chloros इंस्टॉल करें।** Windows: [डाउनलोड](download.md) पेज से डेस्कटॉप इंस्टॉलर (डिफ़ॉल्ट पथ `C:\Program Files\Chloros\`) चलाएँ। Linux: `sudo apt install ./chloros_1.3.0_<arch>.deb` के साथ `.deb` पैकेज इंस्टॉल करें ([Linux इंस्टॉलेशन](linux/linux-installation.md)).**चरण 2 — **SDK** (Python 3.7+) की जाँच करें। आमतौर पर इंस्टॉल करने के लिए कुछ नहीं होता है:

* **Windows:** इंस्टॉलर आपके सिस्टम Python में बंडल किया गया SDK व्हील इंस्टॉल करता है (यदि कोई Python इंस्टॉल नहीं है, तो यह इस चरण को छोड़ देता है)।
* **Linux:** `.deb` सिस्टम `python3` के लिए SDK को `/usr/lib/python3/dist-packages` में इंस्टॉल करता है। इसके requirements, `python3-numpy` और `python3-requests`, पैकेज द्वारा अनुशंसित हैं: `apt install` उन्हें लाता है, `dpkg -i` नहीं लाता है।
* **एक वर्चुअलएनवी** सिस्टम के पैकेज नहीं देखता है; इसमें बंडल किए गए व्हील को इंस्टॉल करें: Linux पर `pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl`, या Windows इंस्टॉल फ़ोल्डर के `sdk` फ़ोल्डर में `chloros_sdk-*.whl` (डिफ़ॉल्ट रूप से `C:\Program Files\Chloros\sdk\`)।

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"   # Windows: py -c "…"
```

Chloros पैकेज वाली मशीन पर, जिसका SDK एक दूसरी मशीन पर बैकएंड चलाता है (देखें [What a pip-only install gets](#what-a-pip-only-install-gets)), उसे PyPI से इंस्टॉल करें: `pip install chloros-sdk`. PyPI रिलीज़ बिल्ड पर अपडेट होता है, इसलिए यह नवीनतम स्थिर रिलीज़ से मेल खाता है।

**चरण 3 — प्रति मशीन एक बार लॉग इन करें:**

```bash
chloros-cli login user@example.com 'YourPassword'
```

क्रेडेंशियल `~/.chloros/` में कैश किए जाते हैं (दोनों प्लेटफ़ॉर्म पर)। Windows पर आप वैकल्पिक रूप से डेस्कटॉप ऐप के यूज़र <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> टैब के माध्यम से साइन इन कर सकते हैं। SDK को एक सशुल्क Chloros+ प्लान की आवश्यकता है — नीचे [लाइसेंस आवश्यकता](#license-requirement) देखें।

| आवश्यकता | विवरण |
| --- | --- |
| **Chloros स्थापित** | Windows: डेस्कटॉप इंस्टॉलर; Linux: `.deb` पैकेज (बैकएंड बाइनरी और SDK प्रदान करता है) |
| **Python** | 3.7 या उच्चतर (3.10 पर विकसित/परीक्षित) |
| **ऑपरेटिंग सिस्टम** | Windows 10/11 64-बिट, उबंटू 22.04 LTS या नया, या NVIDIA Jetson (JetPack 6) |
| **लाइसेंस** | सक्रिय Chloros+ लॉगिन, कोई भी सशुल्क टियर (कॉपर या उच्चतर) |

## 60-सेकंड में जीत

एक कॉल एक प्रोजेक्ट बनाती है, एक फ़ोल्डर आयात करती है, प्रोसेसिंग कॉन्फ़िगर करती है, और पाइपलाइन चलाती है — यदि बैकएंड पहले से चल नहीं रहा है तो उसे स्वचालित रूप से शुरू करती है:

```python
import chloros_sdk

results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "GNDVI", "OSAVI"],
)
```

(Linux पर, Linux पथ का उपयोग करें: `/home/user/drone_images/flight001`। SDK दोनों प्लेटफ़ॉर्म पर समान रूप से काम करता है।)

क्या आप एक LATTICE कैप्चर फ़ोल्डर को प्रोसेस कर रहे हैं? LATTICE-अनुकूल रैपर का उपयोग करें — यह सही डिफ़ॉल्ट लागू करता है (कोई पैनल-लक्ष्य डिटेक्शन नहीं, मानक डेबयेर):

```python
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)
```

## `ChlorosLocal` — पूरी पाइपलाइन नियंत्रण

एक-लाइनर से परे किसी भी चीज़ के लिए, `ChlorosLocal` का उपयोग करें। यह एक बैकएंड का उपयोग करता है जो पहले से चल रहा है (डेस्कटॉप ऐप का), या पहले उपयोग पर इंस्टॉल किए गए बैकएंड को शुरू करता है (`auto_start_backend=True`; Windows पर `backend` फ़ोल्डर से Chloros इंस्टॉल फ़ोल्डर, `C:\Program Files\Chloros\backend\` (डिफ़ॉल्ट रूप से) का उपयोग करता है, प्रोजेक्ट बनाता और कॉन्फ़िगर करता है, प्रगति की निगरानी करता है, और रन के बाद का सारांश लौटाता है।

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
`localhost` को प्रतिस्थापित करने के बजाय डिफ़ॉल्ट `http://127.0.0.1:5000` रखें — Windows पर, `localhost` पहले `::1` पर रिज़ॉल्व होता है और IPv4-केवल बैकएंड के विरुद्ध प्रति अनुरोध ~2 सेकंड का समय लेता है।
{% endhint %}

इसे एक संदर्भ प्रबंधक के रूप में उपयोग करें। ब्लॉक को छोड़ने से इस इंस्टेंस का बैकएंड का उपयोग समाप्त हो जाता है, न कि बैकएंड का: यह अगली स्क्रिप्ट के लिए चलता रहता है और 30 मिनट तक उपयोग में न आने पर अपने आप बंद हो जाता है। `shutdown_backend()` इस इंस्टेंस को शुरू करते ही रोक देता है।

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26", camera="Survey3N_RGN")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "GNDVI", "OSAVI"],
        export_format="TIFF (16-bit)",
    )
    results = cl.process(mode="parallel", wait=True)
print(results["summary"])
```

`configure()` इन कीवर्ड्स को स्वीकार करता है: `debayer`, `vignette_correction`, `reflectance_calibration`, `indices`, `export_format`, `ppk`, `daq_log_path`, `input_level`, `radiometric_output`, `array_alignment`, `array_alignment_crop`, `array_alignment_interpolation`, और `custom_settings`। मुख्य मान:

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (8-bit)"            # 8-bit TIFF; what all-8-bit images get for a deeper format
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

पूरा होने पर, `process()` बैकएंड के प्रोसेसिंग सारांश को `result["summary"]` के रूप में संलग्न करता है। `summary["hints"]` में प्रत्येक प्रविष्टि किसी भी उल्लेखनीय बात को समझाने वाला एक पूरा वाक्य है — उदाहरण के लिए, किसी रन से शून्य आउटपुट क्यों मिला — और हर संकेत को एक Python `UserWarning` के रूप में भी फिर से प्रसारित किया जाता है, इसलिए खाली रन स्वयं निदान करते हैं भले ही आप कभी भी dict की जाँच न करें:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

{% hint style="warning" %}
**`process()` तब कोई त्रुटि नहीं देता जब एक रन कोई छवि उत्पन्न नहीं करता है।** यह एकमात्र स्थान है जहाँ SDK और CLI जानबूझकर भिन्न हैं: `chloros-cli process` "उत्पादों का अनुरोध किया गया था, कोई भी नहीं लिखा गया" को एक विफलता के रूप में मानता है और गैर-शून्य (non-zero) पर बाहर निकलता है, जबकि SDK सामान्य रूप से वापस लौटता है और `summary` / संकेतों (hints) के माध्यम से स्थिति की रिपोर्ट करता है। यदि आपकी पाइपलाइन को एक खाली रन पर रुकना चाहिए, तो स्वयं इसकी जाँच करें — किसी अपवाद (exception) पर निर्भर रहने के बजाय `summary` की जाँच करें (या प्रोजेक्ट फ़ोल्डर के अंतर्गत फ़ाइलों को गिनें)।
{% endhint %}

## स्मार्ट कनेक्ट — लाइव हार्डवेयर

तीन हेल्पर बैकएंड के हार्डवेयर पूल में स्थायी सत्र खोलते हैं — यह वही पूल है जिसका उपयोग CLI, अन्य स्क्रिप्ट और GUI के कैमरा टैब करते हैं, इसलिए SDK स्क्रिप्ट सीरियल पोर्ट या नेटवर्क बैंडविड्थ के लिए संघर्ष किए बिना उनके साथ सह-अस्तित्व में रहती हैं। यदि कोई स्थानीय बैकएंड चल नहीं रहा है, तो तीनों स्वचालित रूप से एक स्थानीय बैकएंड शुरू करते हैं। GUI का DAQ पैनल पूल का हिस्सा नहीं है: किसी स्क्रिप्ट से खोलने से पहले GUI में एक लाइट सेंसर को बंद करें।

### सिंगल लैटिस कैमरा — `connect_camera`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)   # microseconds, dB
    cam.capture("output/")
```

{% hint style="info" %}
**सापेक्ष पथ उस स्थान पर जाते हैं जहाँ आपका स्क्रिप्ट चलता है (1.3.0 में बदला गया)।** एक स्थानीय बैकएंड के साथ, एक सापेक्ष `output_dir` (डिफ़ॉल्ट `"output"` सहित) — और अन्य पथ जिन्हें बैकएंड खोलता है (`burst_dir`, `profile_path`, `save_path`, `mask_path`) — आपके स्क्रिप्ट की वर्किंग डायरेक्टरी के विरुद्ध हल किया जाता है, इसलिए `cam.capture("output/")` `<current folder>/output/` में लिखता है। 1.2.0 में, एक इंस्टॉल किए गए बैकएंड ने उन्हें अपने स्वयं के इंस्टॉल फ़ोल्डर में हल किया और "Access is denied" त्रुटि के साथ विफल हो गया। एक रिमोट बैकएंड के लिए, पथ टाइप किए गए रूप में ही भेजे जाते हैं।
{% endhint %}

### सिंक्रनाइज़्ड ऐरे — `connect_array`

`connect_array` मल्टी-कैमरा रिग्स के लिए अनुशंसित एंट्री पॉइंट है। यह GUI की तरह ही स्मार्ट-प्रैप फ्लो चलाता है: नेटवर्क विश्लेषण, सिंक-टियर ऑटो-पिक, PTP टाइम-सिंक, प्रति-कैमरा पिक्सेल-फ़ॉर्मेट चयन, AE सीडिंग, और GPIO ट्रिगर आर्मिंग। **पहला सीरियल मास्टर है** (यह हार्डवेयर ट्रिगर पल्स को फायर करता है); बाकी स्लेव हैं।

```python
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")
```

ट्रिगर करने से पहले सभी कैमरों पर ऑटो-एक्सपोज़र के सेट होने की प्रतीक्षा करने के लिए किसी भी एरे कैप्चर में `smart=True` जोड़ें।

1.3.0 में नया, एक `ArraySession` आपको फ़ाइलें लिखे बिना सीधे सिंक्रनाइज़ किए गए फ़्रेम भी देता है:

* `arr.grab()` प्रत्येक कैमरे के लिए एक फ़्रेम लौटाता है, सभी एक ही ट्रिगर से, numpy ऐरे (`{serial: array}`) के रूप में; `.group_id` साझा ट्रिगर है और `.errors` उन कैमरों के नाम बताता है जिन्हें बाहर रखा गया है।
* `arr.frame_stream(fps=5.0)` आपकी अपनी पाइपलाइन में लाइव प्रोसेसिंग के लिए, प्रति टिक ऐसा एक सेट प्रदान करता है।
* `arr.continuous("output/")` बैकएंड की अपनी निरंतर कैप्चर (GUI का कंटीन्यूअस मोड) चलाता है और `ContinuousHandle` को `status()` के साथ लौटाता है, `wait()` और `stop()` — जब आपको हर ट्रिगर को उच्च दर पर सहेजने की आवश्यकता हो, तो कैप्चर लूप के बजाय इसका उपयोग करें।

कैप्चर मोड (सिंगल / कंटिन्यूअस / इंटरवल / फास्टेस्ट), रिकॉर्डर, बर्स्ट-टू-वीडियो, और एरे एलाइनमेंट के लिए, [SDK रेफरेंस](reference/sdk-reference.md#synchronized-array--arraysession-smart-prep) देखें।

### DAQ लाइट सेंसर — `connect_daq_sensor`

बिना किसी तर्क के, `connect_daq_sensor()` स्वचालित रूप से ट्रांसपोर्ट का पता लगाता है (प्राथमिकता: ईथरनेट → BLE → USB):

```python
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])
```

प्रत्येक फ़्रेम में 135-पॉइंट `spectrum` (कैलिब्रेट होने पर W/m²/nm), एक `is_saturated` फ़्लैग, और CIE `x` शामिल होता है, `y`, `z`। किसी विशिष्ट सेंसर या ट्रांसपोर्ट को पिन करने के लिए, एक स्पष्ट संकेत पास करें:

```python
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")        # implies BLE
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")     # implies Ethernet
```

ध्यान दें कि कैप-करेक्शन प्रोफाइल (`cap_id`) **किसी** SDK नॉब नहीं हैं — इसके बजाय उन्हें `chloros-cli daq pool-connect --cap-id …` / `pool-set-cap` के माध्यम से चुनें।

### सहेजे गए प्रोजेक्ट — `open_project`

एक सहेजा गया Chloros प्रोजेक्ट अपने जुड़े हार्डवेयर (`cameras.json` + `sensors.json` के साथ `project.json`) को बनाए रखता है, और `chloros_sdk.open_project(path)` एक साथ अपने कैमरों, ऐरे और लाइट सेंसर को फिर से जोड़ सकता है और डिवाइस नाम के अनुसार कैप्चर चला सकता है। लाइट सेंसर एक चल रहे Chloros बैकएंड के माध्यम से फिर से जुड़ते हैं, अपनी सहेजी गई सेटिंग्स और कैप के साथ। संदर्भ में [प्रोजेक्ट ऑटोमेशन](reference/sdk-reference.md#project-automation--chlorosproject) देखें।

## pip-केवल इंस्टॉल से क्या मिलता है

हार्डवेयर सरफेस का उपयोग करने से पहले मॉड्यूल-स्तर की उपलब्धता फ़्लैग की जाँच करें:

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)    # True iff lattice_sdk imported cleanly
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)  # True iff ChlorosProject deps available
```

ऐसे होस्ट पर जिसमें **केवल** `pip install chloros-sdk` हो और कोई Chloros डेस्कटॉप पैकेज न हो:

* `ChlorosLocal`, `process_folder`, और `process_lattice_capture` काम **नहीं** करते — उन्हें बैकएंड बाइनरी की आवश्यकता होती है जो डेस्कटॉप इंस्टॉलर के अंदर आती है।
* स्मार्ट-कनेक्ट हेल्पर (`connect_camera`, `connect_array`, `connect_daq_sensor`) शुद्ध HTTP क्लाइंट हैं, इसलिए वे किसी दूसरी मशीन पर मौजूद बैकएंड के साथ काम करते हैं — लेकिन शिप किए गए बैकएंड केवल लूपबैक पर बाइंड करते हैं, इसलिए आपको स्वयं पोर्ट फॉरवर्ड करना होगा (उदा. `ssh -N -L 5000:127.0.0.1:5000 user@chloros-host`) और `backend_url="http://127.0.0.1:5000"` को `auto_start_backend=False` के साथ पास करना होगा। [Remote-Backend Mode](reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel) देखें।
* डायरेक्ट-हार्डवेयर LATTICE क्लासेस (`LatticeCamera`, `CameraPool`, …) आयात, लेकिन डेस्कटॉप पैकेज से Arena SDK रनटाइम की आवश्यकता होती है — इसके बिना `CAMERA_AVAILABLE` `False` है।
* DAQ सेंसर केवल एक (टनल किए गए) बैकएंड — `connect_daq_sensor()`, या किसी प्रोजेक्ट के `proj.sensors` के माध्यम से संचालित होते हैं; बैकएंड अपने स्वयं के मशीन पर सेंसर खोलता है।

## एआई सहायक — `chloros-mcp`

SDK में `chloros-mcp` शामिल है, जो एक MCP सर्वर है जो Claude Desktop, Claude Code, Cursor और अन्य MCP क्लाइंट्स को Chloros चलाने की अनुमति देता है: इमेजरी को प्रोसेस करना, और LATTICE कैमरों, ऐरे और DAQ लाइट सेंसर चलाना। इसके टूल इस बात को कवर करते हैं कि यह SDK क्या करता है। यह SDK के साथ इंस्टॉल होता है, इसलिए Claude Code के लिए सेटअप एक लाइन है:

```bash
claude mcp add chloros -- chloros-mcp
```

अन्य क्लाइंट्स: [AI असिस्टेंट्स के साथ Chloros का उपयोग करना](ai-assistants.md). विकल्प और हर टूल: [MCP सर्वर (AI असिस्टेंट्स)](reference/sdk-reference.md#mcp-server-ai-assistants).

## लाइसेंस आवश्यकता

SDK एक्सेस के लिए किसी भी पेड टियर पर एक सक्रिय Chloros+ लॉगिन आवश्यक है — **कॉपर या उससे ऊपर**(कॉपर / ब्रॉन्ज़ / सिल्वर / गोल्ड); निःशुल्क आयरन टियर में कोई SDK/CLI एक्सेस नहीं है। प्रवर्तन**सर्वर-साइड** है: हर SDK अनुरोध के साथ एक लाइव सत्र और एक सशुल्क योजना दोनों होनी चाहिए, अन्यथा बैकएंड `403` / `PLAN_UPGRADE_REQUIRED` लौटाता है। (`ChlorosLocal` द्वारा `ChlorosLicenseError` के रूप में, और `connect_*` हेल्पर्स द्वारा `ChlorosConnectError` के रूप में उठाया गया)। लॉग-आउट किए हुए कॉलर को `401` / `AUTH_REQUIRED` मिलता है (`ChlorosAuthenticationError`) के बजाय — `chloros-cli login` को फिर से चलाने से पहला मामला ठीक हो जाता है लेकिन दूसरा नहीं।

ऑफ़लाइन उपयोग प्लान की छूट अवधि के भीतर काम करता है: टियर सर्वर-प्रमाणीकरण कैश (5 मिनट) या हस्ताक्षरित, मशीन-आधारित लाइसेंस कैश (मासिक प्लान के लिए 30 दिन; वार्षिक प्लान के लिए सदस्यता की समाप्ति तक) से पढ़ा जाता है। जब ग्रेस पीरियड समाप्त हो जाता है, तो प्लान फ्री पर वापस चला जाता है और मशीन के एक बार सर्वर तक पहुँचने तक SDK एक्सेस बंद हो जाता है। `chloros-cli status` फ्री टियर पर उपलब्ध रहता है ताकि कारण हमेशा दिखाई दे। [Chloros+ लॉगिन](chloros+-login.md) देखें।

## अपवाद

"Chloros में कोई भी त्रुटि" को संभालने के लिए बेस क्लास को कैच करें:

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

* [SDK संदर्भ](reference/sdk-reference.md) — पूरी API सतह, जो AI सहायकों के लिए अनुकूलित है।
* [CLI संदर्भ](reference/cli-reference.md) — हर CLI उप-कमांड एक SDK कॉल को दर्शाता है।
* [AI सहायकों के साथ Chloros का उपयोग](ai-assistants.md) — `chloros-mcp` सेटअप और प्रॉम्प्ट रेसिपी।
* [डाउनलोड](download.md) — Windows और Linux के लिए इंस्टॉलर।
