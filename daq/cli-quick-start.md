# CLI क्विक स्टार्ट (pool-*)

शिप की गई `chloros-cli` ड्राइव्स **`daq pool-*`** कमांड फैमिली के माध्यम से DAQ सेंसरों को संचालित करती हैं — ये पतले HTTP क्लाइंट्स हैं जो Chloros बैकएंड के पर्सिस्टेंट सेंसर पूल के माध्यम से सेंसर को संचालित करते हैं। बैकएंड ट्रांसपोर्ट का मालिक है, इसलिए GUI, CLI, और SDK स्क्रिप्ट सभी पोर्ट पर लड़ने के बजाय एक ही लाइव हैंडल साझा करते हैं। एक ग्राहक को जो कुछ भी चाहिए, वह सब `pool-*` के माध्यम से पहुँच योग्य है: कनेक्ट, स्ट्रीम, कैलिब्रेटेड `.daq` फ़ाइलों को रिकॉर्ड करें, और कैप प्रोफाइल स्वैप करें।

`pool-*` रिलीज़ किए गए बिल्ड में एकमात्र DAQ सतह भी है। `chloros-cli daq --help`, `pool-*` सबकमांड की सूची देता है, और शिप किए गए बिल्ड पर एक डायरेक्ट-हार्डवेयर DAQ सबकमांड को कॉल करने पर एक स्पष्ट त्रुटि के साथ एक्जिट होता है जो गायब पैकेज का नाम बताती है और आपको `pool-*` की ओर वापस इंगित करती है — कुछ भी चुपचाप विफल नहीं होता है। (डायरेक्ट-हार्डवेयर कमांड केवल एक MAPIR सोर्स चेकआउट से चलते हैं; `pip install chloros-sdk` उन्हें भी प्रदान नहीं करता है।)

***

## पूर्वापेक्षाएँ

* **Chloros बैकएंड चल रहा होना चाहिए** — `pool-*` कमांड हार्डवेयर ड्राइवर नहीं, बल्कि HTTP क्लाइंट हैं। Windows पर, Chloros डेस्कटॉप ऐप शुरू करें (यह बैकएंड लॉन्च करता है)। हेडलेस Linux /Jetson पर, सेवा सक्षम करें: `sudo systemctl enable --now chloros-backend.service`।
* **एक Chloros+ (पेड-टियर) लॉगिन**: पहले `chloros-cli login` चलाएँ। अनुपालन सर्वर-साइड है — लॉगिन के बिना, कमांड्स `401 AUTH_REQUIRED` के साथ विफल हो जाती हैं; मुफ्त (आयरन) टियर पर वे `403 PLAN_UPGRADE_REQUIRED` के साथ विफल हो जाती हैं।
* कमांड डिफ़ॉल्ट रूप से `http://127.0.0.1:5000` को लक्षित करते हैं; यदि आपका बैकएंड कहीं और चलता है तो `daq pool-*` परिवार `CHLOROS_BACKEND_URL` पर्यावरण चर का सम्मान करता है।

***

## एक पाँच-मिनट का सत्र

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)

# 2. List the pool — this shows the sensor_id used by every command below
chloros-cli daq pool-list

# 3. Read the most recent calibrated spectrum frame (add --json for scripting)
chloros-cli daq pool-latest --sensor-id daq-e-def330 --json

# 4. Record a calibrated .daq file for 60 seconds
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 60 \
  --device-name "field-A"

