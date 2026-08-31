# Chloros Python SDK संदर्भ

**संस्करण:**

1.2.0**उत्पन्न:**2026-07-29 19:19 ·**संशोधित:** 2026-08-30**पैकेज:** `chloros-sdk` (PyPI)**लक्षित दर्शक:** एलएलएम (LLM) उपयोग के लिए अनुकूलित; मानव-पठनीय।**दायरा:** `import chloros_sdk` द्वारा प्रदर्शित हर सार्वजनिक क्लास, फ़ंक्शन, और हेल्पर, जिसमें इमेज प्रोसेसिंग, सिंगल-कैमरा कंट्रोल, सिंक्रनाइज़्ड ऐरे, DAQ सेंसर, और प्रोजेक्ट ऑटोमेशन को कवर करने वाले कॉपी-पेस्ट करने योग्य उदाहरण शामिल हैं।

यदि आपको केवल मुख्य बातें जाननी हैं, तो यहाँ जाएँ:
- [इंस्टॉलेशन और क्विकस्टार्ट](#installation)
- [LATTICE ऐरे के लिए स्मार्ट-कनेक्ट](#smart-connect-for-lattice-cameras)
- [DAQ सेंसर सेशंस](#daq-sensor-sessions)
- [प्रोजेक्ट ऑटोमेशन](#project-automation--chlorosproject)
- [स्मार्ट-AE / स्मार्ट-कैप्चर](#smart-ae--smart-capture)

---

## 60 सेकंड में आर्किटेक्चर

SDK, Chloros बैकएंड के ऊपर एक पतली Python परत है (वही Flask सर्वर जिसका उपयोग डेस्कटॉप GUI और CLI करते हैं)। ऑटोमेशन के लिए आप `chloros_sdk` को इम्पोर्ट करते हैं और हाई-लेवल मेथड्स को कॉल करते हैं; इसके अंतर्गत, हर कॉल पोर्ट 5000 पर स्थानीय बैकएंड के लिए एक HTTP अनुरोध बन जाता है — `http://127.0.0.1:5000/api/...` (जानबूझकर `localhost` नहीं, जो पहले Windows पर `::1` को रिज़ॉल्व करता है और केवल IPv4 बैकएंड के खिलाफ प्रति अनुरोध ~2 s की लागत आता है)। बैकएंड हार्डवेयर पूल का मालिक है — कैमरे, DAQ सेंसर, संरेखण प्रोफ़ाइल, फ्रेम बफ़र्स — ताकि SDK स्क्रिप्ट GUI के साथ सीरियल पोर्ट या NIC बैंडविड्थ के लिए प्रतिस्पर्धा किए बिना सह-अस्तित्व में रह सकें।

आप तीन सतहों का उपयोग करेंगे:

1. **`ChlorosLocal` + फ्री फंक्शंस** (`process_folder`, `process_lattice_capture`) — इमेज-प्रोसेसिंग पाइपलाइन। एक ही Python कॉल से पूरे फ़ोल्डर का कैलिब्रेशन / डेबायर / इंडेक्स एक्सपोर्ट करें।
2. **स्मार्ट-हैंडल** (`connect_camera`, `connect_array`, `connect_daq_sensor`) — लाइव हार्डवेयर के लिए एक स्थायी बैकएंड सत्र खोलें। समान GUI के रूप में &quot;smart-prep&quot; फ्लो: नेटवर्क प्रोब, टियर ऑटो-पिक, PTP, AE सीडिंग, GPIO ट्रिगर कॉन्फिग।
3. **`ChlorosProject` / `open_project`** — एक सहेजा गया प्रोजेक्ट लोड करें (`cameras.json` + `sensors.json` + `project.json` वाला फ़ोल्डर), एक साथ सब कुछ कनेक्ट करें, और नामित हैंडल के साथ कैप्चर चलाएँ।

सतहें 1 और 2 **एक स्थानीय बैकएंड को स्वचालित रूप से शुरू करें** यदि कोई पहले से सुन नहीं रहा है (वही बंडल किया गया बाइनरी जिसे GUI/CLI स्पॉन करता है) — ताकि एक खाली स्क्रिप्ट एक नए शेल से काम करे, बिना आपके पहले बैकएंड शुरू किए। बाहर रहने के लिए `auto_start_backend=False` पास करें (जैसे कि जब किसी रिमोट बैकएंड की ओर इशारा किया जा रहा हो, जिसे कभी स्पॉन नहीं किया जाता)। [बैकएंड ऑटो-स्टार्ट](#backend-auto देखें-start). Surface 3 का व्यवहार अलग है: `open_project()` कोई `auto_start_backend` पैरामीटर नहीं लेता है, और `connect_all()` कभी भी बैकएंड स्पॉन नहीं करता है — यह  की जांच करता है91 एक बार `lattice_sdk` को कॉल करता है, और यदि कोई उत्तर नहीं मिलता है, तो चुपचाप सीधे (बैकएंड-रहित) `lattice_sdk` डिवाइस नियंत्रण पर वापस चला जाता है। केवल `proj.process()` और `stream(..., overlays=True)` आलसी ढंग से एक `ChlorosLocal()` बनाते हैं (जो स्वचालित रूप से शुरू होता है)।

तीनों auth-gated हैं: मशीन पर `chloros-cli login` एक बार चलाएँ, या डेस्कटॉप GUI के माध्यम से साइन इन करें। एक वैध सत्र के बिना SDK कॉल `ChlorosAuthenticationError` को बढ़ाता है।

आवश्यकताएँ:
- Python 3.7+ (पैकेज द्वारा घोषित; 3.10 पर विकसित/परीक्षित)
- Chloros डेस्कटॉप स्थानीय रूप से स्थापित होना चाहिए (बैकएंड बाइनरी इंस्टॉलर के अंदर आती है)
- सक्रिय Chloros+ लॉगिन। SDK / CLI का स्तर **कॉपर**या उससे अधिक होना चाहिए (कॉपर / ब्रॉन्ज / सिल्वर / गोल्ड); मुफ्त**आयरन**स्तर में कोई SDK / CLI एक्सेस नहीं है। यह**सर्वर-साइड** लागू किया जाता है: हर SDK / CLI -flagged अनुरोध में लाइव सत्र और एक सशुल्क योजना दोनों होनी चाहिए, अन्यथा बैकएंड `403` के साथ `error_code: PLAN_UPGRADE_REQUIRED` लौटाता है (जिसे `ChlorosLicenseError` के रूप में  द्वारा दिखाया जाता है)।0101, और `connect_*` हेल्पर्स द्वारा `ChlorosConnectError` के रूप में)। एक लॉग-आउट किया हुआ कॉलर `401` / `AUTH_REQUIRED` प्राप्त करता है (`ChlorosAuthenticationError`) के बजाय — दोनों अलग हैं क्योंकि `chloros-cli login` को दोबारा चलाने से पहला ठीक हो जाता है और दूसरा ठीक नहीं हो सकता।
- ऑफ़लाइन उपयोग योजना की छूट अवधि के भीतर समर्थित है: टियर सर्वर-प्रमाणीकरण कैश से पढ़ा जाता है (5 मिनट) या हस्ताक्षरित, मशीन-बाउंड लाइसेंस कैश (मासिक योजनाओं के लिए 30 दिन, वार्षिक के लिए सदस्यता की समाप्ति तक)। जब वह छूट अवधि समाप्त हो जाती है तो योजना मुफ्त पर वापस आ जाती है और SDK / CLI एक्सेस तब तक बंद हो जाता है जब तक कि मशीन एक बार सर्वर तक नहीं पहुंच सकती। `chloros-cli status` (`GET /api/license-status`) मुफ़्त टियर पर पहुँच योग्य बना रहता है ताकि कारण दिखाई दे — यह एकमात्र SDK / CLI मार्ग है जो टियर गेट से छूट प्राप्त है।
- Windows 10/11 64-बिट, **उबंटू 22.04 LTS या नवीनतर**, या Jetson (JetPack 6)। Ubuntu 20.04 समर्थित**नहीं** है: `.deb` की निर्भरताएँ उन चीज़ों से प्राप्त होती हैं जिनके खिलाफ बैकएंड लिंक होता है, जिसमें `libc6 (>= 2.34)` शामिल है, और focal glibc 2.31 के साथ आता है।

---

## इंस्टॉलेशन

Python SDK, Chloros बैकएंड के ऊपर एक पतली Python परत है। कुछ DAQ-केवल वर्कफ़्लो के अलावा हर चीज़ के लिए, आपको **Chloros डेस्कटॉप पैकेज को स्थानीय रूप से इंस्टॉल** करने की आवश्यकता है (Windows इंस्टॉलर या Linux `.deb`) — यह वही है जो बैकएंड बाइनरी प्रदान करता है, LATTICE कैमरों के लिए Arena SDK रनटाइम, और कैलिब्रेशन बंडल्स।

नवीनतम डाउनलोड: [`https://mapir.gitbook.io/chloros/download`](https://mapir.gitbook.io/chloros/download)

### चरण 1 — Chloros प्लेटफ़ॉर्म पैकेज इंस्टॉल करें

#### Windows (.exe)

1. डाउनलोड पेज से `Chloros-Setup-x.y.z.exe` डाउनलोड करें।
2. इंस्टॉलर चलाएँ और विज़ार्ड का पालन करें। डिफ़ॉल्ट इंस्टॉल पथ `C:\Program Files\MAPIR\Chloros\` है।
3. Chloros को कम से कम एक बार लॉन्च करें और अपने Chloros+ खाते से साइन इन करें।

#### Linux amd64 (.deb)

```bash
sudo dpkg -i chloros-amd64.deb
sudo apt-get install -f         # only if dpkg reports missing dependencies
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

#### Linux arm64 — Jetson (JetPack 6)

```bash
sudo dpkg -i chloros-arm64-jp6.deb
sudo apt-get install -f
chloros-cli --version
chloros-cli login user@example.com 'YourPassword'
```

### चरण 2 — Python SDK स्थापित करें

**Chloros इंस्टॉलर एक मिलते-जुलते SDK व्हील के साथ आता है।** हर Windows इंस्टॉलर और Linux .deb डिस्क पर एक `chloros_sdk-X.Y.Z-py3-none-any.whl` रखता है जो GUI / CLI से बिल्कुल मेल खाता है। / बैकएंड संस्करण। आपको सिंक में रहने के लिए PyPI के पीछे भागने की ज़रूरत नहीं है।

#### Windows

इंस्टॉलर आपके सिस्टम के Python का उपयोग करके बंडल किए गए व्हील के खिलाफ `pip install` को स्वचालित रूप से चलाता है (`py.exe` लॉन्चर को प्राथमिकता दी जाती है, X पर वापस आता है). किसी कार्रवाई की आवश्यकता नहीं है — `import chloros_sdk` एक सफल इंस्टॉलेशन के बाद आपके Python वातावरण में काम करता है। यदि बॉक्स पर कोई Python नहीं है, तो इंस्टॉलर चुपचाप इस चरण को छोड़ देता है और GUI + CLI काम करते रहते हैं।

#### Linux (.deb)

.deb व्हील को `/usr/lib/chloros/sdk/` पर रखता है। `postinst` सटीक कमांड प्रिंट करता है — PEP 668 डिस्ट्रो डिफ़ॉल्ट रूप से ग्लोबल पाइप राइट्स को अस्वीकार करते हैं, इसलिए हम स्वचालित रूप से नहीं करते-install:

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

एयर-गैप्ड Jetson डिप्लॉयमेंट्स के लिए यह पूरी तरह से ऑफ़लाइन है — व्हील पहले से ही डिस्क पर है।

#### सार्वजनिक PyPI

केवल pip-होस्ट के लिए (Chloros डेस्कटॉप पैकेज स्थापित नहीं है; रिमोट-बैकएंड या केवल DAQ वर्कफ़्लो):

```bash
pip install chloros-sdk
```

रिलीज़-संस्करण इंस्टॉलर बिल्ड पर PyPI अपडेट किया जाता है, ताकि प्रकाशित व्हील नवीनतम स्थिर रिलीज़ से मेल खाता हो। डेव बिल्ड (जैसे `1.1.4.dev1`) केवल बंडल किए गए इंस्टॉलर व्हील के माध्यम से ही भेजे जाते हैं।

#### सत्यापित करें

```python
import chloros_sdk
print(chloros_sdk.__version__)
print("CAMERA_AVAILABLE =", chloros_sdk.CAMERA_AVAILABLE)
print("DAQ_AVAILABLE    =", chloros_sdk.DAQ_AVAILABLE)
print("PROJECT_AVAILABLE =", chloros_sdk.PROJECT_AVAILABLE)
```

> **Chloros+ सदस्यता आवश्यक है।** सभी SDK कॉल के लिए एक सक्रिय Chloros+ लॉगिन की आवश्यकता होती है। प्रत्येक मशीन पर `chloros-cli login user@example.com 'YourPassword'` एक बार चलाएँ; क्रेडेंशियल `~/.chloros/` में कैश किए जाते हैं।

### क्या मुझे डेस्कटॉप पैकेज की आवश्यकता है?

अधिकांश वर्कफ़्लो के लिए केवल pip पैकेज **काफी** नहीं है। यहाँ बताया गया है कि प्रत्येक SDK सतह को क्या चाहिए:

| SDK सतह | डेस्कटॉप पैकेज की आवश्यकता है? | क्यों |
| --- | --- | --- |
| `ChlorosLocal`, `process_folder`, `process_lattice_capture` | **हाँ** | `/usr/lib/chloros/chloros-backend` पर बैकएंड बाइनरी को स्वचालित रूप से शुरू करता हैX (Linux) या `C:\Program Files\MAPIR\Chloros\…` (Windows). |
| `connect_camera`, `connect_array`, `connect_daq_sensor`, `analyze_array_network`, `list_*`, `discover_*` | **हाँ**(स्थानीय)**/ नहीं**(रिमोट) | बैकएंड पर शुद्ध HTTP क्लाइंट। स्थानीय बैकएंड → डेस्कटॉप पैकेज आवश्यक है। रिमोट बैकएंड → `backend_url=`**एक टनल के माध्यम से** (देखें रिमोट-बैकएंड मोड — शिप किए गए बैकएंड केवल लूपबैक से बाइंड करते हैं)। |
| `ChlorosProject` / `open_project` | **हाँ** | बैकएंड के माध्यम से सहेजे गए प्रोजेक्ट चलाता है। |
| डायरेक्ट लैटिस क्लासेस (`LatticeCamera`, `CameraPool`, `Calibration`, `DLS`, …) | **हाँ** | डेस्कटॉप पैकेज के अंदर आने वाले Arena SDK नेटिव रनटाइम की आवश्यकता है। `CAMERA_AVAILABLE` अन्यथा `False` है। |
| डायरेक्ट DAQ क्लासेस (`DAQUSensor`, `DAQMSensor`, `DAQESensor`, `SensorFleet`, `discover_all`) | **नहीं** | pyserial/bleak/zeroconf पर शुद्ध Python। एक pip-केवल वातावरण DAQs को एंड-टू-एंड चला सकता है। |

### रिमोट-बैकएंड मोड (टनल के माध्यम से, pip-केवल होस्ट)

> **शिप किया गया बैकएंड LAN पर पहुँच योग्य नहीं है।** प्रोडक्शन बिल्ड केवल लूपबैक (दोनों लूपबैक परिवारों) से बाइंड करते हैं और एकमात्र नॉन-लूपबैक मोड (`CHLOROS_CLOUD_MODE`) को कड़ाई से अस्वीकार करते हैं।, इसलिए
> `backend_url="http://<lan-ip>:5000"` **स्थापित
> Chloros** के खिलाफ काम नहीं कर सकता
> — यह पैटर्न केवल कभी एक स्रोत/dev
> बैकएंड के खिलाफ काम करता था। किसी दूसरी मशीन पर बैकएंड चलाने के लिए, उसका लूपबैक
> पोर्ट स्वयं फॉरवर्ड करें और SDK को टनल पर पॉइंट करें:

```bash
# on the pip-only host: forward local 5000 to the Chloros machine's loopback
ssh -N -L 5000:127.0.0.1:5000 user@chloros-host
```

```python
import chloros_sdk

BACKEND = "http://127.0.0.1:5000"   # the tunnel endpoint

chloros_sdk.connect_camera("213800234", backend_url=BACKEND)
chloros_sdk.connect_array(serials, backend_url=BACKEND)
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local", backend_url=BACKEND)
```

हेडलेस / सीआई / रोबोटिक्स होस्ट एक मशीन पर पूरा डेस्कटॉप इंस्टॉल रख सकते हैं और उसे &quot;Chloros सर्वर&quot; के रूप में उपयोग कर सकते हैं, और बाकी सभी जगह `pip install chloros-sdk` का उपयोग कर सकते हैं — लेकिन उनके बीच का ट्रांसपोर्ट ऊपर बताई गई उपयोगकर्ता-व्यवस्थित टनल है, न कि एक सीधा LAN URL।

> **ज्ञात सीमा — `ChlorosLocal` केवल पाइप-सक्षम नहीं है।** `ChlorosLocal(backend_url=BACKEND)` वर्तमान में URL की जाँच करने से पहले अपने कंस्ट्रक्टर में एक स्थानीय बैकएंड बाइनरी को हल करता है, और जब कोई डेस्कटॉप पैकेज स्थापित नहीं होता है तो `ChlorosBackendError` (&quot;Chloros बैकएंड नहीं मिला…&quot;) त्रुटि दिखाता है — भले ही कोई पहुँच योग्य रिमोट बैकएंड मौजूद हो। केवल ऊपर दी गई स्मार्ट-कनेक्ट सतह (`connect_camera` / `connect_array` / `connect_daq_sensor`, साथ ही `analyze_array_network` और `list_*` / `discover_*` सहायक) केवल pip होस्ट से काम करता है।

### केवल DAQ वर्कफ़्लो (केवल pip होस्ट)

यदि आपको केवल DAQ सेंसर की आवश्यकता है और आप LATTICE कैमरों या इमेज प्रोसेसिंग को नहीं छूते हैं, तो pip पैकेज स्व-निहित है:

```bash
pip install chloros-sdk
```

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

sensor = DAQUSensor(port="/dev/ttyUSB0")
sensor.connect()
sensor.start_streaming()
```

डायरेक्ट-हार्डवेयर DAQ कार्य के लिए किसी बैकएंड, .deb, या Chloros+ लॉगिन की आवश्यकता नहीं है।

---

## क्विकस्टार्ट

```python
import chloros_sdk

# === Image processing ===
results = chloros_sdk.process_folder(
    "C:/DroneImages/Flight001",
    indices=["NDVI", "NDRE", "GNDVI"],
)

# === Live LATTICE single-cam ===
with chloros_sdk.connect_camera("213800234") as cam:
    cam.set_settings(exposure_time=10000, gain=0.0)
    cam.capture("output/")

# === Live LATTICE synchronized array (GUI smart-prep flow) ===
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    arr.capture("output/", processing="reflectance")

# === Live DAQ spectral sensor ===
with chloros_sdk.connect_daq_sensor() as daq:    # smart-detect USB / BLE / ETH
    for frame in daq.latest(n=5):
        print(frame["spectrum"][:10])

# === Drive a saved project end-to-end ===
proj = chloros_sdk.open_project("/path/to/project")
proj.connect_all()
proj.arrays["main_rig"].capture("output/", processing="reflectance")
proj.disconnect_all()
```

---

## टॉप-लेवल API सूचकांक

```python
import chloros_sdk

# === Image processing (full pipeline) ===
chloros_sdk.ChlorosLocal                          # class
chloros_sdk.process_folder(...)                   # one-shot helper
chloros_sdk.process_lattice_capture(...)          # LATTICE-friendly defaults
chloros_sdk.read_image_audit_tags(path)           # post-run audit

# === Live cameras (persistent backend pool) ===
chloros_sdk.connect_camera(serial, ...)           # → CameraSession
chloros_sdk.connect_array(serials, ...)           # → ArraySession (smart-prep)
chloros_sdk.attach_array(serials_or_id, ...)      # → ArraySession (attach without re-connecting)
chloros_sdk.list_cameras()
chloros_sdk.list_arrays()
chloros_sdk.discover_lattice_cameras()
chloros_sdk.analyze_array_network(...)            # network capability + recommendation
chloros_sdk.CaptureResult                         # list subclass returned by ArraySession.capture
chloros_sdk.RecorderHandle                        # handle for an array record()/burst() job

# === Live DAQ sensors (persistent backend pool) ===
chloros_sdk.connect_daq_sensor(...)               # → DAQSensorSession
chloros_sdk.discover_daq_sensors()                # scan USB/BLE/ETH (finds a DAQ-M MAC)
chloros_sdk.list_daq_sensors()

# === Project lifecycle ===
chloros_sdk.open_project(path)                    # → ChlorosProject
chloros_sdk.ChlorosProject                        # class
chloros_sdk.AlignmentSpec                         # dataclass
chloros_sdk.ArrayHandle, CameraHandle, SensorHandle

# === Direct-hardware (no-backend) classes (from lattice_sdk / daq_sdk) ===
chloros_sdk.LatticeCamera, CameraSettings, PRESETS, CameraPool
chloros_sdk.Calibration, CalibrationCoefficients, FilterModel, list_filters
chloros_sdk.DLS, NetworkDiagnostics
chloros_sdk.DAQUSensor, DAQMSensor, DAQESensor, SensorFleet, discover_all

# === Exceptions ===
chloros_sdk.ChlorosError                          # base
chloros_sdk.ChlorosBackendError
chloros_sdk.ChlorosLicenseError
chloros_sdk.ChlorosConnectionError
chloros_sdk.ChlorosProcessingError
chloros_sdk.ChlorosAuthenticationError
chloros_sdk.ChlorosConfigurationError
chloros_sdk.ChlorosConnectError                   # raised by smart-connect surface
chloros_sdk.LatticeError, CameraNotFoundError, ...  # from lattice_sdk

# === Availability flags ===
chloros_sdk.CAMERA_AVAILABLE     # True iff lattice_sdk imported cleanly
chloros_sdk.DAQ_AVAILABLE        # True iff daq_sdk imported cleanly
chloros_sdk.PROJECT_AVAILABLE    # True iff ChlorosProject deps available
```

---

## इमेज प्रोसेसिंग — `ChlorosLocal`

मुख्य पाइपलाइन क्लास। पहले उपयोग पर बैकएंड को स्पॉन करता है, प्रोजेक्ट बनाता/कॉन्फ़िगर करता है, प्रगति की निगरानी करता है, रन के बाद सारांश लौटाता है।

### कंस्ट्रक्टर

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

### मेथड्स

| मेथड | विवरण |
| --- | --- |
| `create_project(project_name, camera=None)` | एक नया प्रोजेक्ट बनाएँ (वैकल्पिक रूप से `"Survey3N_RGN"` जैसे कैमरा टेम्पलेट के साथ)। |
| `import_images(folder_path, recursive=False)` | RAW/TIF/JPG/DNG छवियाँ **और `.daq` लाइट-सेंसर रिकॉर्डिंग** आयात करें। `count` लौटाता है (छवियाँ) और `scan_count` (रिकॉर्डिंग)। चेतावनी केवल तभी देता है जब फ़ोल्डर में दोनों में से कोई भी न हो। |
| `export_light_sensor(daq=True, csv=True)` | कैलिब्रेटेड `.daq` +  लिखेंप्रोजेक्ट पर प्रत्येक लाइट-सेंसर रिकॉर्डिंग के लिए 72, `<project>/Light Sensor/` में। देखें [लाइट-सेंसर रिकॉर्डिंग्स](#light-sensor-recordings--calibrated-daq--csv). |
| `configure(debayer=..., vignette_correction=..., reflectance_calibration=..., indices=[...], export_format=..., ppk=..., daq_log_path=..., input_level=..., radiometric_output=..., array_alignment=..., array_alignment_crop=..., array_alignment_interpolation=..., custom_settings=None)` | प्रोसेसिंग नॉब्स सेट करें। |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | पाइपलाइन चलाएँ। XPROT लौटाता हैX000176, और जब बैकएंड एक प्रदान करता है तो एक `summary` कुंजी — देखें [पोस्ट-रन सारांश और संकेत](#post-run-summary--hints). |
| `get_config()` / `get_status()` / `status()` | बैकएंड की स्थिति की जाँच करें। |
| `logout()` | कैश किए गए क्रेडेंशियल साफ़ करें। |
| `shutdown_backend()` | बैकएंड को समाप्त करें (यदि SDK-started)। |
| `discover_cameras()` | इस इंस्टेंस के माध्यम से LATTICE कैमरों का पता लगाएँs backend** (`/api/camera/discover`). dicts (`serial`, `model`, की एक सूची लौटाता है, …) — GUI/CLI में जैसा दिखता है वैसा ही स्वरूप। यदि कोई नहीं मिला या बैकएंड तक पहुँच नहीं हो पाई तो सूची खाली होगी। `ip`, …) — वही आकार जो GUI/  देखता है। यदि कोई नहीं मिला या बैकएंड तक पहुंच नहीं हो पाई तो खाली सूची लौटाता है। |
| `camera_capture(output_dir, format="tiff", **settings)` | एक सिंगल फ्रेम कैप्चर करें***बैकएंड के माध्यम से**(इस हैंडल द्वारा स्वचालित रूप से शुरू किया गया) ताकि इसे GUI/ CLI (डिफ़ॉल्ट 12-बिट, पूल पुन: उपयोग, एम्बेडेड कैल मेटाडेटा) के समान तैयारी मिले। लक्ष्य को `serial=` या `device_index=` के साथ हल करें; पास करें `exposure`/`gain`/`pixel_format`/`preset` को `**settings` के रूप में। पुराने मेटाडेटा डिक्ट (`filepath`, `width`, `height`, `pixel_format`, `exposure_time`, `gain`, `timestamp`). |
| `camera_stream(serial, *, fps=10.0, overlay=None, decode=True, connect_timeout=10.0, read_timeout=15.0)` | पूल्ड कैमरा से ओवरले-कंपोजिटेड पूर्वावलोकन फ्रेम प्रदान करता है — बैकएंड के `/api/camera/<serial>/stream-annotated` मार्ग पर एक पतला MJPEG क्लाइंट (ज़ेब्रा / ग्रिड / क्रॉसहेयर / हिस्टोग्राम / पीकिंग / स्पॉट सर्वर-साइड पर बनाया गया)। `decode=True` BGR ऐरे देता है; `False` कच्चे JPEG बाइट्स देता है। प्रोजेक्ट-वार भी `ChlorosProject.stream(overlays=True)` के रूप में उपलब्ध है। |

गारंटीकृत सफाई के लिए एक संदर्भ प्रबंधक के रूप में उपयोग करें:

```python
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

### लाइट-सेंसर रिकॉर्डिंग — कैलिब्रेटेड `.daq` + `.csv`

एक DAQ-U / DAQ-M / DAQ-E को उसके कैलिब्रेशन बंडल **के बिना** रिकॉर्ड किया जा सकता है। यही वह है जो सार्वजनिक [`chloros_scripts`](https://github.com/mapircamera/chloros_scripts)
रिकॉर्डर (`record_daq.py`) डिफ़ॉल्ट रूप से यही करते हैं: वे कच्चे सेंसर काउंट लिखते हैं और फ़ाइल पर स्टैम्प लगाते हैं ताकि Chloros उस सेंसर का **सीरियल के अनुसार** फैक्ट्री कैलिब्रेशन प्राप्त कर सके — पहले लोकल कैश, फिर MAPIR क्लाउड — और इम्पोर्ट करते समय इसे लागू करता है।

Chloros परिणाम को प्रति रिकॉर्डिंग दो उत्पादों के रूप में वापस लिखता है, `<project>/Light Sensor/` के तहत:

| उत्पाद | यह क्या है |
| --- | --- |
| `<name>_calibrated.daq` | पुनः संसाधित करने योग्य आर्काइव — एक लाइव रिकॉर्डिंग के समान स्कीमा, अब इसे बनाने वाले बंडल की घोषणा करता है। इसे फिर से इम्पोर्ट करना इसे दूसरी बार **नहीं** कैलिब्रेट करता है। |
| `<name>_calibrated.csv` | सेंसर के अपने तरंगदैर्घ्य ग्रिड पर W/m²/nm में वर्णक्रमीय विकिरण, प्रत्येक रीडिंग के लिए एक पंक्ति, साथ ही फोटोमेट्रिक कॉलम (कुल पावर, फोटोपिक/स्कोटॉपिक लक्स, पीपीएफडी और इसका नीला/हरा/लाल विभाजन, पीक तरंगदैर्ध्य)। |
| `<name>_raw.daq` / `<name>_raw.csv` | **केवल बंडल-रहित सेंसर (DAQ-A)।** कच्ची स्पेक्ट्रल सेंसर गिनती — विकिरण नहीं। नीचे देखें। |

`process()` अपने चरणों में से एक के रूप में इस एक्सपोर्ट को करता है। इसे इमेजरी की **आवश्यकता नहीं** है:
अकेले उड़ाया गया एक लाइट सेंसर एक प्रथम-श्रेणी का वर्कफ़्लो है, और इस तरह के प्रोजेक्ट में स्वभावतः शून्य छवियाँ होती हैं।

**DAQ-A रिकॉर्डिंग्स कच्चे काउंट्स के रूप में एक्सपोर्ट होती हैं।** DAQ-A परिवार प्रति-सीरियल बंडल सिस्टम से पुराना है और इसमें प्राप्त करने के लिए कोई बंडल नहीं है — इसके बजाय इसे क्षेत्र में एक परावर्तना लक्ष्य (reflectance target) के खिलाफ कैलिब्रेट किया जाता है, यही कारण है कि इसे कभी इसकी आवश्यकता नहीं पड़ी। वे रिकॉर्डिंग निर्यात करती हैं
`_calibrated` के बजाय `_raw` स्टेम के तहत: फ़ाइल के अंदर एक फ़्लैग के बजाय एक अलग फ़ाइलनाम, क्योंकि इस दावे को एक साधारण नाम के रूप में ईमेल किए जाने पर भी बरकरार रहना होता है।
`.csv` हेडर `raw spectral sensor counts (NOT irradiance)` को दर्शाता है और चेतावनी देता है कि
मान फ़ाइल के **अंदर**तुलनीय हैं — ठीक वही जिसके लिए लक्ष्य-आधारित कैलिब्रेशन उनका उपयोग करता है — और सेंसरों के बीच नहीं। पावर-निर्भर फोटोमेट्रिक कॉलम (कुल पावर, फोटॉपिक/स्कोटॉपिक लक्स, PPFD) काउंट्स से एकीकृत होने के बजाय**NULL** लौटते हैं।

एक DAQ-U / DAQ-M / DAQ-E जिसका बंडल बस प्राप्त नहीं किया जा सका, उसे अभी भी **छोड़ दिया जाता है**, कच्चा नहीं लिखा जाता: वहाँ बंडल मौजूद है और &quot;दोबारा कनेक्ट करें और पुनः संसाधित करें&quot; एक वास्तविक सलाह है।

लीगेसी **v1.01 / v1.02** रिकॉर्डिंग (एक DAQ-A-SD इन्हें लिखता है) प्रत्येक रीडिंग के लिए कोई एपोक नहीं होता, केवल फ़ाइल का लिखने का समय होता है। इमेज↔डाउनवेलिंग मैचर अभी भी उन्हें अस्वीकार कर देता है — एक फ़्रेम का लिखने के समय से मिलान करना अदृश्य रूप से गलत होगा — लेकिन एक्सपोर्टर उन्हें पढ़ता है, और CSV `clock=daq_created_on` प्रिंट करता है ताकि उत्पाद यह बता सके कि कौन सी घड़ी यह चालू है।

```python
import chloros_sdk

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("DAQ-U_2026-08-26")
    cl.import_images("C:/Flights/raw_daq")     # .daq only — no camera involved
    result = cl.export_light_sensor()          # or just cl.process()

for rec in result["exported"]:
    print(rec["csv"])
for rec in result["skipped"]:
    print("skipped", rec["source"], "--", rec["reason"])
```

एक रिकॉर्डिंग जिसका कैलिब्रेशन बंडल प्राप्त नहीं किया जा सकता (ऑफ़लाइन, या फ़ाइल पर बिना कैलिब्रेशन वाला कोई सेंसर) को `skipped` **कारण के साथ** रिपोर्ट किया जाता है। इसे कभी भी कच्चे काउंट्स रखने वाली &quot;कैलिब्रेटेड&quot; फ़ाइल के रूप में नहीं लिखा जाता है — इंटरनेट से कनेक्ट करें और दोबारा चलाएँ, और एक्सपोर्ट पूरा हो जाता है।

### प्रगति कॉलबैक

```python
def show_progress(percent, message):
    print(f"[{percent:3d}%] {message}")

with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("FieldA")
    cl.import_images("C:/DroneImages/Flight001")
    cl.configure(indices=["NDVI"])
    cl.process(progress_callback=show_progress, poll_interval=1.0)
```

### रन के बाद का सारांश और संकेत

पूरा होने पर, `process()`, `GET /api/processing-summary` को प्राप्त करता है और बॉडी को `result["summary"]` के रूप में संलग्न करता है। यह प्राप्त करना सबसे अच्छा है-प्रयास होता है और कभी भी सफल वापसी को अवरुद्ध नहीं करता — यदि सारांश अनुपलब्ध है, तो `process()` साधारण `{"status": "complete", "async": False}` रूप पर वापस चला जाता है। `summary["hints"]` में प्रत्येक प्रविष्टि — सुझाए गए समाधानों के साथ पूर्ण वाक्यकरण, जैसे कि कोई रन शून्य आउटपुट क्यों उत्पन्न करता है — को भी एक त्रुटि संदेश (Python) `UserWarning` के रूप में पुनः प्रसारित किया जाता है, इसलिए 0-आउटपुट वाले रन स्वयं निदान करते हैं, भले ही आप कभी भी डिक्ट की जाँच न करें:

```python
result = cl.process()
for hint in result.get("summary", {}).get("hints", []):
    print("HINT:", hint)
# hints also arrive on the warnings channel:
#   python -W always::UserWarning your_script.py
```

`summary["totals"]` मशीन-पठनीय आधा हिस्सा है:

| कुंजी | यह क्या गिनती है |
| --- | --- |
| `models` | रन में कैमरा समूह। |
| `images_in_groups` | उन समूहों में स्रोत छवियाँ। |
| `targets_found` | पता लगाए गए परावर्तन लक्ष्य। |
| `images_calibrated` | रन द्वारा कैलिब्रेट की गई छवियाँ। |
| `exported_files` | **रन द्वारा लिखी गई इमेज उत्पाद फ़ाइलें।** |
| `daq_recordings_exported` / `daq_recordings_skipped` | प्रकाश-सेंसर रिकॉर्डिंग, जिन्हें जानबूझकर अलग से गिना जाता है — वे एक अलग चरण से आती हैं और उन रन के लिए मौजूद होती हैं जिनमें कोई इमेजरी नहीं होती, इसलिए उन्हें शामिल करने से एक केवल DAQ रन ऐसा दिखेगा जैसे उसने इमेज एक्सपोर्ट की हैं। |

उनके साथ: `summary["output_dirs"]` (हर डायरेक्टरी जिसमें लिखा गया है),
`summary["light_sensor_export"]`, `summary["stopped"]` (तब सही होता है जब उपयोगकर्ता ने रन को बाधित किया हो, ताकि आंशिक गिनती एक पूर्ण के रूप में न पढ़ी जाए run जिसने कम उत्पादन किया), और
`summary["groups"]` (प्रति-समूह ब्रेकडाउन)।

`exported_files` को पाइपलाइन द्वारा **लिखते समय** रिकॉर्ड किया जाता है, बाद में प्रोजेक्ट की इमेज ऑब्जेक्ट्स को स्कैन नहीं किया गया। पैरेलल और GPU रणनीतियाँ अपनी खुद की इमेज ऑब्जेक्ट्स बनाती हैं (GPU पाथ के लिए वर्कर सबप्रोसेस में), इसलिए पुरानी स्कैन हर ऐसे रन के लिए `0 file(s) written` रिपोर्ट करती थी और फिर जीरो-एक्सपोर्ट्स संकेत उत्सर्जित करती थी — उन रन पर जहाँ सब कुछ काम कर गया था। यदि आप इस संख्या के आधार पर स्क्रिप्ट बनाते हैं, तो एक स्वस्थ समानांतर रन अब एक गैर-शून्य गिनती रिपोर्ट करता है।

लाइट-सेंसर स्किप रिपोर्ट प्रत्येक फ़ाइल के लिए रीडर द्वारा वास्तव में स्थापित कारण — एक अपठनीय स्कीमा, एक गुम बंडल, एक लिखने की त्रुटि — **डुप्लिकेट हटाकर**, इसलिए बीस फ़ाइलें एक कारण से स्किप होने पर, बीस बार दोहराए जाने के बजाय एक कारण के रूप में गिनी जाती हैं।

> **`process()` तब त्रुटि नहीं दिखाता जब कोई रन कोई छवि उत्पन्न नहीं करता है।** यह एकमात्र जगह है जहाँ SDK और CLI जानबूझकर भिन्न हैं: `chloros-cli process` &quot;उत्पादों का अनुरोध किया गया था, कोई नहीं लिखा गया&quot; को एक विफलता के रूप में मानता है और गैर-शून्य पर बाहर निकलता है, जबकि SDK सामान्य रूप से लौटता है और `summary` / संकेतों के माध्यम से स्थिति की रिपोर्ट करता है। यदि आपकी पाइपलाइन को एक खाली रन पर रुकना चाहिए, तो स्वयं इसकी जाँच करें — `summary` का निरीक्षण करें (या प्रोजेक्ट फ़ोल्डर के अंतर्गत फ़ाइलों को गिनें) किसी अपवाद की अनुपस्थिति पर निर्भर रहने के बजाय। इसके सामान्य कारण हैं एक इनपुट फ़ोल्डर जिसे कैप्चर के रूप में नहीं पहचाना गया और मौजूद कैमरों के लिए अनुपयुक्त होने के कारण उत्पादों को छोड़ दिया गया (जैसे कि केवल RGB वाले कैमरों से विकिरण)।

### सुविधा फ़ंक्शन

```python
# One-call process: project + import + configure + process
results = chloros_sdk.process_folder(
    folder_path="C:/DroneImages/Flight001",
    project_name="FieldA_2026-05-26",
    camera="Survey3N_RGN",
    indices=["NDVI", "NDRE", "GNDVI"],
    vignette_correction=True,
    reflectance_calibration=True,
    export_format="TIFF (16-bit)",
    mode="parallel",
    debayer="High Quality (Faster)",      # or "Texture Aware (Slow, Highest Quality)"
    ppk=False,
    recursive=False,
    processing_timeout=14400,
)

# LATTICE-friendly defaults (no panel-target detection, standard debayer)
results = chloros_sdk.process_lattice_capture(
    folder_path="C:/Captures/2026-05-13_Field",
    indices=["NDVI"],
)

# Audit which calibration sources were applied to a processed image
tags = chloros_sdk.read_image_audit_tags("output/Reflectance_Calibrated/x.tif")
print(tags["CalibrationSource"])   # 'per_serial' / 'legacy_lookup' / 'none'
print(tags["VignetteSource"])      # 'per_serial' / 'legacy_polynomial' / 'none'
```

### समर्थित मान

```python
# export_format
"TIFF (16-bit)"           # default, recommended
"TIFF (32-bit, Percent)"  # reflectance percentage as float32
"PNG (8-bit)"
"JPG (8-bit)"

# debayer
"High Quality (Faster)"               # standard, default
"Texture Aware (Slow, Highest Quality)"  # neural debayer, Chloros+ only
"Standard (Fast, Medium Quality)"      # alias used internally for LATTICE

# input_level (LATTICE only; Survey3 .raw ignores)
"auto"        # default — infers from each file's XMP ProcessingLevel tag
"raw"         # force-treat as raw Bayer
"debayered"   # force-treat as already-debayered BGR
"processed"   # force-treat as already-calibrated radiance

# array_alignment / array_alignment_crop (LATTICE arrays; None = keep saved setting)
True          # backend default — apply the module-to-module transform stamped
              # in each capture's Chloros:Alignment* XMP to every product
False         # export in native sensor geometry / skip the common-overlap crop

# array_alignment_interpolation (alignment warp resampling)
"bilinear"    # backend default
"nearest"     # preserves exact source DNs (no inter-pixel value mixing)
"cubic"
```

#### रेडियोमेट्रिक आउटपुट (लैटिस मल्टीस्पेक्ट्रल पाइपलाइन)

`process` पाइपलाइन का LATTICE मल्टीस्पेक्ट्रल (M3C/M3M) एक्सपोर्ट लेवल — `reflectance` (डिफ़ॉल्ट), `radiance`, `sensor-response`, या `all` (प्रति छवि हर लागू मोड) — प्रोजेक्ट की **&quot;रेडियोमेट्रिक आउटपुट&quot;** प्रोसेसिंग सेटिंग से मेल खाता है। `configure()` के लिए एक समर्पित कीवर्ड है:

```python
with chloros_sdk.ChlorosLocal() as cl:
    cl.create_project("Field_A")
    cl.import_images("C:/Captures/lattice_flight")
    cl.configure(
        radiometric_output="radiance",   # reflectance (default) / radiance / sensor-response / all
        export_format="TIFF (32-bit, Percent)",
    )
    cl.process()
```

उन्नत एस्केप हैच — प्रोजेक्ट के`"Radiometric output"` कुंजी के माध्यम से `custom_settings` — अभी भी काम करता है, लेकिन याद रखें कि यह पूरे सेटिंग्स ब्लॉक को बदल देता है (नीचे दी गई चेतावनी देखें):

```python
cl.configure(custom_settings={
    "Project Settings": {
        "Processing": {"Radiometric output": "radiance"},
        "Export": {"Calibrated image format": "TIFF (32-bit, Percent)"},
    }
})
```

`reflectance` (डिफ़ॉल्ट) कैमरा रेडियंस को **टाइमस्टैम्प-मैच्ड DAQ डाउनवेलिंग**से विभाजित करता है, रिकॉर्ड किए गए `.daq` (DAQ-U/M/E)**या DAQ-M नेटिव `.csv`* से स्वचालित रूप से हल किया जाता है* जो इमेजरी के साथ पाया जाता है; कोई भी प्रति-कैमरा या DAQ कैलिब्रेशन बंडल जो स्थानीय रूप से अनुपस्थित है, पहली बार उपयोग पर **AWS से स्वचालित रूप से प्राप्त** किया जाता है। CLI इसे `chloros-cli process` पर प्रति-प्रकार उत्पाद टॉगल के रूप में प्रदर्शित करता है: `--radiance`/`--no-radiance`, `--reflectance`/`--no-reflectance`, `--debayered`, `--preview`.

> `custom_settings` **पूरे गणना किए गए सेटिंग्स ब्लॉक को बदलता है (यह डिज़ाइन के अनुसार `configure()` के अन्य कीवर्ड और सत्यापन को बायपास करता है)। जब आप इसका उपयोग करते हैं, तो जैसा कि ऊपर दिए गए उदाहरण में है, हर उस `Project Settings` कुंजी को शामिल करें जो आपके लिए महत्वपूर्ण है।

---

## LATTICE कैमरों के लिए स्मार्ट-कनेक्ट

लाइव हार्डवेयर के लिए स्थायी बैकएंड सत्र। GUI द्वारा उपयोग किए जाने वाले वही एंडपॉइंट, इसलिए SDK / CLI / GUI में व्यवहार समान है।

### सिंगल कैमरा — `CameraSession`

```python
import chloros_sdk

# Open by serial; reuses existing pool entry if one exists
with chloros_sdk.connect_camera("213800234") as cam:
    # cam is a CameraSession; supports context manager + manual disconnect
    cam.set_settings(
        exposure_time=10000,    # microseconds
        gain=0.0,               # dB
        pixel_format="BayerRG12",
        target_brightness=80,
        ae_damping=8.0,
    )
    cam.capture("output/", ext=".tiff")
```

#### `connect_camera()` सिग्नेचर

```python
connect_camera(
    serial,
    *,
    preset=None,                       # "default" | "high_quality" | "high_speed" | "triggered"
    settings=None,                     # dict overlaid on the preset
    backend_url="http://127.0.0.1:5000",  # deliberately not 'localhost' (::1-first on Windows ≈ 2 s/request)
    timeout=60.0,
    auto_start_backend=True,           # spawn a local backend if none is running
) -> CameraSession
```

#### `CameraSession` मेथड्स

| मेथड | विवरण |
| --- | --- |
| `read_nodes(names, enum_names=(), timeout=30.0)` | GenICam नोड्स पढ़ें; `{nodes, errors, enums, device}` लौटाता है। |
| `set_settings(**kwargs)` | फ्रेंडली नाम से नोड्स लिखें (`exposure_time`, `gain`, `pixel_format`, `width`, `height`, `target_brightness`, `ae_damping`, `ae_upper_limit`, `trigger_mode`, `trigger_source`, …). |
| `capture(output_dir="output", ext=".tiff", jpeg_quality=95, processing=None, levels=None, force_daq=None, settings=None, timeout=None)` | एक **एकल** फ़्रेम कैप्चर करें। फ़्रेम मेटाडेटा डिक्शनरीज़ की एक-तत्वीय सूची लौटाता है। (बर्स्ट/मल्टी-फ़्रेम कैप्चर हटा दिया गया था — यदि आपको एक श्रृंखला की आवश्यकता है तो लूप में `capture()` को कॉल करें।) |
| `disconnect()` | पूल से रिलीज़ करें। यदि हम पहले से खुले सत्र से जुड़े हैं तो नो-ऑप। |

`capture()` निर्यात नियंत्रण (एरे + GUI के समान मॉडल):

- `processing` / `levels` — `processing="all"` सहेजता है हर लागू निर्यात प्रकार; `levels=["raw","radiance"]` केवल उन्हीं को सहेजता है (`processing` को ओवरराइड करता है)। बैकएंड डिफ़ॉल्ट के लिए दोनों को छोड़ दें।
- `force_daq=True` — एक रॉ-ओनली ग्रैब पर भी, असाइन की गई DAQ/DLS रीडिंग को एक `.daq` साइडकार के रूप में सहेजता है, ताकि फ्रेम को बाद में रिफ्लेक्टेंस/इंडेक्स में फिर से प्रोसेस किया जा सके। यदि कोई DAQ लिंक नहीं है तो यह निष्क्रिय (No-op) रहता है।

### सिंक्रोनाइज़्ड ऐरे — `ArraySession` (स्मार्ट-प्रैप)

`connect_array` मल्टी-कैमरा सेटअप के लिए **अनुशंसित एंट्री पॉइंट** है। यह बैकग्राउंड में पूरा GUI स्मार्ट-प्रैप फ्लो चलाता है:

1. **नेटवर्क विश्लेषण** (`/api/camera/array/recommend`) — बिना फ्रेम ड्रॉप किए sim-emit tier में फिट होने वाले सबसे बड़े फ्रेम आकार को खोजता है।
2. **टियर ऑटो-पिक** — यदि वायर इसे संभाल सकता है तो `sim-capture-sim-emit`; अन्यथा `sim-capture-ftd-stagger` या `slip-emit-and-capture`।
3. **ऑटो-श्रिंक**— जब वायर अनुरोधित रिज़ॉल्यूशन को बनाए नहीं रख सकता है तो यह चुपचाप फ़्रेम आकार को छोटा कर देता है / बिनिंग बढ़ा देता है।**यह सुरक्षा जाल एग्रीगेट ओवर-सब्सक्रिप्शन को कवर नहीं करता है**: वायर के लिए बहुत अधिक कैम को फ़्रेम को छोटा करके ठीक नहीं किया जा सकता — देखें [ओवर-सब्सक्रिप्शन](#over-subscription-the-per-cam-floor).
4. **PTP सक्षम**डिफ़ॉल्ट रूप से — क्रॉस-कैमरा टाइमस्टैम्प एक साझा घड़ी पर**~1 ms**तक आते हैं। एक साथ एक्सपोज़र M8 हार्डवेयर ट्रिगर से आता है (**&lt; 100 µs** इंटर-मॉड्यूल), PTP से नहीं: PTP *टाइमस्टैम्प* को संरेखित करता है, एक्सपोज़र को नहीं।
5. **प्रति-कैम पिक्सेल-फ़ॉर्मेट स्वचालित-चयन** — RGB कैम → `BayerRG8`, मल्टीस्पेक्ट्रल → `BayerRG12`।
6. **AE सीडिंग** — प्रत्येक कैम की वर्तमान AE स्थिति का स्नैपशॉट लेता है ताकि कनेक्ट उड़ान के दौरान एक्सपोज़र को रीसेट न करे।
7. **GPIO ट्रिगर कॉन्फ़िगर** — `connect_array` हर कैमरे (`TriggerMode=On`, `TriggerSource=Line2`) को तैयार करता है ताकि मास्टर का पल्स M8 केबल के माध्यम से स्लेव्स को ड्राइव करता है। यह केवल-एरे चरण है: `LatticeCamera` से एकल कैमरा खोलने पर यह इसके बजाय फ्री-रन करता है।

```python
import chloros_sdk

# First serial is the MASTER (fires the trigger pulse); rest are slaves.
with chloros_sdk.connect_array(
        ["213800234", "214000533", "214701288", "214701292"]) as arr:
    print(arr.array_id, arr.sync_mode, arr.ptp_enabled)
    arr.capture("output/", processing="reflectance")
```

#### `connect_array()` सिग्नेचर

```python
connect_array(
    serials,                              # list[str]; serials[0] = master
    *,
    line="Line2",                         # GPIO sync line: Line0 | Line2 | Line3
    target_fps=None,                      # master trigger fire rate (auto if None)
    force_tier=None,                      # override tier picker; see below
    wire_ceiling_mbps=None,               # host sustained wire budget, MB/s (auto if None)
    width=None,                           # explicit frame size; skips network analysis
    height=None,
    pixel_format=None,
    binning=None,
    recommend=True,                       # set False to skip the recommend step
    ptp_enable=True,                      # set False to disable PTP
    backend_url="http://127.0.0.1:5000",  # same IPv6-avoidance default as connect_camera
    timeout=180.0,
    auto_start_backend=True,              # spawn a local backend if none is running
) -> ArraySession
```

`force_tier` मान:
- `"sim-capture-sim-emit"` — सच्ची समवर्ती (सभी कैमरे एक ही क्लॉक एज पर फायर करते हैं)।
- `"sim-capture-ftd-stagger"` — लचीला टाइम-डोमेन स्टैगर (कैमरे थोड़े अलग समय पर उत्सर्जित करते हैं ताकि पैकेट वायर पर सीरियलाइज़ हो जाएं)।
- `"slip-emit-and-capture"` — प्रति-कैम अनुक्रमिक कैप्चर (कोई समय-आधारित सिंक नहीं; केवल तभी विकल्प जब कोई फ्रेम आकार सिम में फिट न हो)`wire_ceiling_mbps` **होस्ट के निरंतर वायर बजट** (MB/s में) को ओवरराइड करता है — यह एकमात्र संख्या है जिस पर पूरी एरे आवंटन निर्भर करता है। ऑटो-डिटेक्टेड मान का उपयोग करने के लिए इसे `None` पर छोड़ दें। जब एरे GVSP-खराब फ़्रेम की रिपोर्ट करता है तो इसे कम करें: स्वचालित मान NIC की विज्ञापित लिंक दर से लिया जाता है, जो USB एडाप्टर, पतले PCIe लेन और व्यस्त साझा फैब्रिक को ज़्यादा आँकता है — और यह अति-अनुमान एक स्पष्ट रूप से धीमे लिंक के बजाय भ्रष्ट फ्रेम के रूप में सामने आता है। यह मान प्रोजेक्ट के एरे कैप्चर ब्लॉक में बना रहता है, इसलिए एक रीओपन या बाद का `connect_array` इसे किसी अन्य एरे सेटिंग की तरह ही बहाल कर देता है।
[एरे हेल्थ](#array-health--which-subsystem-is-losing-frames) देखें।

#### ओवर-सब्सक्रिप्शन (प्रति-कैमरा न्यूनतम सीमा)

Sim-emit पेसिंग प्रत्येक कैमरा को कोलिज़न-सेफ़ वायर बजट का एक हिस्सा आवंटित करती है, जिसकी न्यूनतम सीमा **प्रति कैमरा 8 एमबी/सेकंड**(`per_cam_floor_bps`) है. एक बार जब `N × floor` कोलिसन-सेफ सीमा से अधिक हो जाता है, तो एरे**वायर का ओवर-सब्सक्रिप्शन कर देता है**— विफलता का तरीका GVSP पैकेट हानि है, कम फ्रेम दर नहीं — और फ्रेम-आकार का कोई समाधान मौजूद नहीं है:**बिनिंग और ROI प्रति फ्रेम बाइट्स को कम करते हैं, प्रति सेकंड पेस्ड बाइट्स को नहीं**जिसकी समग्र जांच तुलना करती है। 1 GbE होस्ट पर व्यावहारिक पूर्ण-रिज़ॉल्यूशन सीमाएँ:**6 कैम @ 1500 MTU, जंबो फ्रेम के साथ 9** (विश्लेषण प्रतिक्रिया में `max_cams_collision_safe` आपके वायर के लिए सीमा बताता है)। उपाय: कम कैमरे, एंड-टू-एंड जंबो फ्रेम, या एक तेज़ NIC।

- `analyze_array_network()` और `/api/camera/array/connect` प्रतिक्रियाओं में `oversubscribed` शामिल है, `aggregate_demand_bps`, `collision_safe_ceiling_bps`, `max_cams_collision_safe`, और `per_cam_floor_bps`। जब `oversubscribed` सत्य होने पर, प्रक्षेपण fps फ़ील्ड्स को शून्य कर देता है** (`achievable_fps_max` / `fps_bright` / `fps_dark`) के बजाय एक भ्रामक धीमी-लेकिन-कार्यशील दर की रिपोर्ट करने के बजाय।
- `POST /api/camera/array/connect` एक `pin_resolution` बॉडी पैरामीटर स्वीकार करता है (**केवल HTTP — कोई SDK kwarg नहीं**; `connect_array` इसे एक्सपोज नहीं करता है)। पिनिंग बिनिंग वॉक-डाउन सेफ्टी नेट को हटा देती है, इसलिए `pin_resolution` सेट के साथ एक ओवर-सब्सक्राइब्ड कनेक्ट को हर रेमेडी का नाम बताने वाली एक त्रुटि के साथ**हार्ड-रिफ्यूज़्ड** कर दिया जाता है। पिनिंग के बिना, कनेक्ट वॉक-डाउन के साथ आगे बढ़ता है लेकिन चेतावनी देता है कि श्रिंक करना एग्रीगेट को साफ़ नहीं कर सकता है।
- बेंच-वर्क एस्केप हैच: अस्वीकृति को एक ज़ोरदार चेतावनी में डाउनग्रेड करने के लिए बैकएंड के वातावरण में `CHLOROS_ARRAY_ALLOW_OVERSUBSCRIBED=1` सेट करें — आप फिर भी कनेक्ट करते हैं और पैकेट हानि को स्वीकार करते हैं।

#### एरे की स्थिति — कौन सा सबसिस्टम फ्रेम खो रहा है

`GET /api/camera/array/<array_id>/capability` एक जुड़े हुए एरे पर एक लाइव `health` ब्लॉक ले जाता है, जिसका पुनर्मूल्यांकन एक रोलिंग **10-सेकंड** की विंडो पर। यह फ्रेम हानि को दो कारणों में विभाजित करता है जिनके लिए विपरीत समाधानों की आवश्यकता होती है, एक &quot;अपूर्ण&quot; दर के बजाय जो दोनों में से किसी का भी नाम नहीं बताती है:

| फ़ील्ड | इसका क्या मतलब है | कौन सा सबसिस्टम |
| --- | --- | --- |
| `gvsp_corrupt_rate_pct` (प्रति सीरियल) | फ्रेम **पहुँचा और संरचनात्मक रूप से खराब था**— GVSP पैकेट हानि। |**नेटवर्क**: वायर बजट, पेसिंग, NIC RX रिंग, MTU |
| `never_arrived_rate_pct` (प्रति सीरियल) | फ्रेम **कभी आया ही नहीं**— कैमरा फायर नहीं हुआ, या कुछ भी उससे बाहर नहीं गया। |**ट्रिगर / सिंक**: M8 केबल, `line=`, `TriggerMode` |
| `worst_gvsp_corrupt_pct` / `worst_never_arrived_pct` | सबसे खराब कैमरे की दर प्रत्येक के लिए। | — |
| `per_cam_rate_pct` | प्रति कैमरा संयुक्त अपूर्ण दर (दोनों कारणों को मिलाकर)। | — |
| `stable_for_seconds` | प्रत्येक कैमरा 0.01 % से नीचे कितनी देर तक रहा। | — |

`health` के साथ-साथ, यही रिकॉर्ड वह संख्या रिपोर्ट करता है जिससे पूरी आवंटन निर्भर करती है:

| फ़ील्ड | इसका क्या मतलब है |
| --- | --- |
| `wire_ceiling_mbps` | होस्ट का लागू स्थिर वायर बजट, एमबी/सेकंड। |
| `wire_ceiling_source` | वह संख्या कहाँ से आई, शब्दों में — जैसे `USB-capped 200 MB/s (was theoretical 1062; …)` या `user override 120 MB/s (auto said 200)`। |
| `wire_ceiling_is_user_set` | `true` जब `wire_ceiling_mbps=` इसे सेट करता है। |
| `nic_is_usb` | USB ईथरनेट एडाप्टर के लिए 5। |

इस एंडपॉइंट के लिए कोई SDK रैपर नहीं है — इसे सीधे पढ़ें:

```python
import requests, chloros_sdk

arr = chloros_sdk.attach_array(["213800234", "214000533"])
h = requests.get(
    f"http://127.0.0.1:5000/api/camera/array/{arr.array_id}/capability",
    timeout=10).json()

health = h.get("health", {})
print("wire ceiling:", h["wire_ceiling_mbps"], "MB/s", h["wire_ceiling_source"])
print("corrupt (network) :", health.get("worst_gvsp_corrupt_pct"), "%")
print("absent  (trigger) :", health.get("worst_never_arrived_pct"), "%")

if (health.get("worst_gvsp_corrupt_pct") or 0) > 1.0:
    # Network path. Reconnect with a lower budget -- NOT a lower target_fps.
    arr.disconnect()
    arr = chloros_sdk.connect_array(serials, wire_ceiling_mbps=120)
```

**इसे पढ़ना:** non-zero `gvsp_corrupt_rate_pct` के साथ `never_arrived_rate_pct` का मान 0 का मतलब है कि ट्रिगरिंग और केबल सिंक एकदम सही हैं और 100 % नुकसान नेटवर्क पथ पर है — `wire_ceiling_mbps` को कम करें और फिर से कनेक्ट करें। इसके विपरीत पैटर्न सिंक केबल या
ट्रिगर लाइन के बजाय।

> **`target_fps` भ्रष्ट फ़्रेमों के लिए लीवर नहीं है।** GevSCPD पेसिंग कनेक्ट होने पर एक बार लिखी जाती है, इसलिए ट्रिगर दर को कम करने से ड्यूटी साइकिल बदलता है, न कि समवर्ती-उत्सर्जन बर्स्ट दर। मापी गई 5× मांग कटौती से कोई सुधार नहीं हुआ, जबकि वायर सीलिंग को 240 से 200 MB/s तक घटाने से वही रिग 10.4 % भ्रष्ट से
> 0.00 %।

> **TRI032S फर्मवेयर पर मिड-स्ट्रीम ऑटो-श्रिंक उपलब्ध नहीं है।** एक चल रहा एरे
> इसे स्वयं ठीक नहीं कर सकता; डिस्कनेक्ट करें और फिर से कनेक्ट करें ताकि कनेक्ट-समय पिकर नई सीमा के अनुसार फिर से योजना बनाता है।

एक **USB ईथरनेट एडाप्टर को इसकी नाम-प्लेट की परवाह किए बिना** प्रोब द्वारा 200 MB/s पर सीमित किया जाता है: दक्षता तालिका जो लिंक दर को एक स्थायी आंकड़े में बदलती है वह है
PCIe-व्युत्पन्न, और एक USB NIC अपनी ईथरनेट लिंक दर का विज्ञापन करता है जबकि यह USB बस और उसके ड्राइवर द्वारा सीमित होता है। यह सीमा एक निश्चित सीमा है, कोई अंश नहीं — एक USB 1 GbE एडाप्टर ~80 MB/s प्राप्त करता है और इससे प्रभावित नहीं होता है।

#### `ArraySession` विधियाँ

| विधि | विवरण |
| --- | --- |
| `status(timeout=10.0)` | लाइव `{fps, ptp, frame_count, last_error, …}`। |
| `capture(output_dir="output", format="tiff", processing="debayered", levels=None, aligned=None, render_index=None, force_daq=None, smart=False, timeout=300.0)` | एक सिंक किया गया कैप्चर समूह। एक `CaptureResult` लौटाता है (फ्रेम डिक्शन्स की सूची + `.skipped`). नीचे निर्यात नियंत्रण दिए गए हैं। |
| `capture(..., smart=True)` | **स्मार्ट कैप्चर** — सभी कैम्स पर AE के स्थिर होने तक प्रतीक्षा करता है, फिर ट्रिगर करता है। |
| `capture_fastest(output_dir="output", force_daq=True, render_index=True, timeout=120.0)` | सबसे तेज़ कैप्चर: केवल कच्चा + असाइन किया गया DAQ रीडिंग (+ मुफ्त संयुक्त सूचकांक)। GUI &quot;सबसे तेज़ कैप्चर&quot; बटन को दर्शाता है। |
| `capture_repeated(output_dir="output", count=None, duration_s=None, interval_s=0.0, on_capture=None, **capture_kwargs)` | एकल / निरंतर / एक सीमित लूप में अंतराल। `list[CaptureResult]` लौटाता है।**`count` और/या `duration_s` की आवश्यकता है** इसलिए यह समाप्त हो जाता है (SDK में Ctrl+C नहीं है)। |
| `record(output_dir="output", fps=10.0, duration_s=None, video=True, gif=False, timeout=30.0)` | लाइव संयुक्त रिकॉर्डिंग शुरू करें-इंडेक्स व्यू को वीडियो/GIF में बदलें → `RecorderHandle`। प्रति एरे एक कंपोजिट रिकॉर्डर। |
| `burst(output_dir="output", duration_s=None, max_frames=None, index_config=None, serial_index_config=None, timeout=30.0)` | उच्च-fps वाला रॉ-बायर बर्स्ट शुरू करें → `RecorderHandle`। `build_video()` के साथ ऑफ़लाइन पुनः संसाधित करें। |
| `build_video(burst_dir, products=None, fps=10.0, video=True, gif=False, save_tiffs=False, wait=True, poll_s=2.0, timeout=1800.0)` | एक सहेजे गए कच्चे बर्स्ट को कैलिब्रेटेड वीडियो(ओं) में ऑफ़लाइन पुनः संसाधित करें। पूरा होने तक ब्लॉक करता है (`wait=True`) और `{outputs, errors, combined}` लौटाता है। |
| `build_video_status(job_id, timeout=15.0)` | एक ऑफ़लाइन बिल्ड जॉब को पोल करें: `{running, result, error, burst_dir}`। |
| `disconnect()` | पूरी एरे को रिलीज़ करें। |

`capture()` एक्सपोर्ट कंट्रोल (वही एंडपॉइंट जिसका GUI/ CLI उपयोग करते हैं):

- `processing` / `levels` — `processing="all"` (या `levels=["raw","radiance",…]`) प्रत्येक कैम के लिए हर लागू निर्यात प्रकार को सहेजता है; एक एकल `processing` मान केवल उसी स्तर को सहेजता है।
- `aligned=True` — हर सदस्य के गैर-कच्चे एक्सपोर्ट को एरे के [संरेखण प्रोफ़ाइल](#array-alignment) (सह-पंजीकृत) पर वॉर्प करता है; कच्चा (raw) बिना वॉर्प किए रहता है लेकिन मेटाडेटा में ट्रांसफॉर्म को ले जाता है। यदि एरे में कोई प्रोफ़ाइल नहीं है तो यह असंगत (unaligned) पर वापस चला जाता है (परिणाम के `alignment` में एक चेतावनी दिखाई जाती है)।
- `render_index=False` — प्रति-कैमरे वनस्पति-सूचकांक ओवरले को छोड़ें; डिफ़ॉल्ट रूप से इसे कॉन्फ़िगर किए गए स्थान पर रेंडर करता है।
- `force_daq=True` — असाइन की गई DAQ/DLS रीडिंग को एक `.daq` साइडकार के रूप में सहेजें, भले ही किसी भी चुने हुए स्तर को इसकी आवश्यकता न हो।

**TIFF संपीड़न (HTTP-केवल नॉब):**`ArraySession.capture()` कोई `compression` कुंजी नहीं भेजता है, इसलिए बैकएंड डिफ़ॉल्ट लागू होता है — `POST /api/camera/array/capture` एक `compression` बॉडी पैरामीटर पढ़ता है, `"deflate"` डिफ़ॉल्ट रूप से (lossless zlib L1 + क्षैतिज प्रिडिक्टर, ~4.1 MB प्रति पूर्ण-रिज़ॉल्यूशन फ़्रेम)। `"none"` बिना संपीड़ित (~6.3 MB/फ़्रेम) लिखता है, एक**~5× तेज़ लेखन* के साथ* — दोनों लॉसलेस हैं और इम्पोर्ट पर समान रूप से पढ़े जाते हैं। SDK इसके लिए कोई kwarg नहीं देता; इसका बचाव का रास्ता `chloros-cli lattice array-capture --compression none` या कच्चा HTTP है। DEFLATE भी Python GIL को धारण करता है, इसलिए संपीडित लेखन प्रति-कैम लेखक थ्रेड्स में समानांतर नहीं होते हैं — सेंसर दर पर निरंतर 8-कैम फुल-रेस कैप्चर के लिए `compression: "none"` की आवश्यकता होती है। विवरण: [CLI संदर्भ → array-capture](cli-reference.md).

**प्रति-सदस्य एक्सपोर्ट ओवरराइड (केवल HTTP):**वही एंडपॉइंट `exclude_serials` को भी स्वीकार करता है (सूची — सहेजे गए सेट से सदस्यों को हटाता है; एरे फिर भी एक सिंक किए गए समूह के रूप में ट्रिगर होता है और हटाए गए सदस्यों को `excluded` में लौटाया जाता है), `serial_levels` (`{serial: [level tokens]}` प्रति-कैम स्तर ओवरराइड), और `serial_index` (`{serial: bool}` प्रति-कैम इंडेक्स-ओवरले ओवरराइड्स)। ये GUI-पैरिटी बॉडी पैरामीटर हैं और अभी**SDK** kwargs नहीं हैं; मैप्स में अनुपस्थित सदस्य एरे-व्यापी `levels` / `render_index` पर वापस चले जाते हैं।

##### स्किप किए गए कैम्स का निरीक्षण — `CaptureResult.skipped`

`ArraySession.capture()` एक `CaptureResult` लौटाता है, जो एक `list` उप-क्लास है: इसे दोहराएँ, इसे इंडेक्स करें, इसे `len()` करें — हर मौजूदा पैटर्न काम करता रहता है। नया कोड यह देखने के लिए `.skipped` एट्रिब्यूट की जाँच कर सकता है कि किन कैम्स को बाहर रखा गया था और क्यों। सबसे आम मामला मिश्रित-फ़िल्टर एरे में RGB कैम्स का है जब आप `render_index` के लिए पूछते हैं।0416 या `"reflectance"` — प्रति-बायर रेडियंस एक ब्रॉडबैंड सेंसर के लिए निरर्थक है, इसलिए बैकएंड उन कैम्स को बकवास उत्पन्न करने के बजाय छोड़ देता है।

```python
with chloros_sdk.connect_array(serials) as arr:
    result = arr.capture("output/", processing="reflectance")

    # Back-compat: iterate as a plain list
    for frame in result:
        print(frame["filepath"], frame["serial"])

    # New: see why N-1 cams were saved
    for skip in result.skipped:
        print(f"skipped SN:{skip['serial']} reason={skip['reason']}")
        # e.g. {'serial': '214701292', 'level': 'reflectance',
        #       'reason': 'reflectance-not-applicable-to-rgb-cam',
        #       'filter': 'RGB'}
```

कारण टोकन `<level>-not-applicable-to-rgb-cam` पैटर्न का पालन करते हैं (प्रत्येक छोड़े गए स्तर के लिए एक प्रविष्टि, प्रत्येक में XPR होता हैOTX000419)। परावर्तन-विशिष्ट स्किप्स हैं `reflectance-skipped-no-fresh-dls` (कोई नई डाउनवेलिंग रीडिंग उपलब्ध नहीं है), `reflectance-skipped-bound-daq-unavailable (…)` (बाउंड DAQ तक नहीं पहुँचा जा सका), और `dls-uncalibrated-band-<nm>` — यह बैंड अधिकांशतः DAQ लाइट सेंसर की रेडियोमेट्रिक रूप से कैलिब्रेटेड सीमा के बाहर है (~374–974 nm), इसलिए निरपेक्ष DAQ-आधारित परावर्तन विभाजन अस्वीकार कर दिया जाता है और फ़्रेम ज़ोर से सेंसर-प्रतिक्रिया पर डाउनग्रेड हो जाता है। शिपिंग SKU में से केवल F988 इसे ट्रिगर करता है; उस कैमरे का समर्थित पथ परावर्तन-पैनल वर्कफ़्लो है।

`processing` स्तर:

| स्तर | आउटपुट |
| --- | --- |
| `"raw"` | सेंसर से सीधे सिंगल-चैनल बायर (मोनो कैम्स: एकल बैंड)। |
| `"debayered"` *(डिफ़ॉल्ट रूप सेSDK)* | बाइलिनियर डेमोसाइक के माध्यम से 3-चैनल BGR (मोनो कैम्स: 1-चैनल ग्रेस्केल)। |
| `"radiance"` | पूर्ण रेडियोमेट्रिक श्रृंखला के माध्यम से float32 W/m²/sr/nm। केवल मल्टीस्पेक्ट्रल — RGB कैम्स को छोड़ दिया जाता है। |
| `"reflectance"` | uint16 0..32768 (Pix4D-तैयार); निरपेक्ष संदर्भ के लिए एक लाइव DAQ पेयरिंग की आवश्यकता है। केवल बहु-स्पेक्ट्रल। |
| `"display"` | पूरी चेन जो GUI पूर्वावलोकन (कैम की प्रोफ़ाइल के अनुसार CCM + WB + गामा) से मेल खाती है। |
| `"all"` | प्रत्येक कैमरे के लिए **प्रत्येक लागू स्तर के लिए एक फ़ाइल** (GUI &quot;कैप्चर ऑल&quot; / CLI से मेल खाती है। डिफ़ॉल्ट)। लौटाया गया `CaptureResult` फिर प्रत्येक `(cam, level)` के लिए एक फ़्रेम डिक्ट रखता है, जिसमें प्रत्येक डिक्ट में स्तर होता है; अनुपयुक्त स्तर `.skipped` में दिखाई देते हैं। किसी भी परावर्तन फ्रेम के लिए उपयोग किया गया DAQ रीडिंग एक `.daq` साइडकार के रूप में सहेजा जाता है। |

> **नोट — डिफ़ॉल्ट CLI से भिन्न है।** `ArraySession.capture()` का डिफ़ॉल्ट `processing="debayered"` है; `chloros-cli lattice array-capture` कमांड का डिफ़ॉल्ट `processing="all"` है। SDK से CLI/GUI बहु-स्तरीय सेव को दर्शाने के लिए `processing="all"` को स्पष्ट रूप से पास करें।

### कैप्चर मोड और रिकॉर्डर

एरे सरफेस GUI कैप्चर पैनल को दर्शाता है: सिंगल / निरंतर / अंतराल / सबसे तेज़ शटर मोड, साथ ही दो रिकॉर्डर (लाइव-कंपोजिट वीडियो और रॉ बर्स्ट → ऑफ़लाइन रीप्रोसेस)।

```python
import time, chloros_sdk

with chloros_sdk.connect_array(serials) as arr:
    # Single (default) — one synced group
    arr.capture("out/", processing="reflectance")

    # Fastest — raw + .daq + combined index now, calibrate later
    arr.capture_fastest("flightline/")

    # Interval — one reflectance pass every 2 s, 5 passes (bounded so it ends)
    arr.capture_repeated("timelapse/", count=5, interval_s=2.0,
                         processing="reflectance",
                         on_capture=lambda i, r: print(f"pass {i}: {len(r)} frames"))

    # Combined-index video/GIF recorder (needs the combined live view streaming)
    with arr.record("monitoring/", fps=10, gif=True) as rec:
        time.sleep(30)
    print(rec.result["video_path"])

    # Raw-Bayer burst → offline reprocess into calibrated video(s)
    with arr.burst("capture/", duration_s=5) as b:
        pass
    out = arr.build_video(b.result["out_dir"], products=[
        {"kind": "per_cam", "level": "reflectance"},
        {"kind": "combined", "level": "index"}])
    print(out["outputs"])
```

- **`capture_repeated`** **SDK**का Continuous/Interval लूप है। क्योंकि इसे स्क्रिप्ट से तोड़ने के लिए कोई `Ctrl+C` नहीं है, आप**अनिवार्य रूप से** `count` और/या `duration_s` पास करें (यह पहुँचने पर रुक जाता है)। `interval_s` प्रत्येक पास की शुरुआत से मापा जाता है (GUI से मेल खाता है)।. शेष kwargs सीधे `capture()` को पास हो जाते हैं।
- **`record`** *निगरानी-ग्रेड* है: यह लाइव संयुक्त-सूचकांक कंपोजिट को प्रदर्शित के रूप में कैप्चर करता है, इसलिए फ्रेम आने के लिए संयुक्त स्ट्रीम खुला होना चाहिए। प्रति एरे एक कंपोजिट रिकॉर्डर (यदि कोई पहले से चल रहा है तो त्रुटि उत्पन्न करता है)।
- **`burst` → `build_video`** है *विश्लेषण-ग्रेड*: `burst` कच्चे फ्रेम + प्रति-फ्रेम मैनिफेस्ट + प्रत्येक अलग DLS रीडिंग के तहत एक `.daq`, `<output>/bursts/<base>/` के तहत ग्रैब लूप की पूरी दर पर लिखता है (कोई चेन नहीं, कोई exiftool नहीं, कोई लाइव व्यू नहीं)। `build_video` प्रत्येक फ्रेम को निकटतम `.daq` से समय-संरेखित करता है और इम्पोर्ट पाइपलाइन की रेडियंस/रिफ्लेक्टेंस/इंडेक्स चेन को फिर से चलाता है। `products` `{"kind": "per_cam"|"combined", "level": "radiance"|"reflectance"|"index"}` की एक सूची है (डिफ़ॉल्ट: संयुक्त इंडेक्स)। `burst().stop()` एक सर्वोत्तम-प्रयास संयुक्त-इंडेक्स बिल्ड को भी स्वचालित रूप से शुरू करता है, जो स्टॉप परिणाम में `build_job` के रूप में लौटाया जाता है।

#### `RecorderHandle`

`ArraySession.record()` और `ArraySession.burst()` द्वारा लौटा गया। इसका उपयोग एक संदर्भ प्रबंधक (context manager) के रूप में करें ताकि स्कोप से बाहर निकलने पर यह स्वचालित रूप से रुक जाए, या इसे मैन्युअल रूप से चलाएं।

| सदस्य | विवरण |
| --- | --- |
| `job_id` | बैकएंड जॉब आईडी (स्ट्र)। |
| `kind` | `"composite"` (`record` से) या `"raw"` (`burst` से)। |
| `start_stats` | XPROT द्वारा लौटाया गया dictX000467 कॉल। |
| `result` | `None` चलने के दौरान; एक बार रुकने पर अंतिम स्टॉप-रिजल्ट डिक्ट। |
| `stats(timeout=10.0)` | लाइव जॉब आँकड़े (लिखे गए फ्रेम, वास्तविक एफपीएस, बीता हुआ समय)। |
| `stop(timeout=60.0)` | रिकॉर्डर को रोकें; अंतिम परिणाम को लौटाता है और कैश करता है। इडेम्पोटेंट (दूसरी कॉल कैश किया गया परिणाम लौटाती है)। |

```python
rec = arr.burst("capture/")
# ... drive manually ...
print(rec.stats()["frames"])
result = rec.stop()
print(result["out_dir"], result.get("build_job"))
```

### पहले से जुड़े एरे से अटैच करना — `attach_array`

यदि एरे पहले से ही चालू है (GUI ने इसे खोला है, या पिछला SDK सत्र `connect_array` को कॉल किया गया), पुनः कनेक्ट करने के बजाय `attach_array` का उपयोग करके इसका हैंडल प्राप्त करें। `connect_array` <sn><id>उस स्थिति में</id></sn> हमेशा &quot;कैमरा <sn>पहले से ही एरे में </sn>है<sn><id>&quot;</id></sn> के साथ त्रुटि देता है<sn><id>, क्योंकि पूल-में-सदस्य के लिए `/array/connect` को POST करना idempotent नहीं है; `attach_array`, `/api/camera/array/list` को पढ़ता है और array_id या serials द्वारा मिलान करता है।

```python
import chloros_sdk

# By serials (matches if every serial is a member of one existing array)
arr = chloros_sdk.attach_array(
    ["213800234", "214000533", "214701288", "214701292"])

# By array_id (when you've already noted it down)
arr = chloros_sdk.attach_array("array-1779862544497")

# attach_array returns the same ArraySession as connect_array
arr.capture("output/", processing="reflectance")
```

पैटर्न: SDK डेस्कटॉप GUI के साथ सह-किरायेदार स्क्रिप्ट्स को पहले `attach_array` को आज़माना चाहिए और यदि पूल में अभी तक कोई एरे नहीं है तो `connect_array` पर वापस आना चाहिए।

```python
import chloros_sdk

try:
    arr = chloros_sdk.attach_array(serials)
except chloros_sdk.ChlorosConnectError:
    arr = chloros_sdk.connect_array(serials)
```

> **महत्वपूर्ण — context-manager exit डिस्कनेक्ट कर देता है।**`ArraySession.disconnect()` हमेशा `/array/disconnect` को POST करता है; कोई संलग्न नहीं है-not-owned गार्ड जैसा कि `CameraSession` / `DAQSensorSession` के लिए है। यदि आप co-GUI के साथ टेनेंटिंग कर रहे हैं और स्कोप एग्जिट पर एरे को हटाना नहीं चाहते हैं, तो**`with` ब्लॉक का उपयोग न करें** — हैंडल को एक सामान्य वेरिएबल में रखें और एक्सप्रेस `disconnect()` को छोड़ दें:
>
> ```python
> arr = chloros_sdk.attach_array(serials)
> arr.capture("output/", processing="reflectance")
> # … script ends; array stays up for the GUI
> ```

### नेटवर्क-विश्लेषण सहायक

एरे खोलने से पहले उपयोगी — यह अनुमान लगाता है कि आपकी प्रस्तावित सेटिंग्स उपयुक्त होंगी या नहीं:

```python
result = chloros_sdk.analyze_array_network(
    master_serial="214701288",
    slave_serials=["213800234", "214000533", "214701162"],
    width=2048, height=1536,
    pixel_format="BayerRG12",
    binning=1,
)

if result["status"] == "ok":
    print("Use the requested settings.")
elif result["status"] == "auto_capped_fps":
    r = result["recommended"]
    print(f"Keep the resolution; cap the trigger rate at {r['recommended_target_fps']} fps")
elif result["status"] == "auto_shrunk":
    r = result["recommended"]
    print(f"Shrink to {r['out_width']}x{r['out_height']} binning={r['binning']}")
elif result["status"] == "needs_force_slip":
    print("Sim-sync impossible on this wire; force_tier='slip-emit-and-capture' required")
```

`status`, `ok` / `auto_capped_fps` / `auto_shrunk` में से एक हैX000489 / `auto_shrunk` / `needs_force_slip` (अन्यथा `error`)। `auto_capped_fps` का अर्थ है कि अनुरोधित रिज़ॉल्यूशन केवल एक सीमित ट्रिगर दर पर RX रिंग में फिट बैठता है — रिज़ॉल्यूशन बनाए रखें और `target_fps=result["recommended"]["recommended_target_fps"]` पास करें से `connect_array` (देखें [उदाहरण 6](#6-capability-probe-before-connecting-a-4-cam-array)).

**प्रोजेक्शन को कैसे पढ़ें** (GUI एरे सेटिंग्स पैनल के समान मॉडल):

- **बर्स्ट (`frame_bytes_total`) प्रत्येक कैम के वास्तविक पिक्सेल फ़ॉर्मेट पर प्रति-कैमरा समूहित किया जाता है।**मोनो**M3M**कैम, चाहे कुछ भी हो, Mono12 (2 B/px) स्ट्रीम करते हैं आप जो `pixel_format` पास करते हैं, उसके आधार पर, इसलिए तीन मोनो कैम के साथ 4-कैम फुल-रेस फ्रेम**~25 MB** का होता है, न कि ~12.6 MB जो एक ऑल-8-बिट मानने पर आता है। बैकएंड अपने मॉडल से प्रत्येक कैम के फॉर्मेट को रिज़ॉल्व करता है।
- **एडमिटेंस (`burst_fits_nic_ring`) ड्रेन-अवेयर है**, न कि पूरे-बर्स्ट-बनाम-रिंग: sim-emit तब फिट बैठता है जब होस्ट RX रिंग को कैम्स द्वारा इसे भरने से तेज़ी से ड्रेन करता है। एक 10G होस्ट + 1 GbE कैम्स**पूरे-रिज़ॉल्यूशन को स्वीकार करता है**, भले ही बस्ट रिंग से अधिक हो; एक 1 GbE होस्ट ब्लॉक कर देता है (`needs_force_slip` / `auto_shrunk`)।
- **`achievable_fps_max` एक रूढ़िवादी सीरियल-रीट्रीव सीलिंग** — `max(readout+emit, N×emit)`, जिसमें प्रति-कैम उत्सर्जन (emit) को 1 GbE कैमरा लिंक पर क्लैम्प किया गया है, जो एक्सपोजर-स्वतंत्र है। उदाहरण के लिए, 4-कैम फुल के लिए ~2.8 fps-रेज़ 12-बिट एरे (रनटाइम के मापे गए ~2.7–3.0 से मेल खाता है)। पूर्ण मॉडल: [CLI संदर्भ → एरे fps और बर्स्ट मॉडल](cli-reference.md#array-fps--burst-model)।
- **ओवर-सब्सक्रिप्शन (`oversubscribed: true`) का अर्थ है N × प्रति-कैम फ्लोर कोलिसन-सेफ सीलिंग से अधिक हो जाता है** — fps फ़ील्ड्स (`achievable_fps_max` / `fps_bright` / `fps_dark`) 0 पढ़ता है, और ऑटो-श्रिंक/बिनिंग इसे ठीक नहीं कर सकता (वे प्रति सेकंड पेस्ड बाइट्स को नहीं, बल्कि प्रति फ्रेम बाइट्स को कम करते हैं)। उपाय हैं कम कैमरे, जंबो फ्रेम, या एक तेज़ NIC; `max_cams_collision_safe` सीमा रिपोर्ट करता है (1 GbE पर 1500 MTU पर 6 पूर्ण-रिज़ॉल्यूशन कैमरे, जंबो के साथ 9)। प्रतिक्रिया में `aggregate_demand_bps`, `collision_safe_ceiling_bps`, और `per_cam_floor_bps` (8 MB/s) भी शामिल होते हैं। [ओवर-सब्सक्रिप्शन] देखें(#over-subscription-the-per-cam-floor).

### डिस्कवरी और लिस्टिंग

```python
chloros_sdk.discover_lattice_cameras()   # list all cams visible to the backend
chloros_sdk.list_cameras()               # cams currently in the pool
chloros_sdk.list_arrays()                # active arrays in the pool
```

---

## स्मार्ट-AE / स्मार्ट-कैप्चर

LATTICE ऐरे कनेक्ट होते ही बैकग्राउंड में निरंतर AE चलाते हैं, लेकिन एक ताज़ा-दिखाए गए दृश्य को अभिसरित होने में थोड़ा समय लगता है। **स्मार्ट-कैप्चर** एक पैकेज्ड सुविधा है: यह प्रत्येक कैम के एक्सपोज़र को पोल करता है, तब तक इंतज़ार करता है जब तक कि विंडो में ऐरे स्थिर न हो जाए, फिर कैप्चर ट्रिगर करता है। यह GUI-के-बराबर है: डेस्कटॉप ऐप का &quot;स्मार्ट&quot; कैप्चर बटन उसी बैकएंड एंडपॉइंट को कॉल करता है।

```python
import chloros_sdk

with chloros_sdk.connect_array([
        "213800234", "214000533", "214701288", "214701292"]) as arr:
    # Initial pose
    arr.capture("pose_a/", processing="reflectance", smart=True)
    input("Move the rig, then press Enter...")
    # New pose — smart-capture waits for AE to re-settle automatically
    arr.capture("pose_b/", processing="reflectance", smart=True)
```

जब `ChlorosProject` (अगला अनुभाग) के माध्यम से ड्राइव करते हैं तो आपको और अधिक नियंत्रण मिलते हैं:

```python
proj.arrays["main_rig"].capture_smart(
    output_dir="out/",
    processing="reflectance",
    settle_timeout_s=5.0,           # max wait
    stability_window_s=1.5,         # exposure must hold steady this long
    exposure_tolerance_pct=5.0,     # %-spread allowed within the window
)
```

स्मार्ट-AE नीति डिफ़ॉल्ट रूप से रूढ़िवादी होती है। सटीक रेडियोमेट्रिक कार्य के लिए `exposure_tolerance_pct` को कड़ा करें; तेज़ी से बदलने वाले दृश्यों के लिए इसे ढीला करें जहाँ आपको बस &quot;काफ़ी करीब&quot; चाहिए।

---

## DAQ सेंसर सत्र

स्पेक्ट्रल सेंसर के लिए स्थायी बैकएंड पूल (USB पर DAQ-U, BLE पर DAQ-M, ईथरनेट पर DAQ-E)। कैमरा सतह को दर्शाता है: स्मार्ट-डिटेक्ट, पूल का पुन: उपयोग, इडेम्पोटेंट अटैच।

### स्मार्ट-डिटेक्ट (शून्य-कॉन्फिग)

```python
import chloros_sdk

with chloros_sdk.connect_daq_sensor() as daq:
    print(daq.model, daq.transport, daq.address)
    for frame in daq.latest(n=10):
        spectrum = frame["spectrum"]   # list[float] (W/m²/nm if calibrated)
        is_sat = frame["is_saturated"]
        x, y, z = frame["x"], frame["y"], frame["z"]
        print(len(spectrum), is_sat)
```

प्राथमिकता: ईथरनेट → BLE → USB। परिवहन को पिन करने के लिए किसी एक स्पष्ट संकेत को पास करें।

### पिन किया गया परिवहन

```python
# DAQ-U on a specific serial port
daq = chloros_sdk.connect_daq_sensor(transport="usb", port="COM3")

# DAQ-M over BLE by MAC (implies transport="ble")
daq = chloros_sdk.connect_daq_sensor(mac="AA:BB:CC:DD:EE:FF")

# DAQ-E over Ethernet by hostname (implies transport="eth")
daq = chloros_sdk.connect_daq_sensor(eth_host="daq-e-xxx.local")

# Tuning knobs
daq = chloros_sdk.connect_daq_sensor(
    port="COM3",
    integration_time=64,      # ms
    frame_avg=20,
    enable_ae=True,
    start_streaming=True,
)
```

### `DAQSensorSession` मेथड्स

| मेथड | विवरण |
| --- | --- |
| `status(timeout=10.0)` | पूल एंट्री सारांश (स्ट्रीमिंग/रिकॉर्डिंग स्थिति, तरंगदैर्ध्य रेंज, कैलिब्रेशन sha, इंटीग्रेशन समय, frame_avg, AE स्थिति)। |
| `latest(n=1, timeout=10.0)` | N तक सबसे हालिया स्पेक्ट्रम फ्रेम लौटाएँ। |
| `stream_start()` / `stream_stop()` | स्ट्रीमिंग फिर से शुरू / विराम करें (हैंडल खुला रहता है)। |
| `record_start(output_dir=None, device_name=None)` | एक .daq फ़ाइल रिकॉर्ड करना शुरू करें। फ़ाइलपथ लौटाता है। AWS कैलिब्रेशन बंडल के बिना DAQ-U/M के लिए अस्वीकार करता है (DAQ-E को छूट है)। |
| `record_stop()` | रिकॉर्डिंग रोकें। `{path, rows}` लौटाता है। |
| `disconnect()` | पूल से रिलीज़ करें। अटैच किए गए-लेकिन-स्वामित्व वाले नहीं हैंडल के लिए नो-ऑप। |

> **कैप-करेक्शन प्रोफाइल (`cap_id`) एक SDK नॉब नहीं हैं।** `connect_daq_sensor()` / `DAQSensorSession` कोई `cap_id` पैरामीटर या `set_cap` विधि प्रदर्शित नहीं करते हैं। CLI (`chloros-cli daq pool-connect --cap-id …` / X के माध्यम से एक फ्लीट कैप-करेक्शन प्रोफ़ाइल चुनें।) या बैकएंड के `/api/daq` HTTP राउट्स (`/api/daq/connect` और `/api/daq/<id>/cap-id` स्वीकार करते हैं `cap_id`).

### डिस्कवरी — कनेक्ट करने के लिए एक पता खोजना

`discover_daq_sensors()` उन सेंसरों के लिए USB / BLE / ETH को स्कैन करता है जिन्हें आप *खोल सकते हैं*। यह `discover_lattice_cameras()` का DAQ समकक्ष है, और **प्राप्त करने का एकमात्र तरीका हैDAQ-M का BLE MAC** — एक DAQ-E का एक होस्टनेम और एक DAQ-U का एक COM पोर्ट होता है, लेकिन एक MAC न तो डिवाइस पर मुद्रित होता है और न ही OS द्वारा सूचीबद्ध होता है।

```python
for s in chloros_sdk.discover_daq_sensors():
    print(s["transport"], s["address"], s["model"], s["extra"])
# ble  C3:D8:85:E0:0A:19  DAQ-M  {'name': 'NSP32_SPECTRUM'}
# usb  COM3               None   {'manufacturer': 'Intel'}

# `address` is exactly what connect_daq_sensor wants:
for s in chloros_sdk.discover_daq_sensors(transports=["ble"]):
    if s["model"] == "DAQ-M":
        daq = chloros_sdk.connect_daq_sensor(mac=s["address"])
```

| क्षेत्र | विवरण |
| --- | --- |
| `transport` | `usb` \| `ble` \| `eth`. |
| `address` | COM पोर्ट / BLE मैक / होस्टनेम — `connect_daq_sensor` को `port=` / `mac=` / के रूप में पास करें। `eth_host=`. |
| `display` | मानव-पठनीय लेबल। |
| `model` | `DAQ-U` \| `DAQ-M` \| `DAQ-E`, या `None` किसी ऐसे पोर्ट के लिए जिसका स्कैन पहचान नहीं कर सकता (प्रोब के बिना यूएसबी सीरियल एडाप्टर एक-दूसरे से अलग नहीं होते हैं, इसलिए अज्ञात मानों को छिपाने के बजाय दिखाया जाता है)। |
| `extra` | प्रति-परिवहन विवरण (BLE विज्ञापित नाम, USB निर्माता, DAQ-E ip/fw/…)। खाली मानों को छोड़ दिया जाता है। |

| पैरामीटर | डिफ़ॉल्ट | विवरण |
| --- | --- | --- |
| `transports` | तीनों | स्कैन को सीमित करने वाला अनुक्रम (या csv स्ट्रिंग)। जब आपको पता हो कि आप क्या चाहते हैं तो इसे पास करना उचित है — BLE धीमा हिस्सा है। |
| `scan_timeout` | 5 | प्रति-ट्रांसपोर्ट स्कैन विंडो सेकंड में; बैकएंड इसे 1–20 तक सीमित करता है। |
| `timeout` | 60.0 | पूरे कॉल के लिए HTTP की ऊपरी सीमा (जैसा कि SDK में अन्यत्र है)। |
| `auto_start_backend` | `True` | यदि कोई स्थानीय बैकएंड चल नहीं रहा है तो उसे शुरू करें। रिमोट `backend_url` के लिए कभी भी शुरू नहीं किया जाता। |

> **पूल में पहले से खुले सेंसर दिखाई नहीं देते।** एक कनेक्टेड BLE पेरिफेरल विज्ञापन बंद कर देता है और एक खुला COM पोर्ट प्रोब नहीं किया जा सकता, इसलिए डिस्कवरी में वही सूचीबद्ध होता है जो *कनेक्ट करने के लिए उपलब्ध* है. किसी चीज़ को कनेक्ट करने के ठीक बाद एक खाली परिणाम की उम्मीद है — जो आपके पास पहले से है उसके लिए `list_daq_sensors()` का उपयोग करें। जिन ट्रांसपोर्ट्स का स्कैन नहीं चल सकता (कोई bleak / zeroconf इंस्टॉल नहीं है) वे त्रुटि देने के बजाय छोड़ दिए जाते हैं, इसलिए ब्लूटूथ के बिना एक मशीन को अभी भी अपने USB और ETH के जवाब मिलते हैं।

### सूचीकरण

```python
for s in chloros_sdk.list_daq_sensors():
    print(s["sensor_id"], s["model"], s["transport"], s["wavelength_range"])
```

### GUI / CLI के साथ सह-कब्ज़ा

यदि GUI में पहले से ही कोई सेंसर खुला है, तो Python से `connect_daq_sensor(port="COM3")` को कॉल करने पर `already_connected=True` चिह्नित एक हैंडल लौटाता है। सत्र का `disconnect()` तब एक नो-ऑप होता है ताकि आपका SDK स्क्रिप्ट न करेंस्कॉप से बाहर निकलने पर GUI के नीचे से सेंसर को हटाने से बचाता है।

### डायरेक्ट-हार्डवेयर क्लासेस (नो बैकएंड)

`daq_sdk` को `chloros_sdk` द्वारा पुनः निर्यात किया जाता है ताकि आप बैकएंड के बिना इन-प्रोसेस में सेंसर को एंड-टू-एंड भी चला सकें:

> **उपलब्धता:**`daq_sdk` Chloros डेस्कटॉप इंस्टॉल के साथ आता है,**PyPI पैकेज के साथ नहीं — `pip install chloros-sdk` आपको `lattice_sdk` देता है लेकिन `chloros_sdk.DAQ_AVAILABLE == False` को छोड़ देता है। इन क्लासेस का उपयोग करने से पहले उस फ़्लैग की जाँच करें; एक pip-only होस्ट पर सेंसर को के माध्यम से चलाएँ इसके बजाय [`connect_daq_sensor()`](#daq-sensor-sessions) का उपयोग करें, जिसके लिए किसी स्थानीय ट्रांसपोर्ट लाइब्रेरी की आवश्यकता नहीं होती है।

```python
from chloros_sdk import DAQUSensor, DAQMSensor, DAQESensor, discover_all

# Discovery
for d in discover_all(timeout=3.0):
    print(d.model, d.display, d.address)   # USB serials: d.extra.get("serial_number")

# Direct DAQ-U
sensor = DAQUSensor(port="COM3")
sensor.connect()
sensor.start_streaming()
# ... use sensor.add_spectrum_callback(...) ...
sensor.stop()
```

जब आप GUI के साथ साझा स्वामित्व चाहते हैं तो स्मार्ट-कनेक्ट पथ (`connect_daq_sensor`) को प्राथमिकता दें; हेडलेस स्क्रिप्ट्स के लिए डायरेक्ट क्लासेस का उपयोग करें जो सेंसर का विशेष रूप से स्वामित्व रखते हैं।

---

## प्रोजेक्ट ऑटोमेशन — `ChlorosProject`

एक सहेजा गया Chloros प्रोजेक्ट एक फ़ोल्डर होता है जिसमें `cameras.json` + `sensors.json` + `project.json` होता है। `open_project` मैनिफेस्ट को लोड करता है, और `connect_all` हर सहेजे गए डिवाइस को उसकी सहेजी गई सेटिंग्स के साथ ऑनलाइन लाता है — वही हार्डवेयर स्थिति जो GUI उत्पन्न करता।

### न्यूनतम उदाहरण

```python
import chloros_sdk

proj = chloros_sdk.open_project("/home/user/Chloros Projects/Field_A")
report = proj.connect_all(verbose=True)
print(report)  # {'cameras': {...}, 'arrays': {...}, 'sensors': {...}}

# Cameras and arrays are addressable by name OR serial / array_id
cam = proj.cameras["FrontLeft"]
cam.capture("./out", format="tiff", processing="reflectance")

arr = proj.arrays["main_rig"]
arr.capture("./out", format="tiff", processing="reflectance")

# Read a DAQ
spectrum = proj.sensors["Sky"].read()

# Trigger every device simultaneously
proj.capture_all("./out")

proj.disconnect_all()
```

या एक संदर्भ प्रबंधक के रूप में:

```python
with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    proj.arrays["main_rig"].capture("./out", processing="reflectance")
```

### `ChlorosProject` मेथड्स

| मेथड | विवरण |
| --- | --- |
| `connect_all(cameras=True, arrays=True, sensors=True, verbose=False, align=None)` | हर सहेजे गए डिवाइस को खोजें + कनेक्ट करें। प्रति-क्लास कनेक्ट रिपोर्ट लौटाता है। जब `127.0.0.1:5000` पर कोई बैकएंड चल रहा हो तो इसका उपयोग करता है; अन्यथा चुपचाप सीधे (बैकएंड-मुक्त) पर वापस चला जाता है `lattice_sdk` डिवाइस नियंत्रण — यह कभी भी बैकएंड नहीं बनाता है। |
| `disconnect_all()` | सब कुछ बंद कर देता है। |
| `capture_all(output_dir=".")` | हर कैम से एक फ्रेम + हर सेंसर से स्पेक्ट्रम। |
| `stream(camera, overlays=False, fps=10.0)` | नामित कैम से BGR `numpy` फ्रेम देने वाला जनरेटर (या ऐरे)। `overlays=False` एक प्रत्यक्ष `lattice_sdk` ग्रैब लूप है (एरे `{serial: frame}` डिक्शनरीज़ देते हैं)। `overlays=True` `ChlorosLocal.camera_stream()` → बैकएंड के माध्यम से रूट करता है `/api/camera/<serial>/stream-annotated` MJPEG फ़ीड, जिसमें कैमरे के सहेजे गए `ui.overlay` ब्लॉक को क्वेरी पैरामीटर के रूप में पास किया जाता है। इसके लिए बैकएंड मोड और एक **स्टैंडअलोन कैमरा** आवश्यक है।: एक डायरेक्ट-मोड कैम `RuntimeError` को बढ़ाता है (बैकएंड इस प्रक्रिया के स्वामित्व वाले कैम को नहीं पकड़ सकता) और एक ऐरे `NotImplementedError` को बढ़ाता है (प्रति कैमरा कंपोजिट ओवरले — नाम के अनुसार एक सदस्य को स्ट्रीम करता है)। एक-शॉट के बराबर: `CameraHandle.capture(annotated=True)`। |
| `align_arrays(align=True, verbose=False)` | वर्तमान में-कनेक्टेड हर एरे पर संरेखण चलाएँ। |
| `process(mode="parallel", wait=True, progress_callback=None, poll_interval=2.0)` | प्रोजेक्ट पर कैलिब्रेशन / इंडेक्स पाइपलाइन चलाएँकी छवियों पर। (`ChlorosLocal.process` को लपेटता है; ये चार **एकमात्र** स्वीकृत kwargs हैं — `indices=` आदि `TypeError` उठाते हैं; `ChlorosLocal.configure()` के माध्यम से सूचकांक सेट करें). आलसी ढंग से एक `ChlorosLocal()` बनाता है, जो स्वचालित रूप से एक बैकएंड शुरू करता है। |

गुणधर्म:
- `proj.cameras` — `Dict[str, CameraHandle]` नाम और सीरियल द्वारा कुंजीबद्ध।
- `proj.arrays` — `Dict[str, ArrayHandle]` नाम और ऐरे_id द्वारा कुंजीबद्ध।
- `proj.sensors` — `Dict[str, SensorHandle]` नाम और स्लॉट_id द्वारा कुंजीबद्ध।
- `proj.config` — `project.json["config"]` dict.

### `CameraHandle`

```python
cam = proj.cameras["FrontLeft"]

# Save a frame to disk (processing-aware)
filepath = cam.capture(
    output_dir="./out",
    format="tiff",
    processing="radiance",           # see the level table below
    apply_calibration=True,          # DSNU + flat + 3x3 unmix + NIST
    apply_white_balance=True,        # DLS-aware WB
    apply_index=False,
    index_expression=None,
)

# In-memory grab (numpy array)
frame = cam.grab(processing="debayered")
frame, header = cam.grab(processing="radiance", with_metadata=True)

# Frame iterator (generator)
for arr in cam.frame_stream(processing="debayered", fps=5, count=100):
    my_analysis(arr)
```

**प्रसंस्करण स्तर।** `capture()`, `grab()`, और `frame_stream()` सभी एक ही `processing` टोकन लेते हैं, और श्रृंखला संचयी है — प्रत्येक स्तर अपने ऊपर के सभी स्तरों को चलाता है:

| स्तर | आउटपुट | नोट्स |
| --- | --- | --- |
| `raw` | 1-चैनल बायर, सेंसर-नेटिव | डेमोसाइक नहीं। इस स्तर पर ओवरले उपलब्ध नहीं हैं। |
| `debayered` | 3-चैनल BGR (**डिफ़ॉल्ट**) | बाइलिनियर डेमोसाइक। यह एकमात्र स्तर है जो बैकएंड मोड के बिना काम करता है। |
| `radiance` | float32, W/m²/sr/nm | पूर्ण रेडियोमेट्रिक श्रृंखला: डेमोसाइक + 3×3 अनमिक्स (मल्टीस्पेक्ट्रल) + DSNU + फ्लैट-फील्ड + NIST स्केल, जिसमें एक्सपोजर × गेन को विभाजित किया गया है ताकि मान निरपेक्ष हों। |
| `reflectance` | uint16, 32768 = 1.0 | रेडियंस को डाउनवेलिंग इरैडियंस से विभाजित किया गया (ρ = π·L/E)। इसके लिए DLS/DAQ रीडिंग की आवश्यकता है — नीचे दिया गया नोट देखें। |
| `display` | 8-बिट sRGB-जैसा | GUI-समतुल्य रेंडर: कैमरे की सक्रिय कलर प्रोफ़ाइल के माध्यम से CCM + व्हाइट बैलेंस + गामा। |

`debayered` के अलावा किसी भी चीज़ के लिए बैकएंड मोड की आवश्यकता होती है; एक डायरेक्ट-मोड कैमरा बढ़ाता है
`NotImplementedError`। `reflectance` को एक उपयोग योग्य डाउनवेलिंग रीडिंग की आवश्यकता है — फ्रेम एंडपॉइंट पुल करता है
DAQ को स्वचालित रूप से कैमरा के DLS स्लॉट में पूल करता है, लेकिन जब कोई DAQ बंधा नहीं होता है तो चेन प्रतिबिंबन निकास को अस्वीकार कर देती है और चुपचाप करने के बजाय लौटाए गए मेटाडेटा में इस अवनमन को ईमानदारी से अंकित करती है।
एक कमतर उत्पाद वापस करते हुए।

> **रिफ्लेक्टेंस DN स्केल — इसे हार्डकोड न करें।** LATTICE रिफ्लेक्टेंस `32768` = ρ 1 का उपयोग करता है।0 और स्टैम्प करता है
> XMP `Chloros:PixelScale=32768`; Survey3 रिफ्लेक्टेंस `65535` = ρ 1.0 का उपयोग करता है और कोई
> `Chloros:*` टैग नहीं रखता है। टैग को पढ़ें और उसे उससे विभाजित करें। यह uint16 डोमेन में परिभाषित है, इसलिए यह हर उस फॉर्मेट के लिए `32768` रहता है जो पुनः पैमाने पर लाता है (16-बिट TIFF, 8-बिट PNG/JPG, 32-बिट प्रतिशत) — सामान्यीकृत करें
> संग्रहीत dtype को पहले वापस uint16 में बदलें (8-बिट से ×257, फ्लोट से ×65535)। एक अपवाद:
> 8-बिट-सोर्स कैप्चर जिसे 8-बिट TIFF के रूप में लिखा गया है, वह *क्लिप* होता है, पुनः स्केल नहीं किया जाता है, इसलिए कोई स्केल वर्णन नहीं करता
> यह — Chloros उस स्थिति में `PixelScale` और MicaSense टपल को पूरी तरह से छोड़ देता है। LATTICE रिफ्लेक्टेंस फ़ाइल पर एक अनुपस्थित टैग को &quot;कोई मान्य स्केल नहीं&quot; के रूप में मानता है, डिफ़ॉल्ट के रूप में नहीं।

> **EXIF एक्सपोर्ट तक ले जाया जाता है।** `process()` स्रोत कैप्चर के GPS ब्लॉक
> **और उसके ExifIFD** को हर उत्पाद पर कॉपी करता है, इसलिए एक्सपोर्ट में `FocalLength` होता है, `FNumber`, `ExposureTime`, `ISO`, `DateTimeOriginal` और `CameraSerialNumber` के साथ-साथ
> भू-संदर्भण भी। `FocalLength` वह है जिससे Pix4D ग्राउंड सैंपल दूरी की समस्या को हल करता है — इसके बिना
> पुनर्निर्माण एक बहुत ही गलत पैमाने पर आ जाता है (एक मापे गए मामले में 411 मीटर के स्थल को
> 47.8 किमी में बदल दिया गया). कॉपी जानबूझकर `-all:all` नहीं है: IFD0 के स्ट्रक्चरल टैग्स लैटिस आउटपुट को तोड़ देते हैं, और `ExifImageWidth`/`Height` को बाहर रखा गया है क्योंकि वे निर्यात किए गए रास्टर के बजाय स्रोत कैप्चर का वर्णन करते हैं।

कैप्चर-स्टेज सब-फ़्लैग (रेडियोमेट्रिक स्तरों पर लागू होते हैं — `radiance`, `reflectance`, `display`):

| फ़्लैग | डिफ़ॉल्ट | अर्थ |
| --- | --- | --- |
| `apply_calibration` | `True` | DSNU + फ्लैट-फील्ड + 3x3 अनमिक्स + NIST रेडियोमेट्रिक स्केल। |
| `apply_white_balance` | `True` | WB LUT. DLS-जब कोई DAQ कैम से बंधा होता है तो सचेत। |
| `apply_index` | `False` | वनस्पति सूचकांक मूल्यांकन। |
| `index_expression` | `None` | ओवरराइड फ़ॉर्मूला। गैर-खाली → स्वचालित रूप से इंडेक्स सक्षम करता है। |
| `annotated` | `False` | ओवरले GUI सजावट (ज़ेबरा/ग्रिड/पीकिंग)। `raw` के लिए उपलब्ध नहीं है। |

### `ArrayHandle`

```python
arr = proj.arrays["main_rig"]

# Single synced capture group
files = arr.capture("./out", format="tiff", processing="reflectance")
# → {"213800234": "/path/to/x.tif", "214000533": "/path/to/y.tif", ...}

# Multi-level: each serial's value becomes an ordered LIST, not a str
files = arr.capture("./out", processing="all")
# → {"213800234": ["/raw.tif", "/debayered.tif", ...], "combined": "/idx.tif"}

# Smart capture (wait for AE to settle)
result = arr.capture_smart(
    "./out", processing="reflectance",
    settle_timeout_s=5.0,
    stability_window_s=1.5,
    exposure_tolerance_pct=5.0,
)
print(result["frames"], result["settle"])

# In-memory grab: {serial: numpy array}
frames = arr.grab(processing="debayered")
frames = arr.grab(processing="radiance", with_metadata=True)

# Stream-to-disk loop
arr.stream(count=60, output_dir="./stream", fps=5, processing="raw")

# Frame-iterator (tolerates per-cam drops; great for downstream analysis pipelines)
for frames in arr.frame_stream(processing="radiance", fps=5, count=100):
    if "213800234" in frames:
        my_analysis_pipeline(frames["213800234"])

# Preview iterator (live MJPEG-equivalent; tolerates partial cycles)
counts = arr.preview_stream("./preview", fps=3.0, duration=30.0)
print(counts)  # frames written per serial
```

> **वापसी का प्रकार `CapturePathMap` है, न कि `Dict[str, str]`।**
> `chloros_sdk.CapturePathMap` है `Dict[str, Union[str, List[str]]]`: एक एकल-स्तर का
> `processing` प्रत्येक सीरियल को एक पथ देता है, जबकि एक बहु-स्तर का एक (`"all"`, या एक स्पष्ट `levels` सूची) उसे उस कैमरे के लिए सहेजे गए हर उत्पाद की **क्रमबद्ध सूची** देता है। एक लाइव संयुक्त कंपोजिट, यदि कोई स्ट्रीमिंग कर रहा था, तो वह एक सीरियल के बजाय अतिरिक्त `"combined"` कुंजी के अंतर्गत आता है। कोड यह मानता है कि `str` सूची स्वरूप में टूटता है, बिना किसी टाइप चेकर के आपत्ति जताए — सूची स्वरूप के शिप होने के बाद कुछ समय के लिए एनोटेशन में `Dict[str, str]` लिखा था, और इसीलिए यह उपनाम मौजूद है। जब आप फ़्लैट फ़ॉर्म चाहते हैं तो सामान्यीकृत करें:
>
> ```python
> paths = arr.capture(processing="all")
> flat = [p for v in paths.values()
>         for p in (v if isinstance(v, list) else [v])]
> ```

### एरे संरेखण

`ArrayHandle` पूरी एलाइनमेंट सरफेस को एक्सपोज़ करता है। प्रोफाइल डिफ़ॉल्ट रूप से केवल-सत्र (session-only) होती हैं — पर्सिस्ट करने के लिए एक्सप्रोटएक्स000664 को स्पष्ट रूप से कॉल करें।

```python
from chloros_sdk import AlignmentSpec

arr = proj.arrays["main_rig"]

# Defaults: ORB / affine / one synced snapshot — same as the GUI's auto-cal
result = arr.calibrate_alignment()
print(result["profile"]["rms_residual_px"])

# Custom spec for tough scenes (low-contrast canopy)
spec = AlignmentSpec(
    method="feature_orb",         # feature_orb / feature_akaze / phase_correlation / checkerboard / manual
    model="rigid",                # translation / rigid / affine / homography
    num_frames=5,
    max_features=8000,
    ratio_threshold=0.7,
    ransac_threshold_px=2.0,
    min_matches=30,
    max_reproj_err_px=2.0,
)
arr.calibrate_alignment(spec)

# Or tweak one knob at a time
arr.calibrate_alignment(num_frames=3, model="affine")

# Inspect / manipulate
status = arr.alignment_status()
arr.tweak_alignment("214701292", dx=2.5, dy=-1.0, rotation_deg=0.0, scale=1.0)
arr.export_alignment("/tmp/main_rig_alignment.json")
arr.import_alignment("/tmp/main_rig_alignment.json", validate=True)
arr.clear_alignment()
```

#### कनेक्ट-टाइम एलाइनमेंट

`connect_all(align=...)` कनेक्ट होने पर हर एरे को स्वचालित रूप से संरेखित कर सकता है:

```python
# Align every array with defaults
proj.connect_all(align=True)

# Per-array control
proj.connect_all(align={
    "main_rig": AlignmentSpec(num_frames=5, model="affine"),
    "side_rig": True,             # use defaults
    "verify_rig": False,          # skip
})
```

जब निर्दिष्ट नहीं किया जाता है तो यह `project.json["config"]["auto_align_on_connect"]` पर वापस चला जाता है।

### `SensorHandle`

```python
spectrum = proj.sensors["Sky"].read()
# (spectrum_list, is_saturated, integration_time, x, y, z) — matches the
# daq_sdk add_spectrum_callback signature.
```

---

## डायरेक्ट हार्डवेयर (बैकएंड-मुक्त)

जब आप बैकएंड पर शून्य निर्भरता चाहते हैं (CI, हेडलेस रोबोट, एम्बेडेड), `lattice_sdk` और `daq_sdk` को सीधे इम्पोर्ट करें — दोनों `chloros_sdk` द्वारा पुनः निर्यात किए जाते हैं। `CAMERA_AVAILABLE` / XPR पर गार्डOTX000672: `lattice_sdk` PyPI पैकेज में है (लेकिन इसके लिए Arena SDK रनटाइम की आवश्यकता है), जबकि `daq_sdk` केवल डेस्कटॉप इंस्टॉल के साथ आता है।

```python
from chloros_sdk import (
    # cameras
    LatticeCamera, CameraSettings, PRESETS, CameraPool,
    Calibration, CalibrationCoefficients, FilterModel, list_filters,
    DLS, NetworkDiagnostics, gpu_info, gpu_available,
    # discovery
    discover_cameras, discover_cameras_via_backend,
    # exceptions
    LatticeError, CameraNotFoundError, StreamError, CaptureError,
    CalibrationError, NetworkError, DLSError,
)

# Find a camera and capture in one go
cams = discover_cameras(timeout_ms=3000)
print(cams)

settings = PRESETS["high_quality"]
with LatticeCamera(serial="213800234", settings=settings) as cam:
    result = cam.capture(output_dir="./out", format="tiff")
    print(result.filepath, result.width, result.height)
```

##### प्रीसेट और ट्रिगर

चार में से तीन प्रीसेट **फ़्री-रन** करते हैं: कैमरा लगातार एक्सपोज़ करता है और एक
`capture()` अगला फ़्रेम लौटाता है। `triggered` एक अपवाद है — यह लाइन 2 पर हार्डवेयर एज के लिए कैमरे को तैयार करता है, इसलिए यह तब तक कुछ भी कैप्चर नहीं करता जब तक कि कोई एज न आए।

| प्रीसेट | ट्रिगर | इसका उपयोग कब करें |
| --- | --- | --- |
| `default` | फ्री-रन-रन | सामान्य उपयोग |
| `high_speed` | फ्री-रन | 8-बिट, 60 fps कैप, छोटा एक्सपोज़र |
| `high_quality` | फ्री-रन | 12-बिट, कोई fps कैप नहीं — स्टिल के लिए सामान्य विकल्प |
| `triggered` | **सक्रिय, लाइन 2** | कैमरा एक M8 सिंक केबल से जुड़ा है और कोई और चीज़ इसे फायर करती है |

यदि आप `triggered` चुनते हैं (या स्वयं `trigger_mode="On"` सेट करते हैं) और कुछ भी नहीं
Line 2 को चलाने पर, हर `capture()` टाइम आउट हो जाएगा — सही रूप से, क्योंकि आपने कैमरा से इंतजार करने को कहा था। SDK यह समझाता है जब ऐसा होता है; देखें
[SC_ERR_TIMEOUT during capture](#direct-hardware-backend-free).

> **ध्यान दें — कनेक्ट पर &quot;GVSP probe&quot; / `SC_ERR_TIMEOUT -1011` संदेश त्रुटियाँ नहीं हैं।**&gt; कनेक्ट पर SDK उच्च थ्रूपुट के लिए**जंबो फ्रेम** (9000-बाइट GVSP पैकेट) के लिए बातचीत करने का प्रयास करता है। एक डायरेक्ट पॉइंट पर-से-पॉइंट NIC लिंक (जैसे कि एक लिंक-लोकल `169.254.x.x` पता) पर नेटवर्क आमतौर पर जंबो फ्रेम नहीं ले जा सकता है, इसलिए यह प्रोब टाइम आउट हो जाता है और इस तरह की लाइनें लॉग करता है:
>
> ```
> [Network] GVSP probe: unexpected error (TimeoutError: ... SC_ERR_TIMEOUT -1011)
> [Network] GVSP probe at 9000 did not deliver a complete buffer; reverting to ICMP-chosen size
> [Network] GVSP packet size: 1500 bytes (standard)
> ```
>
> यह **डिज़ाइन किया गया फॉलबैक** है: कैमरा स्वचालित रूप से मानक 1500-बाइट पैकेट पर वापस आ जाता है और कैमरा सामान्य रूप से कनेक्ट करना जारी रखता है (इसके बाद आने वाली `[chunk-enable …]` लाइनें सामान्य कनेक्ट अनुक्रम का हिस्सा हैं)। कैप्चर अभी भी काम करता है।
>
> आप इस प्रोब को छोड़ सकते हैं, लेकिन **यह सिर्फ़ एक लॉग-साइलेंसर नहीं है — यह जंबो फ़्रेम को बंद कर देता है।** कैमरा Don&#x27;t-Fragment पिंग का जवाब केवल 1500 बाइट तक ही देता है, चाहे आपका नेटवर्क कितना भी अच्छा क्यों न हो, इसलिए अकेले पिंग टेस्ट से जंबो कभी नहीं मिल सकता; यह प्रोब ही एकमात्र चीज़ है जो ऐसा कर सकता है। इसे अक्षम करें और कैमरा किसी भी नेटवर्क पर हमेशा के लिए मानक 1500-बाइट पैकेट चलाता है:
>
> ```bash
> CHLOROS_GVSP_PROBE_FALLBACK=0   # gives up jumbo — see the warning it prints
> ```
>
> यह केवल उस नेटवर्क पर ही उपयोगी है जिसके बारे में आप *जानते हैं* कि वह जंबो को नहीं ले जा सकता, जहाँ यह प्रति कैमरा कनेक्ट समय में लगभग एक सेकंड बचाता है। चूँकि यह एक दिखावटी बदलाव के बजाय एक वास्तविक बदलाव है, इसलिए अब जब आप इसका उपयोग करते हैं तो SDK भी ऐसा ही बताता है:
>
> ```
> [Network] ⚠️ GVSP probe disabled (CHLOROS_GVSP_PROBE_FALLBACK=0) — staying at
> 1500 bytes, jumbo NOT tested. … if this network does carry it, you are giving
> up ~1.45x wire ceiling. Unset the variable to test for jumbo.
> ```
>
> **जब तक आपके पास कोई कारण न हो, इसे वैसे ही रहने दें।** इसे चालू छोड़ने पर, हर कनेक्ट आपके वास्तविक नेटवर्क को फिर से मापता है: यदि आप किसी जंबो-सक्षम स्विच से कनेक्ट हैं तो अगला कनेक्ट अपने आप जंबो को उठा लेता है, जिसके लिए कुछ भी कॉन्फ़िगर करने या रीस्टार्ट करने की ज़रूरत नहीं होती।
>
> यदि आप जंबो थ्रूपुट *चाहते* हैं, तो एंड-टू-एंड जंबो को सक्षम करें (NIC MTU 9000 + एक स्विच जो उन्हें पास करता है), या जब आपको पता हो कि लिंक इसे सपोर्ट करता है तो इसे `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000` से पिन करें — हालांकि इसे स्थायी रूप से सेट करने के बजाय प्रति-कमांड `CHLOROS_GVSP_PACKET_SIZE_FORCE=9000 python …` को प्राथमिकता दें, क्योंकि एक पिन किया गया आकार स्किप कर देता है प्रोब को छोड़ देता है और अपने सामने के नेटवर्क के अनुकूल होना बंद कर देता है। पथ में **हर** डिवाइस को जंबो पास करना होगा — जिसमें कोई भी PoE स्प्लिटर या इंजेक्टर भी शामिल है, जो आमतौर पर एक अन्यथा जंबो-सक्षम सेटअप द्वारा उन्हें ले जाने में असमर्थ होने का सामान्य कारण है।

> **`capture()` / `grab*()` के दौरान `SC_ERR_TIMEOUT -1011` एक अलग समस्या है — वह एक वास्तविक त्रुटि है।**&gt; उपरोक्त नोट केवल  के बारे में है0692**कनेक्ट-टाइम प्रोब**द्वारा लॉग किया गया। एक**कैप्चर** से उत्पन्न होने वाली समान त्रुटि का अर्थ है कि कैमरा ठीक से जुड़ गया है लेकिन कोई छवियां नहीं भेज रहा है:
>
> ```
> File ".../lattice_sdk/camera.py", line ..., in grab_frame_with_metadata
>   buffer = self._get_buffer(timeout)
> lattice_sdk.exceptions.CaptureError: Capture failed: ... SC_ERR_TIMEOUT -1011
> ```
>
> इसका संकेत एक ऐसा कैमरा है जिसका *कंट्रोल* चैनल स्वस्थ है — डिस्कवरी काम करती है, सेटिंग्स और `[chunk-enable …]` राइट्स सभी सफल होते हैं — जबकि *हर* फ्रेम टाइम आउट हो जाता है।
>
> **आम कारण यह है कि कैमरा हार्डवेयर ट्रिगर के लिए आर्म्ड है।** `trigger_mode="On"` और `trigger_source="Line2"` के साथ, कैमरा तब तक कुछ भी नहीं भेजता जब तक कि M8 सिंक केबल पर एक इलेक्ट्रिकल एज नहीं आता। यदि आपके पास उस लाइन को चलाने वाला कोई केबल नहीं है, तो हर ग्रैब हमेशा के लिए इंतजार करता है। कैमरा खराब नहीं है और नेटवर्क भी ठीक है — यह बिल्कुल वही कर रहा है जो उसे करने के लिए कहा गया है।
>
> `CameraSettings()` और `default` / `high_speed` / `high_quality` प्रीसेट फ्री-रन, और एक ग्रैब जो आर्म्ड रहते हुए टाइम आउट हो जाता है, वह खाली `-1011` प्रिंट करने के बजाय अपना कारण बताता है। `PRESETS["triggered"]` डिज़ाइन के अनुसार लाइन2 को आर्म्ड करता है।
>
> किसी भी कैमरे को फ्री करने के लिए मजबूर करने हेतु-run:
>
> ```python
> settings = PRESETS["high_quality"]
> settings.trigger_mode = "Off"        # free-run; don't wait for an M8 edge
> ```
>
> यदि यह अभी भी `trigger_mode="Off"` के साथ टाइम आउट हो जाता है, तो कैमरा वास्तव में डेटा प्रदान नहीं कर रहा है — हमें लॉग और `ip link show` भेजें।

#### कलर प्रोफाइल (RGB लाइव पूर्वावलोकन) — `set_color_profile`

`LatticeCamera.set_color_profile(profile, custom_cct_k=None)` RGB कैम्स पर **लाइव पूर्वावलोकन** के लिए डिस्प्ले कलर प्रोफाइल चुनता है (मल्टीस्पेक्ट्रल कैम्स इस सेटिंग को अनदेखा करते हैं):

| प्रोफ़ाइल | अर्थ |
| --- | --- |
| `raw` | रेडियोमेट्रिक चेन को पूरी तरह से बायपास करें। |
| `linear` | DSNU + फ़्लैट + WB, कोई CCM नहीं, कोई गामा नहीं। |
| `natural` | रैखिक + मापा गया CCM + sRGB गामा, केवल सस्ते फिनिश के साथ (क्रोमा स्मूदिंग + हाइलाइट डीसैचुरेशन) — यथार्थवादी डिफ़ॉल्ट। |
| `enhanced` | `natural` के साथ पूरा हब-पैरिटी फिनिश (डेफ्रिंज, वाइब्रेंस, CLAHE लोकल कॉन्ट्रास्ट)। लगभग **प्रति-फ्रेम फिनिश लागत दोगुनी** पर अधिक समृद्ध लुक, इसलिए लाइव फ्रेमरेट कम हो जाता है। |
| `custom_temp` | `natural` लेकिन WB को `custom_cct_k` केल्विन पर पिन किया गया (DLS को अनदेखा किया गया; 2000 पर क्लैम्प किया गया–10000 K बैकएंड-साइड)। |

यह प्रोफ़ाइल एक **लाइव-प्रीव्यू-केवल** स्पीड/लुक नॉब है: सहेजी गई कैप्चर हमेशा चयनित प्रोफ़ाइल की परवाह किए बिना पूर्ण रिच फिनिश प्राप्त करती हैं, इसलिए फ्रेम समय वापस पाने के लिए `natural` चुनने से डिस्क पर आने वाली गुणवत्ता कम नहीं होती है। एक अज्ञात प्रोफ़ाइल `ValueError` को बढ़ाता है; जब क्लोरोस बैकएंड पहुँच योग्य होता है तो यह परिवर्तन उसे भी POST किया जाता है ताकि अगला पूर्वावलोकन फ्रेम इसे दर्शाता है (डायरेक्ट-SDK बैकएंड के बिना उपयोगकर्ता भी सेटिंग्स म्यूटेशन प्राप्त करते हैं)।

```python
with LatticeCamera(serial="214701292") as cam:   # RGB cam
    cam.set_color_profile("enhanced")            # richer look, lower LIVE fps
    cam.set_color_profile("custom_temp", custom_cct_k=5600)
```

#### मोनो (M3M) कैमरे और `Calibration`

एक मोनो **M3M** कैमरा (`M3M-<lens>-F<wavelength>`) एकल-बैंड होता है: एक ग्रेस्केल प्लेन, कोई बायर मोज़ेक नहीं, कोई 3×3 स्पेक्ट्रल-क्रॉसटॉक मैट्रिक्स नहीं। `Calibration` इसे पहचानता है और एक `is_mono` फ़्लैग। परावर्तनाक्षमता अभी भी प्रति-बैंड रेडियोमेट्रिक मानचित्र के रूप में लागू होती है (अनमिक्स पहचान मैट्रिक्स है), लेकिन एक ही कैमरे पर मल्टी-बैंड गणित बकवास लौटाने के बजाय त्रुटि उत्पन्न करता है:

```python
from chloros_sdk import Calibration, CalibrationError

calib = Calibration("M3M-L87-F685")
print(calib.is_mono)        # True  (False for any M3C / RGN Bayer cam)
print(calib.filter_type)    # 'mono'  (sentinel; not a real crosstalk key)

# NDVI needs two bands (Red + NIR); one mono band can't supply both.
try:
    calib.compute_ndvi(reflectance_frame)
except CalibrationError as e:
    print(e)   # "...single-band mono (M3M) camera. Combine multiple..."
```

मोनो हार्डवेयर से वनस्पति सूचकांक बनाने के लिए, विभिन्न तरंगदैर्घ्यों पर कई M3M कैम को एक संरेखित मल्टी-बैंड स्टैक में संयोजित करें (देखें [एरे संरेखण](#array-alignment)) और एक कैमरे पर गणना करने के बजाय उस स्टैक पर सूचकांक की गणना करें।

DAQ डायरेक्ट-मोड:

```python
from chloros_sdk import (
    DAQUSensor, DAQMSensor, DAQESensor,
    SensorFleet, discover_all, DiscoveredSensor,
    apply_sensor_settings, SensorSettings,
)

for d in discover_all(timeout=3.0):
    print(d)

sensor = DAQUSensor(port="COM3")
sensor.connect()
apply_sensor_settings(sensor, settings={"integration_time_ms": 64, "frame_avg": 20})
sensor.start_streaming()
# ... sensor.add_spectrum_callback(your_callback) ...
sensor.stop()
```

> **`apply_sensor_settings` स्वीकृत कुंजियाँ**— ठीक `integration_time_ms`, `frame_avg`, `ae_enabled`, `sunshine_diffuser_installed` (DAQ-E; `cap_id` के पक्ष में अप्रचलित), `filter_model` (DAQ-M), और `cap_id` (सभी DAQ प्रकार; `None`/`""`/`"none"` = नग्न सेंसर, कोई कैप सुधार नहीं)। अज्ञात कुंजियाँ**चुपचाप अनदेखा कर दी जाती हैं** — उदाहरण के लिए `{"integration_time": 64}` कुछ नहीं करता (यह  होना चाहिए000732). `{"applied": [...], "errors": {...}}` लौटाता है और कभी त्रुटि नहीं देता।

`chloros_sdk` केवल ऊपर उपयोग की गई कोर सतह को ही पुनः निर्यात करता है। पूर्ण `daq_sdk` सार्वजनिक API (22 नाम) निम्नलिखित जोड़ता है — उन्हें सीधे `daq_sdk` से आयात करें:

```python
from daq_sdk import (
    DAQULogger, DAQMLogger, DAQELogger,     # rotating-file recorders (the ones the GUI uses)
    ConnectResult, FleetRecordResult,       # SensorFleet result types
    discover_all_detailed, build_sensor,    # detailed discovery + build-by-descriptor
    scan_eth_devices, DaqEControl,          # DAQ-E Ethernet scan + control channel
    scan_ble_devices, detect_ble_device, list_ble_devices,   # DAQ-M BLE discovery
    detect_port, list_serial_ports,         # DAQ-U serial-port discovery
    TcpSerial,                              # serial-over-TCP transport shim
)
```

---

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

> `ChlorosAuthenticationError` और `ChlorosConfigurationError` बाकी के साथ शीर्ष स्तर पर निर्यात किए जाते हैं; उन्हें `chloros_sdk.exceptions` से भी आयात किया जा सकता हैOTX जैसा कि दिखाया गया है।

श्रेणीबद्ध संरचना:

```

ChlorosError
├── ChlorosBackendError           (backend failed to start / unreachable)
├── ChlorosConnectionError        (HTTP transport failure)
├── ChlorosLicenseError           (subscription / tier gate)
├── ChlorosAuthenticationError    (login required)
├── ChlorosConfigurationError     (bad configure() / open_project() inputs)
└── ChlorosProcessingError        (pipeline failed)

ChlorosConnectError                (raised by connect_camera / connect_array /
                                    connect_daq_sensor only — derives from
                                    plain Exception, NOT from ChlorosError,
                                    so `except ChlorosError` will not catch it)

lattice_sdk exceptions:
LatticeError
├── CameraNotFoundError
├── CameraConnectionError
├── StreamError
├── CaptureError
├── CalibrationError
├── NetworkError
└── DLSError
```

---

## अंत-से-अंत उदाहरण

### 1. कस्टम प्रोग्रेस बार के साथ एक फ़ोल्डर को प्रोसेस करें

```python
from chloros_sdk import ChlorosLocal

def progress(percent, message):
    bar = "#" * (percent // 5)
    print(f"\r[{bar:<20s}] {percent:3d}% {message}", end="", flush=True)

with ChlorosLocal() as cl:
    cl.create_project("FieldA_2026-05-26")
    cl.import_images("C:/DroneImages/Flight001", recursive=True)
    cl.configure(
        debayer="High Quality (Faster)",
        vignette_correction=True,
        reflectance_calibration=True,
        indices=["NDVI", "NDRE", "GNDVI", "SAVI"],
        export_format="TIFF (16-bit)",
    )
    cl.process(progress_callback=progress)
print()
```

### 2. लाइव लैटिस एरे → परावर्तन + DAQ संदर्भ

```python
import chloros_sdk

# Open a paired sensor first so the array's reflectance step has an
# absolute reference. Smart-detect picks USB / BLE / ETH automatically.
with chloros_sdk.connect_daq_sensor() as daq:
    with chloros_sdk.connect_array([
            "213800234", "214000533", "214701288", "214701292"
    ]) as arr:
        # Smart capture: wait for AE to settle, then snap
        arr.capture("./out", processing="reflectance", smart=True)

        # Record the corresponding DAQ frames as ground truth
        daq.record_start(output_dir="./out", device_name="sky-reference")
        # ... do whatever capture campaign ...
        info = daq.record_stop()
        print(info["path"], info["rows"])
```

### 3. प्रोजेक्ट-संचालित कैप्चर अभियान

```python
import time, chloros_sdk

with chloros_sdk.open_project("/home/user/Chloros Projects/Field_A") as proj:
    report = proj.connect_all(verbose=True, align=True)
    if report["arrays"]["errors"]:
        raise SystemExit(f"Array(s) failed to connect: {report['arrays']['errors']}")

    rig = proj.arrays["main_rig"]

    # Re-align right before the campaign
    rig.calibrate_alignment(num_frames=5)
    rig.export_alignment("./alignments/main_rig.json")

    # 50 sequential single-frame captures at 2 fps
    for i in range(50):
        frames = rig.capture(
            output_dir=f"./out/frame_{i:04d}",
            processing="reflectance",
            apply_calibration=True,
            apply_white_balance=True,
        )
        time.sleep(0.5)

    # End-of-day: process the captured folder. process() accepts only
    # mode/wait/progress_callback/poll_interval — indices come from the
    # project's saved config (or set them via ChlorosLocal.configure()).
    proj.process()
```

### 4. मल्टी-कैमरा फ्रेम-स्ट्रीम → NumPy पाइपलाइन

```python
import chloros_sdk
import numpy as np

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    rig = proj.arrays["main_rig"]

    for frames in rig.frame_stream(
            processing="radiance",
            fps=5.0, count=300,
            apply_calibration=True,
            apply_white_balance=True):
        # frames is {serial: numpy_array}; cams not delivering this tick are omitted
        for serial, frame in frames.items():
            print(serial, frame.shape, frame.dtype, frame.mean())
```

### 5. हेडलेस डायरेक्ट-हार्डवेयर (नो बैकएंड) कैप्चर स्क्रिप्ट

```python
from chloros_sdk import LatticeCamera, PRESETS, discover_cameras

cams = discover_cameras(timeout_ms=3000)
print(f"Found {len(cams)} cams")

settings = PRESETS["high_quality"]
for c in cams:
    with LatticeCamera(serial=c.serial, settings=settings) as cam:
        result = cam.capture(output_dir="./out", format="tiff")
        print(c.serial, result.filepath)
```

### 6. 4-कैम एरे को कनेक्ट करने से पहले क्षमता जांच

```python
import chloros_sdk

serials = ["214701288", "213800234", "214000533", "214701162"]

probe = chloros_sdk.analyze_array_network(
    master_serial=serials[0],
    slave_serials=serials[1:],
    width=2048, height=1536,
    pixel_format="BayerRG12",
)

if probe["status"] == "ok":
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12")
elif probe["status"] == "auto_capped_fps":
    r = probe["recommended"]
    print(f"Keeping resolution; capping trigger rate at "
          f"{r['recommended_target_fps']} fps")
    arr = chloros_sdk.connect_array(
        serials, width=2048, height=1536, pixel_format="BayerRG12",
        target_fps=r["recommended_target_fps"])
elif probe["status"] == "auto_shrunk":
    r = probe["recommended"]
    print(f"Auto-shrinking to {r['out_width']}x{r['out_height']} "
          f"binning={r['binning']} for sim-sync")
    arr = chloros_sdk.connect_array(
        serials,
        width=r["out_width"], height=r["out_height"],
        pixel_format=r["pixel_format"], binning=r["binning"])
elif probe["status"] == "needs_force_slip":
    print("Wire can't sustain sim-sync; falling back to slip mode")
    arr = chloros_sdk.connect_array(
        serials, force_tier="slip-emit-and-capture")
else:
    raise RuntimeError(f"Probe error: {probe.get('error')}")
```

### 7. कैप्चर रेसिपी के बराबर (शुद्ध Python)

CLI की रेसिपी डीएसएल का एक प्रत्यक्ष Python समकक्ष है:

```python
import time, chloros_sdk

with chloros_sdk.open_project("/path/to/proj") as proj:
    proj.connect_all()
    cam = proj.cameras["FrontLeft"]
    rig = proj.arrays["main_rig"]
    sky = proj.sensors["Sky"]

    # apply
    # (CameraHandle has no direct apply method; use the underlying lattice_sdk
    #  helper or the backend's /api/camera/<sn>/apply-settings via requests)
    # For most cases just use cam.cam.set_exposure(...) in direct mode or
    # the GUI's saved settings via project.connect_all().

    # wait
    time.sleep(2)

    # capture
    cam.capture("pose_a/", format="tiff", processing="radiance")

    # stream
    rig.stream(count=60, fps=5, output_dir="stream/", processing="raw")

    # sensor read
    print(sky.read())
```

---

## बैकएंड ऑटो-स्टार्ट

स्मार्ट-कनेक्ट एंट्री पॉइंट — `connect_camera`, `connect_array`, `connect_daq_sensor`, और `discover_lattice_cameras` — ये पतले HTTP क्लाइंट हैं जो यह मानते हैं कि एक बैकएंड `127.0.0.1:5000` पर सुन रहा है (स्मार्ट-कनेक्ट सतह का डिफ़ॉल्ट URL)। जब GUI या CLI पहले से चल रहा हो, तो एक होता है। एक खाली स्क्रिप्ट से, हो सकता है कि एक न हो — इसलिए ये फ़ंक्शन **बंडल किए गए बैकएंड बाइनरी को स्वचालित रूप से शुरू करते हैं** (विंडो-लेस, उसी तरह जैसे `ChlorosLocal` करता है) अपनी पहली कॉल से पहले, फिर इसके आने तक `backend_startup_timeout` तक प्रतीक्षा करें।

नियम:

- **केवल एक स्थानीय URL ही कभी स्पॉन किया जाता है।** एक `backend_url` जो `localhost` / `127.0.0.1` / `[::1]` की ओर इशारा कर रहा हो, पात्र है; किसी अन्य होस्ट को किसी और की मशीन माना जाता है और उसे कभी स्पॉन नहीं किया जाता है।
- **बैकएंड को पुन: उपयोग के लिए चालू छोड़ दिया जाता है** (CLI के समान) — जब आपकी स्क्रिप्ट बाहर निकलती है तो कोई निहित शटडाउन नहीं होता है। स्क्रिप्ट को फिर से चलाने पर लाइव बैकएंड का पुन: उपयोग होता है।
- **उनमें से किसी भी कॉल पर `auto_start_backend=False` के साथ बाहर निकलें** (उदा. जब आपने किसी रिमोट बैकएंड को पॉइंट किया हो, या आप प्रबंधित करते हैं आप स्वयं बैकएंड जीवनचक्र का प्रबंधन करते हैं)।

```python
import chloros_sdk

# Fresh shell, no backend running, no GUI open — this still works:
with chloros_sdk.connect_camera("213800234") as cam:   # spawns the backend
    cam.capture("output/")

# Remote backend (via tunnel — see Remote-Backend Mode): don't spawn one locally
arr = chloros_sdk.connect_array(serials,
                                backend_url="http://127.0.0.1:5000",
                                auto_start_backend=False)
```

यदि बंडल की गई बाइनरी का पता नहीं लगाया जा सकता या उसे शुरू नहीं किया जा सकता, तो बाद वाला HTTP कॉल एक खाली कनेक्शन के बजाय एक क्रियाशील, **प्लेटफ़ॉर्म-जागरूक** `ChlorosConnectError` त्रुटि उत्पन्न करता है-refused trace — Windows पर यह आपको डेस्कटॉप ऐप या एक `chloros-cli` कमांड की ओर निर्देशित करता है; Linux (कोई GUI नहीं) पर यह आपको एक `chloros-cli` कमांड या `.deb` की ओर निर्देशित करता है।

---

## वातावरण और हेडर

SDK हर बैकएंड HTTP कॉल को `X-Chloros-Client: sdk` के साथ चिह्नित करता है। बैकएंड SDK / CLI लाइसेंसिंग नियम लागू करता है (लॉगिन **और** GUI मुफ़्त-टियर पथ के बजाय। यह आयात समय पर स्वचालित रूप से सेट हो जाता है — आपको कुछ भी करने की आवश्यकता नहीं है।

`http://localhost` और `http://127.0.0.1` को स्थानीय बैकएंड के रूप में पता लगाया जाता है। अन्य होस्ट्स (जैसे कि आपकी अपनी एनालिटिक्स सेवा) के लिए कॉल को जस का तस छोड़ा जाता है।

`backend_url=` पास करके बैकएंड URL को ओवरराइड करें (या `ChlorosLocal` पर `api_url=`):

```python
chloros_sdk.connect_camera("213800234", backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_array(serials, backend_url="http://127.0.0.1:5000")
chloros_sdk.connect_daq_sensor(eth_host="daq-e-1.local",
                                backend_url="http://127.0.0.1:5000")
chloros_sdk.ChlorosLocal(backend_url="http://127.0.0.1:5000")
```

(एक नॉन-लूपबैक `backend_url` केवल एक स्रोत/dev बैकएंड — शिप किए गए बैकएंड केवल लूपबैक से बांधते हैं; टनल पैटर्न के लिए रिमोट-बैकएंड मोड देखें।

---

## संस्करणन और संगतता

- SDK संस्करण `chloros_sdk.__version__` के रूप में प्रदर्शित किया गया है।
- SDK व्यवहार को बंडल किए गए बैकएंड संस्करण से जोड़ता है। एक पुराने SDK को एक नए बैकएंड के साथ मिलाना आमतौर पर काम करता है (आगे-संगत एंडपॉइंट), लेकिन एक नए SDK को एक पुराने बैकएंड के साथ मिलाने से नए एंडपॉइंट पर `404` त्रुटियाँ सामने आ सकती हैं — मेल खाने के लिए डेस्कटॉप ऐप को अपग्रेड करें।
- स्मार्ट-कनेक्ट सतह (`connect_camera` / `connect_array` / `connect_daq_sensor`) और नेटवर्क-विश्लेषण एंडपॉइंट स्थिर JSON स्कीमा लौटाते हैं; नए फ़ील्ड जोड़ित होते हैं।

---

## समस्या-निवारण संकेत

- **`ChlorosAuthenticationError: Login required`** → इस मशीन पर एक बार `chloros-cli login EMAIL PASSWORD` चलाएँ, या Chloros डेस्कटॉप ऐप के माध्यम से साइन इन करें।
- **`ChlorosConnectError: No Chloros backend is running …`** → स्मार्ट-कनेक्ट स्वचालित रूप से एक स्थानीय बैकएंड शुरू करता है, इसलिए यह केवल तब दिखाई देता है जब बंडल किया गया बाइनरी नहीं मिल पाता/शुरू नहीं हो पाता (जैसे कि केवल पाइप वाला होस्ट जिसके पास कोई डेस्कटॉप पैकेज नहीं है)। यह संदेश प्लेटफ़ॉर्म-सचेत: Windows पर डेस्कटॉप ऐप खोलें या कोई `chloros-cli` कमांड चलाएँ; Linux पर एक `chloros-cli` कमांड चलाएँ (कोई GUI मौजूद नहीं है) या `.deb` इंस्टॉल करें। एक रिमोट बैकएंड के लिए, पास करें `backend_url=` (और `auto_start_backend=False`).
- **`CAMERA_AVAILABLE == False`** आयात पर → `lattice_sdk` लोड करने में विफल (आमतौर पर एरिना SDK रनटाइम DLL इंस्टॉल नहीं होते हैं)। गैर-कैमरा सतह अभी भी काम करती है।
- **एरे कनेक्ट सब-नेटिव रिज़ॉल्यूशन लौटाता है**→ बैकएंड का स्मार्ट-प्रैप वायर में फिट होने के लिए फ्रेम साइज़ को स्वचालित रूप से छोटा कर देता है। यह जानने के लिए `analyze_array_network()` का उपयोग करें कि ऐसा क्यों हो रहा है, फिर या तो लिंक को अपग्रेड करें, संकुचन को स्वीकार करें, या क्रमिक कैप्चर के लिए `force_tier="slip-emit-and-capture"` पास करें। श्रिंक सुरक्षा-नेट एग्रीगेट ओवर-सब्सक्रिप्शन (`oversubscribed: true`, fps फ़ील्ड 0) को कवर**नहीं** करता है: वायर के लिए बहुत अधिक कैमरे बिनिंग/ROI द्वारा ठीक नहीं किए जा सकते — कैमरा की संख्या कम करें, जंबो फ़्रेम सक्षम करें, या एक तेज़ NIC पर जाएँ (देखें [ओवर-सब्सक्रिप्शन](#over-subscription-the-per-cam-floor)).
- **`analyze_array_network()` NIC RX रिंग को छोटा (~0.26 MB) रिपोर्ट करता है / कनेक्ट गेट्स के साथ &quot;FRAMES WILL DROP&quot;** → होस्ट NIC का रिसीव रिंग अपने डिफ़ॉल्ट पर है (अक्सर NIC ड्राइवर अपडेट के बाद 32 पर रीसेट हो जाता है). एक Realtek USB 10GbE एडाप्टर पर `ReceiveBufferLen=256` और `PendingReceives=64` (उन्नत) सेट करें, फिर बैकएंड को रीस्टार्ट करें ताकि वह रिंग को फिर से पढ़े। पूर्ण प्रक्रिया: [CLI संदर्भ → होस्ट NIC सेटअप और ट्यूनिंग](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **रिस्टार्ट/शटडाउन पर होस्ट हैंग हो जाता है, बाद में WMI `Invalid class` त्रुटियाँ / NIC सक्षम नहीं होती** → पुराना USB 10GbE ड्राइवर `DRIVER_POWER_STATE_FAILURE` (BSOD `0x9F`). एडाप्टर ड्राइवर को एक वर्तमान संस्करण (≥ 2026) में अपडेट करें और रिसीव-रिंग सेटिंग्स को फिर से लागू करें। देखें [CLI संदर्भ → होस्ट NIC सेटअप और ट्यूनिंग](cli-reference.md#host-nic-setup--tuning-lattice-arrays).
- **रिफ्लेक्टेंस अस्वीकार कर दिया गया** → पूर्ण-स्केल परावर्तन के लिए एक लाइव DAQ को कैम (या एरे) से बांधना आवश्यक है। या तो GUI के माध्यम से बांधें या `processing="radiance"` (W/m²/sr/nm) का उपयोग करें, जिसके लिए एक जोड़े गए सेंसर की आवश्यकता नहीं होती है।
- **`smart=True` कैप्चर में अपेक्षा से अधिक समय लगता है** → AE अभिसरण दृश्य की गतिशीलता पर निर्भर करता है; यदि आप तेज़ चाहते हैं तो `exposure_tolerance_pct` को कसें या `stability_window_s` को छोटा करें (कम-स्थिर) ट्रिगर।

---

## यह भी देखें

- [CLI संदर्भ](cli-reference.md) — प्रत्येक CLI उप-कमांड एक SDK कॉल को दर्शाता है।
- [DAQ सेंसर गाइड](../daq/README.md) — सेंसर-विशिष्ट वायरिंग, कैलिब्रेशन, और रिकॉर्डिंग नियम।
- ऑनलाइन दस्तावेज़: `https://mapir.gitbook.io/chloros/api-python-sdk`</id></sn>
