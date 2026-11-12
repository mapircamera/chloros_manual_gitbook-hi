# CLI क्विक स्टार्ट (pool-*)

शिप की गई `chloros-cli` ड्राइव DAQ सेंसरों को **`daq pool-*`** कमांड फैमिली के माध्यम से करती है — ये पतले HTTP क्लाइंट हैं जो Chloros बैकएंड के पर्सिस्टेंट सेंसर पूल के माध्यम से सेंसर को ऑपरेट करते हैं। बैकएंड ट्रांसपोर्ट का मालिक है, इसलिए GUI, CLI, और SDK स्क्रिप्ट्स सभी पोर्ट के लिए प्रतिस्पर्धा करने के बजाय एक ही लाइव हैंडल साझा करते हैं। एक ग्राहक को जो कुछ भी चाहिए, वह सब `pool-*` के माध्यम से पहुँच योग्य है: कनेक्ट, स्ट्रीम, कैलिब्रेटेड `.daq` फ़ाइलों को रिकॉर्ड करें, और कैप प्रोफाइल स्वैप करें।

`pool-*` रिलीज़ किए गए बिल्ड में समर्थित DAQ सतह है। `chloros-cli daq --help` `pool-*` उप-कमांडों को सूचीबद्ध करता है, और `daq discover` / `daq list` `pool-discover` के समान स्कैन चलाता है। जहाँ कोई डायरेक्ट-हार्डवेयर DAQ सबकमांड उपलब्ध नहीं है, वहाँ यह एक स्पष्ट त्रुटि के साथ बाहर निकलता है जिसमें गायब पैकेज का नाम होता है और आपको `pool-*` की ओर वापस निर्देशित करता है — कुछ भी चुपचाप विफल नहीं होता। (`pip install chloros-sdk` भी डायरेक्ट-हार्डवेयर कमांड प्रदान नहीं करता है।)

***

## पूर्वापेक्षाएँ