# 5. Release the sensor when done
chloros-cli daq pool-disconnect --sensor-id daq-e-def330
```

***

## `pool-connect` — पूल में एक सेंसर खोलें

| वेरिएंट | अर्थ |
| --- | --- |
| `daq pool-connect` | स्मार्ट-डिटेक्ट: इस मशीन पर कोई भी DAQ खोजें। |
| `daq pool-connect --port PORT` | एक विशिष्ट सीरियल पोर्ट पर DAQ-U (जैसे `COM3`, `/dev/ttyUSB0`)। |
| `daq pool-connect --ble` | BLE पर DAQ-M, MAC स्वतः स्कैन किया गया। |
| `daq pool-connect --mac MAC` | ज्ञात BLE MAC पर DAQ-M (`--ble` का निहितार्थ)। |
| `daq pool-connect --eth-host HOST` | एक ज्ञात होस्टनेम या IP पर DAQ-E — **विश्वसनीय मार्ग**। |
| `daq pool-connect --eth` | स्वचालित-खोज (mDNS, एक ARP फॉलबैक के साथ) के साथ DAQ-E। नीचे दी गई चेतावनी देखें। |

ट्यूनिंग फ़्लैग, सभी वैकल्पिक:

| फ़्लैग | अर्थ |
| --- | --- |
| `--integration-time MS` / `-t MS` | मिलीसेकंड में मैन्युअल इंटीग्रेशन समय। |
| `--frame-avg N` / `-f N` | प्रति रिपोर्ट किए गए स्पेक्ट्रम पर औसत किए गए फ्रेम। |
| `--no-ae` | ऑटो-एक्सपोज़र (AE डिफ़ॉल्ट रूप से चालू है) अक्षम करें। |
| `--no-stream` | स्ट्रीम शुरू किए बिना कनेक्ट करें (बाद में `pool-stream --start` के साथ फिर से शुरू करें)। |
| `--cap-id CAP` | कैप-करेक्शन प्रोफ़ाइल; बैकएंड डिफ़ॉल्ट `sunshine_cosine` है। [`pool-set-cap`](#pool-set-cap-declare-the-fitted-cap) देखें। |

{% hint style="warning" %}
**`--eth` स्व-खोज चेतावनी।** मल्टी-होम्ड होस्ट (एक से अधिक सक्रिय नेटवर्क इंटरफ़ेस वाला) पर, बूट के बाद *पहला* `pool-connect --eth` सेंसर के स्वस्थ होने पर भी खाली आ सकता है — डिस्कवरी ब्राउज़ सेंसर के इंटरफ़ेस को तब मिस कर सकता है जब ARP कैश कोल्ड हो। यदि `--eth` को कुछ भी नहीं मिलता है, तो इसे फिर से आजमाएँ, या `--eth-host <ip-or-hostname>` के साथ खोज को पूरी तरह से छोड़ दें, जो मल्टी-होम्ड मशीनों पर विश्वसनीय तरीका है। DAQ-E का होस्टनेम `daq-e-<id>.local` (जैसे `daq-e-def330.local`) है; इसका सादा IP भी काम करता है।
{% endhint %}

## `pool-list` — देखें कि क्या जुड़ा है

बैकएंड पूल में हर सेंसर को दिखाता है, जिसमें वह `sensor_id` भी शामिल है जिसकी हर दूसरी कमांड को आवश्यकता होती है:

| मॉडल | `sensor_id` आकार | उदाहरण |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5-अक्टेड हाइफ़नयुक्त | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

## `pool-latest` — स्पेक्ट्रम फ़्रेम पढ़ें

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

सबसे हालिया फ़्रेम, या सबसे हालिया `--recent N` फ़्रेम लौटाता है; `--json` स्क्रिप्टिंग के लिए मशीन-पठनीय आउटपुट उत्सर्जित करता है। फ़्रेम 135-पॉइंट, 340–1010 nm ग्रिड पर रेडियोमेट्रिक रूप से कैलिब्रेटेड स्पेक्ट्रल इरैडियंस (W/m²/nm) होते हैं, जिसमें सेंसर की कैप प्रोफ़ाइल पहले से ही लागू होती है। मात्रात्मक विकिरण संख्याओं के लिए, कम से कम 15 सेकंड के फ्रेमों का औसत लें — यह एक उपकरण की विशेषता है, दोष नहीं।

## `pool-stream` — स्ट्रीमिंग को रोकें या फिर से शुरू करें

```bash
chloros-cli daq pool-stream --sensor-id daq-e-def330 --stop    # pause
chloros-cli daq pool-stream --sensor-id daq-e-def330 --start   # resume
```

## `pool-record` — एक `.daq` फ़ाइल रिकॉर्ड करें

```bash
chloros-cli daq pool-record --sensor-id daq-e-def330 --duration 150 \
  --output ~/Documents/spectra --device-name "rooftop-A"