* **Chloros बैकएंड चल रहा होना चाहिए** — `pool-*` कमांड हार्डवेयर ड्राइवर नहीं, बल्कि HTTP क्लाइंट हैं। Windows पर, Chloros डेस्कटॉप ऐप शुरू करें (यह बैकएंड लॉन्च करता है)। हेडलेस Linux/जेसन पर, अपने उपयोगकर्ता के रूप में सेवा को सक्षम करें: `sudo systemctl enable --now chloros-backend@$USER.service` (देखें [Linux इंस्टॉलेशन](../linux/linux-installation.md#always-on-ptp-for-headless-hosts)).
* **Chloros+ (पेड-टियर) लॉगिन**: पहले `chloros-cli login` चलाएँ। अनुपालन सर्वर-साइड पर होता है — लॉगिन के बिना, कमांड्स `401 AUTH_REQUIRED` के साथ विफल हो जाती हैं; मुफ्त (आयरन) स्तर पर वे `403 PLAN_UPGRADE_REQUIRED` के साथ विफल हो जाती हैं।
* कमांड डिफ़ॉल्ट रूप से `http://127.0.0.1:5000` को लक्षित करते हैं; `daq pool-*` परिवार `CHLOROS_BACKEND_URL` पर्यावरण चर का सम्मान करता है यदि आपका बैकएंड कहीं और चल रहा है।

***

## एक पाँच-मिनट का सत्र

```bash
# 1. Connect a sensor into the backend pool (pick the line matching your model)
chloros-cli daq pool-connect                                  # smart-detect any DAQ
chloros-cli daq pool-connect --port COM3                      # DAQ-U on a specific COM port
chloros-cli daq pool-connect --mac AA:BB:CC:DD:EE:FF          # DAQ-M by BLE MAC
chloros-cli daq pool-connect --eth-host daq-e-def330.local    # DAQ-E by hostname (reliable)
#    (a DAQ-M's BLE MAC is not printed anywhere: find it with `chloros-cli daq pool-discover --only ble`)

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
| `daq pool-connect --eth-host HOST` | ज्ञात होस्टनेम या IP पर DAQ-E — **विश्वसनीय मार्ग**। |
| `daq pool-connect --eth` | ऑटो-डिस्कवरी के साथ DAQ-E (mDNS, एक ARP फॉलबैक के साथ)। नीचे दी गई चेतावनी देखें। |

ट्यूनिंग फ़्लैग, सभी वैकल्पिक:

| फ़्लैग | अर्थ |
| --- | --- |
| `--integration-time MS` / `-t MS` | मिलीसेकंड में मैन्युअल इंटीग्रेशन समय। |
| `--frame-avg N` / `-f N` | प्रति रिपोर्ट किए गए स्पेक्ट्रम के औसत फ़्रेम। |
| `--no-ae` | ऑटो-एक्सपोज़र अक्षम करें (AE डिफ़ॉल्ट रूप से चालू है)। |
| `--no-stream` | स्ट्रीम शुरू किए बिना कनेक्ट करें (बाद में `pool-stream --start` के साथ फिर से शुरू करें)। |
| `--cap-id CAP` | कैप-करेक्शन प्रोफ़ाइल; बैकएंड डिफ़ॉल्ट `sunshine_cosine` है। देखें [`pool-set-cap`](#pool-set-cap-declare-the-fitted-cap). |

पूल में पहले से ही किसी दूसरे पते — इसके `daq-e-<id>.local` होस्टनेम या इसके IP — के तहत मौजूद DAQ-E को कनेक्ट करने पर, यूनिट को दूसरी बार खोलने के बजाय मौजूदा पूल प्रविष्टि लौटाई जाती है।

{% hint style="warning" %}
**`--eth` स्वचालित-खोज चेतावनी।** मल्टी-होम वाले होस्ट (एक से अधिक सक्रिय नेटवर्क इंटरफ़ेस वाला) पर, बूट के बाद *पहला* `pool-connect --eth` सेंसर के स्वस्थ होने पर भी खाली आ सकता है — जब ARP कैश ठंडा होता है तो डिस्कवरी ब्राउज़ सेंसर के इंटरफ़ेस को मिस कर सकता है। यदि `--eth` को कुछ भी नहीं मिलता है, तो इसे दोबारा आज़माएँ, या `--eth-host <ip-or-hostname>` के साथ डिस्कवरी को पूरी तरह से छोड़ दें, जो मल्टी-होम्ड मशीनों पर विश्वसनीय तरीका है। DAQ-E का होस्टनेम `daq-e-<id>.local` (जैसे `daq-e-def330.local`) है; इसका सादा IP भी काम करता है।
{% endhint %}

## `pool-discover` — कनेक्ट किए बिना स्कैन करें

```bash
chloros-cli daq pool-discover                  # USB, BLE and Ethernet
chloros-cli daq pool-discover --only ble       # BLE only: this is how you find a DAQ-M's MAC
chloros-cli daq pool-discover --json           # machine-readable
```

उन सेंसरों को स्कैन करता है जिन्हें आप कनेक्ट कर सकते हैं और कनेक्ट किए बिना, कनेक्ट करने का पता दिखाता है। `--only`, `usb,ble,eth` का एक अल्पविराम-से-अलग किया हुआ उपसमूह लेता है; `--timeout SEC` प्रति-परिवहन स्कैन समय निर्धारित करता है (डिफ़ॉल्ट 5 s, 1–20 s तक क्लैम्प किया गया)। पूल में पहले से खुले सेंसर सूचीबद्ध नहीं किए जाते हैं (एक कनेक्टेड DAQ-M विज्ञापन करना बंद कर देता है) — उनके लिए `pool-list` का उपयोग करें। एक शिप किए गए बिल्ड पर, `chloros-cli daq discover` और `daq list` यह वही स्कैन चलाते हैं।

## `pool-list` — देखें कि क्या जुड़ा है

बैकएंड पूल में हर सेंसर को दिखाता है, जिसमें वह `sensor_id` भी शामिल है जिसकी हर दूसरी कमांड को आवश्यकता होती है:

| मॉडल | `sensor_id` आकार | उदाहरण |
| --- | --- | --- |
| DAQ-U / DAQ-M | 5-octet हाइफ़न वाला | `CB-7C-A8-2E-5F` |
| DAQ-E | `daq-e-<6 hex digits>` | `daq-e-def330` |

एक DAQ-E जिसका PTP बंद कर दिया गया है (यह सेटिंग यूनिट पर सहेजी जाती है) को `pool-list` में `!! PTP is switched OFF on this unit (saved in its flash): its timestamps are not PTP-synced.` के साथ फ़्लैग किया जाता है और उसे वापस चालू करने वाले कमांड के साथ — देखें [`pool-enable-ptp`](#pool-enable-ptp-switch-a-daq-es-ptp-back-on).

## `pool-latest` — स्पेक्ट्रम फ़्रेम पढ़ें

```bash
chloros-cli daq pool-latest --sensor-id daq-e-def330 --recent 10 --json
```

सबसे हालिया फ़्रेम, या सबसे हालिया `--recent N` फ़्रेम लौटाता है; `--json` स्क्रिप्टिंग के लिए मशीन-पठनीय आउटपुट उत्सर्जित करता है। फ़्रेम 135-पॉइंट, 340–1010 nm ग्रिड पर रेडियोमेट्रिक रूप से कैलिब्रेटेड स्पेक्ट्रल इरैडियंस (W/m²/nm) होते हैं, जिसमें सेंसर की कैप प्रोफ़ाइल पहले से ही लागू होती है। मात्रात्मक विकिरण संख्याओं के लिए, कम से कम 15 सेकंड के फ्रेमों का औसत निकालें — यह एक उपकरण की विशेषता है, कोई दोष नहीं।

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
| `--duration SEC` / `-d SEC` | `0` | रिकॉर्डिंग की लंबाई सेकंड में; `0` का अर्थ है जब तक आप `--stop` जारी नहीं करते तब तक चलाना। |
| `--output DIR` / `-o DIR` | `~/Documents/DAQ Live View/` | आउटपुट निर्देशिका। इस कंप्यूटर पर बैकएंड के साथ, एक सापेक्ष पथ उस फ़ोल्डर के सापेक्ष हल किया जाता है जिसमें आपने कमांड चलाया था; एक रिमोट बैकएंड के साथ पथ **बैकएंड चलाने वाली मशीन पर** हल किया जाता है। |
| `--device-name NAME` | — | रिकॉर्डिंग के साथ संग्रहीत लेबल। |
| `--stop` | — | चल रही रिकॉर्डिंग को रोकें। |
| `--confirm-after SEC` | `20` | शुरू करने के बाद, पहली पंक्ति लिखे जाने के लिए इस लंबी प्रतीक्षा करें, और यदि कोई नहीं आती है तो ज़ोर से चेतावनी दें (`!! WARNING: no rows written after …s. This recording is producing an EMPTY file.`)। `0` जाँच को अक्षम करता है। |
| `--heartbeat SEC` | `30` | `--duration` के साथ, हर SEC सेकंड में पंक्तियों की संख्या प्रिंट करें और यदि यह बढ़ना बंद हो जाए तो चेतावनी दें। `0` इसे अक्षम कर देता है। |

{% hint style="info" %}
रिकॉर्डिंग बैकएंड में होती है, इसलिए `.daq` फ़ाइल **बैकएंड मशीन** के फ़ाइलसिस्टम पर आती है — डिफ़ॉल्ट रूप से `~/Documents/DAQ Live View/` में, ज़रूरी नहीं कि वह वहीं हो जहाँ आपने CLI चलाया था। फ़ाइल नामों में सेंसर आईडी और एक टाइमस्टैम्प शामिल होता है।
{% endhint %}

## `pool-set-cap` — फिट की गई कैप घोषित करें

```bash
chloros-cli daq pool-set-cap --sensor-id daq-e-def330 --cap-id sunshine_cosine
```

कैप आईडी हर स्पेक्ट्रम पर लागू होने वाली फैक्ट्री-मापी गई सुधार प्रोफ़ाइल का चयन करती है, और इसका **सेंसर पर भौतिक रूप से लगी कैप से मेल खाना अनिवार्य है** — न तो सेंसर और न ही सॉफ़्टवेयर अकेले कैप का पता लगा सकता है, और यह चयन हर `.daq` फ़ाइल में अंकित हो जाता है। डिफ़ॉल्ट हर जगह `sunshine_cosine` है (हर DAQ सनशाइन कॉसाइन-करेक्टर कैप के साथ आता है, डिज़ाइन के अनुसार ~12× अटेन्यूएशन — एक घोषित न किया गया कैप परिवर्तन स्पेक्ट्रा को लगभग उसी गुणक से गलत-सुधार करता है)।

| `--cap-id` | पर उपलब्ध |
| --- | --- |
| `sunshine_cosine` (डिफ़ॉल्ट) | DAQ-U, DAQ-M, DAQ-E |
| `fov_15`, `fov_45`, `fov_90` | DAQ-U, DAQ-E |
| `fov_30`, `fov_60` | केवल DAQ-U |
| `none` | केवल DAQ-E — नोट देखें |

सेंसर के सेट के बाहर का कैप आईडी कनेक्ट होने पर एक स्पष्ट त्रुटि के साथ अस्वीकार कर दिया जाता है। `none` (DAQ-E) का अर्थ है कि कैप भौतिक रूप से हटा दिया गया है — यह फिर भी DAQ-E के धँसे हुए ग्लास डिफ्यूज़र के लिए फैक्ट्री ज्योमेट्री प्रोफ़ाइल लागू करता है, इसलिए यह कोई निष्क्रिय क्रिया (no-op) नहीं है, और एक खाली DAQ-E एक बेंच कॉन्फ़िगरेशन है, न कि एक समर्थित फील्ड कॉन्फ़िगरेशन। (एक खाली DAQ-U पूरी तरह से खाली है और उसे किसी भी सुधार प्रोफ़ाइल की आवश्यकता नहीं है; DAQ-M का उपयोग इसके सनशाइन कैप के साथ किया जाता है।)

## `pool-disconnect` — सेंसर रिलीज़ करें

```bash
chloros-cli daq pool-disconnect --sensor-id daq-e-def330   # one sensor
chloros-cli daq pool-disconnect --all                      # everything in the pool
```

`pool-disconnect` किसी भी सेंसर को रिलीज़ करता है जिसे भी किसी ने खोला हो, इसलिए यह पूल में एक क्रैश हुई स्क्रिप्ट द्वारा छोड़े गए सेंसर को भी मुक्त करता है (इस पर एक रिकॉर्डिंग पहले समाप्त हो जाती है)। एक सेंसर जो पूल में नहीं है, उसके पास रिलीज़ करने के लिए कुछ नहीं है: कमांड `<sensor-id>: already released (not in the pool)` प्रिंट करती है और `0` से बाहर निकलती है। एक रिलीज़ जिसे बैकएंड ने अस्वीकार कर दिया या पुष्टि नहीं की, `1` से बाहर निकलती है।

## `pool-enable-ptp` — DAQ-E का PTP वापस चालू करें

```bash
chloros-cli daq pool-enable-ptp --sensor-id daq-e-def330
```

एक DAQ-E अपनी PTP ऑन/ऑफ सेटिंग को अपनी ही मेमोरी में रखता है, इसलिए एक यूनिट जिसकी PTP बंद कर दी गई थी, वह बंद ही रहती है — और इसके टाइमस्टैम्प PTP-सिंक नहीं होते हैं — जब तक कि इसे वापस ऑन नहीं किया जाता। `pool-list` ऐसे यूनिट को फ़्लैग करता है और उसके लिए यह कमांड प्रिंट करता है। Chloros अपने आप किसी यूनिट को वापस चालू नहीं करता है, क्योंकि बंद करना जानबूझकर किया गया हो सकता है। 1.3.0 में नया।

## DAQ-E डिवाइस सेटिंग्स

```bash
chloros-cli daq pool-info --sensor-id daq-e-def330
chloros-cli daq pool-set-network --sensor-id daq-e-def330 \
  --static 192.168.1.50 --gateway 192.168.1.1
chloros-cli daq pool-set-network --sensor-id daq-e-def330 --auto
chloros-cli daq pool-set-auto-stream --sensor-id daq-e-def330 --on
chloros-cli daq pool-reboot --sensor-id daq-e-def330
chloros-cli daq pool-update-firmware --sensor-id daq-e-def330
```

`pool-info` सेंसर का फर्मवेयर (और यह कि क्या इस Chloros रिलीज़ में एक नया फर्मवेयर है), उसका पता और उसे कैसे प्राप्त किया गया, सहेजी गई नेटवर्क सेटिंग जब यह चल रही सेटिंग से भिन्न हो, पावर-ऑन स्ट्रीमिंग और PTP दिखाता है। `pool-set-network` परिवर्तन लागू करने के लिए सेंसर को पुनः आरंभ करता है और इसे अपनी पिछली सेटिंग्स के साथ फिर से जोड़ता है; `--no-reboot` केवल इसे सहेजता है। इस कंप्यूटर के नेटवर्क पर एक स्थिर पता होना चाहिए, अन्यथा Chloros इसे अस्वीकार कर देता है; `--force` उस पर अधिलेखन कर देता है, केवल उस कंप्यूटर के लिए जिसे पुनः पता दिया जाएगा या उस पर राउट किया जाएगा। जब सेंसर रिकॉर्ड कर रहा होता है तो रीस्टार्ट अस्वीकार कर दिए जाते हैं। `pool-update-firmware` कमांड लाइन के लिए GUI के **Update to \<version\>** के समान है, और यह Linux पर एक DAQ-E को अपडेट करने का तरीका है, जिसमें कोई GUI नहीं है। 1.3.0 में नया।***

## कमांड सारांश

| कमांड | उद्देश्य |
| --- | --- |
| `daq pool-connect [--port P \| --ble \| --mac M \| --eth \| --eth-host H] [-t MS] [-f N] [--no-ae] [--no-stream] [--cap-id CAP]` | बैकएंड पूल में एक सेंसर खोलें। |
| `daq pool-discover [--only usb,ble,eth] [--timeout SEC] [--json]` | कनेक्ट किए बिना सेंसरों को स्कैन करें (DAQ-M का BLE MAC खोजता है)। |
| `daq pool-list` | पूल में प्रत्येक सेंसर को उसके `sensor_id` के साथ दिखाएँ। |
| `daq pool-latest --sensor-id ID [--recent N] [--json]` | सबसे हालिया N कैलिब्रेटेड स्पेक्ट्रम फ्रेम। |
| `daq pool-stream --sensor-id ID [--start \| --stop]` | स्ट्रीमिंग फिर से शुरू / विराम करें। |
| `daq pool-record --sensor-id ID [-d SEC] [-o DIR] [--device-name NAME] [--confirm-after SEC] [--heartbeat SEC] [--stop]` | एक `.daq` रिकॉर्डिंग शुरू / रोकें (बैकएंड-साइड)। |
| `daq pool-set-cap --sensor-id ID --cap-id CAP` | रनटाइम पर कैप-करेक्शन प्रोफ़ाइल बदलें। |
| `daq pool-disconnect --sensor-id ID [--all]` | एक सेंसर, या सभी को रिलीज़ करें। |
| `daq pool-enable-ptp --sensor-id ID` | उस DAQ-E के लिए PTP को वापस चालू करें जिसका PTP बंद है। |
| `daq pool-info --sensor-id ID [--json]` | एक DAQ-E का फर्मवेयर, पता, सहेजी गई नेटवर्क सेटिंग, पावर-ऑन स्ट्रीमिंग और PTP। |
| `daq pool-set-network --sensor-id ID (--auto \| --static IP [--mask M] [--gateway G]) [--no-reboot] [--force]` | DHCP या एक स्थिर पता; सेंसर को पुनः आरंभ और पुनः कनेक्ट करता है। |
| `daq pool-set-auto-stream --sensor-id ID (--on \| --off)` | पावर-ऑन पर स्ट्रीम करें। |
| `daq pool-reboot --sensor-id ID` | DAQ-E को पुनः आरंभ करें और फिर से कनेक्ट करें। |
| `daq pool-update-firmware --sensor-id ID` | इस रिलीज़ के DAQ-E फर्मवेयर को किसी पुराने सेंसर पर फ्लैश करें। |

***

## पहली DAQ-E कनेक्ट की समस्या निवारण

1. DAQ-E में कोई स्टेटस LED नहीं है — स्विच या इंजेक्टर पोर्ट पर PoE/लिंक इंडिकेटर के माध्यम से पावर की पुष्टि करें, और पावर-अप के बाद इसे बूट होने और नेटवर्क से जुड़ने के लिए कुछ सेकंड का समय दें।
2. बैकएंड मशीन सेंसर के **समान ब्रॉडकास्ट डोमेन** पर होनी चाहिए — mDNS राउटरों से होकर नहीं गुज़रता।
3. फ़ायरवॉल को mDNS (UDP 5353), DAQ-E डेटा (UDP 5002) और PTP (UDP 319/320) को अंदर आने देना चाहिए। Windows इंस्टॉलर इसके लिए एक नियम जोड़ता है, और Linux `.deb` उन्हें एक `ufw` / `firewalld` में खोलता है जो इंस्टॉल करने के समय सक्रिय था; तीसरे पक्ष के फ़ायरवॉल, या बाद में सक्षम किए गए फ़ायरवॉल को इन्हें अनुमति देने की आवश्यकता है।
4. अभी भी `--eth` से कोई जानकारी नहीं मिल रही है? यूनिट के होस्टनेम (`daq-e-<id>.local`) या IP के साथ `--eth-host` का उपयोग करें, जो डिस्कवरी को छोड़ देता है।

***
{% hint style="info" %}
**AI सहायकों के लिए सुझाव।** इस मैनुअल का हर पेज कच्चे मार्कडाउन के रूप में परोसा जाता है — किसी पेज के छोटे अक्षरों वाले URL स्लग (इस पेज: `https://mapir.gitbook.io/chloros/daq/cli-quick-start.md`) में `.md` जोड़ें; मशीन-पठनीय अनुक्रमणिका `https://mapir.gitbook.io/chloros/llms.txt` है। `chloros-cli daq` और हर दूसरे कमांड परिवार के पूर्ण फ़्लैग-स्तर के दस्तावेज़ीकरण के लिए, [CLI संदर्भ](../reference/cli-reference.md) (`https://mapir.gitbook.io/chloros/reference/cli-reference.md`) प्राप्त करें; [SDK संदर्भ](../reference/sdk-reference.md) में Python पथ `chloros_sdk.connect_daq_sensor()` है।
{% endhint %}