chloros-cli daq pool-record --sensor-id daq-e-def330 --stop
```

| फ़्लैग | डिफ़ॉल्ट | अर्थ |
| --- | --- | --- |
| `--duration SEC` / `-d SEC` | `0` | सेकंड में रिकॉर्डिंग लंबाई; `0` का अर्थ है जब तक आप `--stop` जारी नहीं करते तब तक चलाना। |
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | आउटपुट डायरेक्टरी, **बैकएंड चलाने वाली मशीन पर** हल की गई। |
| `--device-name NAME` | — | रिकॉर्डिंग के साथ संग्रहीत लेबल। |
| `--stop` | — | चल रही रिकॉर्डिंग को रोकें। |

{% hint style="info" %}
रिकॉर्डिंग बैकएंड में होती है, इसलिए `.daq` फ़ाइल **बैकएंड मशीन** के फ़ाइल सिस्टम पर पहुँचती है — डिफ़ॉल्ट रूप से `~/Documents/DAQ Live View/` में, ज़रूरी नहीं कि वहीं जहाँ आपने CLI चलाया हो। फ़ाइल नामों में सेंसर आईडी और एक टाइमस्टैम्प शामिल होता है।
{% endhint %}

## `pool-set-cap` — फिट की गई कैप घोषित करें

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

कैप आईडी हर स्पेक्ट्रम पर लागू होने वाली फैक्ट्री-मापी गई सुधार प्रोफ़ाइल का चयन करती है, और इसका **सेंसर पर भौतिक रूप से लगी कैप से मेल खाना अनिवार्य है** — न तो सेंसर और न ही सॉफ़्टवेयर अपने आप कैप का पता लगा सकता है, और यह चयन हर `.daq` फ़ाइल में अंकित हो जाता है। हर जगह डिफ़ॉल्ट `sunshine_cosine` है (हर DAQ सनशाइन कॉसाइन-करेक्टर कैप के साथ आता है, डिज़ाइन के अनुसार ~12× अटेन्यूएशन — एक घोषित न किया गया कैप परिवर्तन स्पेक्ट्रा को लगभग उसी गुणक से गलत-सुधार करता है)।

| `--cap-id` | पर उपलब्ध |
| --- | --- |
| `sunshine_cosine` (डिफ़ॉल्ट) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | केवल DAQ-U |
| `none` | केवल DAQ-E — नोट देखें |

सेंसर के सेट के बाहर का कैप आईडी कनेक्ट करते समय एक स्पष्ट त्रुटि के साथ अस्वीकार कर दिया जाता है। `none` (DAQ-E) का अर्थ है कि कैप भौतिक रूप से हटा दिया गया है — यह फिर भी DAQ-E के धँसे हुए ग्लास डिफ्यूज़र के लिए फैक्ट्री ज्योमेट्री प्रोफ़ाइल लागू करता है, इसलिए यह कोई निष्क्रिय क्रिया (no-op) नहीं है, और एक बिना कैप वाला DAQ-E एक बेंच कॉन्फ़िगरेशन है, न कि एक समर्थित फील्ड कॉन्फ़िगरेशन। (एक खाली DAQ-U पूरी तरह से खाली है और उसे किसी भी सुधार प्रोफ़ाइल की आवश्यकता नहीं है; DAQ-M का उपयोग इसके सनशाइन कैप के साथ किया जाता है।)

## `pool-disconnect` — सेंसर रिलीज़ करें

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

***

## कमांड सारांश

| कमांड | उद्देश्य |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | बैकएंड पूल में एक सेंसर खोलें। |
| `daq pool-list` | पूल में प्रत्येक सेंसर को उसके `sensor_id` के साथ दिखाएँ। |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | सबसे हालिया N कैलिब्रेटेड स्पेक्ट्रम फ्रेम। |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | स्ट्रीमिंग फिर से शुरू / विराम करें। |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--stop]` | एक `.daq` रिकॉर्डिंग शुरू / रोकें (बैकएंड-साइड)। |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | रनटाइम पर कैप-करेक्शन प्रोफ़ाइल बदलें। |
| `daq pool-disconnect --sensor-id ID [--all]` | एक सेंसर, या सभी को रिलीज़ करें। |

***

## पहली बार DAQ-E कनेक्ट करने में समस्या निवारण

1. DAQ-E में कोई स्टेटस LED नहीं है — स्विच या इंजेक्टर पोर्ट पर PoE/लिंक इंडिकेटर के माध्यम से पावर की पुष्टि करें, और पावर-अप के बाद इसे बूट होने और नेटवर्क से जुड़ने के लिए कुछ सेकंड का समय दें।
2. बैकएंड मशीन सेंसर के **समान ब्रॉडकास्ट डोमेन** पर होनी चाहिए — mDNS राउटरों से होकर नहीं गुज़रता।
3. Windows पर, पहली बार चलाने पर डिफेंडर फ़ायरवॉल प्रॉम्प्ट स्वीकार करें (mDNS UDP 5353, DAQ-E डेटा UDP 5002, PTP UDP 319/320)।
4. क्या `--eth` से अभी भी कोई प्रतिक्रिया नहीं मिल रही है? यूनिट के होस्टनेम (`daq-e-<id>.local`) या आईपी के साथ `--eth-host` का उपयोग करें — यह एक विश्वसनीय मार्ग है, खासकर मल्टी-होम्ड होस्ट्स पर।

***{% hint style="info" %}**AI सहायकों के लिए सुझाव।** इस मैनुअल का प्रत्येक पृष्ठ कच्चे मार्कडाउन के रूप में परोसा जाता है — किसी पृष्ठ के छोटे अक्षरों वाले URL स्लग (इस पृष्ठ: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`) में `.md` जोड़ें; मशीन-पठनीय अनुक्रमणिका `https://mapir.gitbook.io/chloros/llms.txt` है। `chloros-cli daq` और प्रत्येक अन्य कमांड परिवार के पूर्ण फ्लैग-स्तर के दस्तावेज़ीकरण के लिए, [CLI संदर्भ](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`) प्राप्त करें; Python का पथ [SDK संदर्भ](../reference/sdk-reference.md) में `chloros_sdk.connect_daq_sensor()` है।
{% endhint %}
