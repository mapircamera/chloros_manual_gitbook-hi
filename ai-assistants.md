# एआई असिस्टेंट्स के साथ Chloros का उपयोग

यह मैनुअल दो दर्शकों के लिए लिखा गया है: मनुष्य, और एआई असिस्टेंट्स जिनके माध्यम से मनुष्य लगातार काम कर रहे हैं। हर पेज पर सटीक मान, डिफ़ॉल्ट, और कॉपी-पेस्ट करने योग्य कमांड प्रकाशित किए जाते हैं ताकि कोई असिस्टेंट (क्लाउड, ChatGPT, कोपायलट, एक कोडिंग एजेंट, …) पहली कोशिश में ही काम करने वाली Chloros ऑटोमेशन लिख सके।

Chloros संस्करण: **

1.2.0**। CLI / SDK प्लेटफ़ॉर्म: Windows 10/11 x64 और Linux (x86\_64 / Jetson aarch64)।

## अपने असिस्टेंट को क्या दें

| संसाधन | URL | यह किस लिए है |
| --- | --- | --- |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | इस मैनुअल में प्रत्येक पृष्ठ का मशीन-पठनीय अनुक्रमणिका। |
| **CLI संदर्भ** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | संपूर्ण `chloros-cli` कमांड सतह: हर कमांड, फ़्लैग, डिफ़ॉल्ट, एग्जिट कोड, और आउटपुट-फ़ोल्डर नियम। LLM उपभोग के लिए लिखा गया। |
| **SDK संदर्भ** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | पूरा `chloros_sdk` Python API: क्लासेस, सिग्नेचर, अपवाद, और काम किए हुए उदाहरण। LLM उपभोग के लिए लिखा गया। |
| **कोई भी पृष्ठ कच्चे मार्कडाउन के रूप में** | पृष्ठ में `.md` जोड़ें URL | उदाहरण के लिए `https://mapir.gitbook.io/chloros/reference/sdk-reference.md` पृष्ठ को कच्चे मार्कडाउन के रूप में लौटाता है — किसी संदर्भ विंडो में पेस्ट करने या किसी एजेंट से प्राप्त करने के लिए आदर्श। |

मैनुअल में लिंक: [CLI संदर्भ](reference/cli-reference.md) · [SDK संदर्भ](reference/sdk-reference.md).

{% hint style="info" %}
ये दो संदर्भ पृष्ठ स्वयं-समावेशी हैं: इन्हें में से एक को पढ़ने वाला एक सहायक, एक सही स्क्रिप्ट लिखने के लिए मैनुअल के बाकी हिस्से की आवश्यकता नहीं रखता है।
{% endhint %}

## प्रॉम्प्ट रेसिपी

`<placeholders>` की प्रतिलिपि बनाएँ, `<placeholders>` को भरें, और अपने असिस्टेंट में पेस्ट करें।

### 1. एक फ़्लाइट फ़ोल्डर को NDVI में प्रोसेस करें

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md.
Then write a script for <Windows PowerShell | bash> that:
1. logs in with `chloros-cli login <email> '<password>'` (only needed once per machine),
2. processes the folder <path/to/flight_001> with reflectance and the NDVI index,
3. prints where each output product landed, using the reference's
   "Where the outputs land" folder rules.
```

### 2. कैप्चर निर्देशिका को बैच-वॉच करें

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (sections
"Quickstart" and "Post-Run Summary & Hints"). Write a Python script that
watches <path/to/captures> for new flight subfolders and runs
chloros_sdk.process_folder() with indices=["NDVI"] on each new one.
After each run, print every hint from result["summary"]["hints"] and treat
a run with zero image products as a failure for that folder.
```

### 3. एक LATTICE एरे कनेक्ट करें और कैप्चर करें

```

Read https://mapir.gitbook.io/chloros/reference/sdk-reference.md (section
"connect_array"). Write a Python script that connects my LATTICE cameras
with serials <213800234, 214000533, ...> as one synchronized array, captures
a reflectance image set into <output/> every 10 seconds for one hour, and
disconnects cleanly when done (use the context-manager form).
```

### 4. DAQ लाइट-सेंसर स्पेक्ट्रा रिकॉर्ड करें

```

Read https://mapir.gitbook.io/chloros/reference/cli-reference.md (section
"chloros-cli daq" — use only the pool-* commands). Write a script that:
1. connects my DAQ-E sensor with `chloros-cli daq pool-connect --eth-host <daq-e-xxxxxx.local>`,
2. lists the pool with `pool-list` to get the sensor id,
3. records a 10-minute calibrated .daq file named "<field-A>" with `pool-record`,
4. disconnects with `pool-disconnect`.
```

{% hint style="warning" %}
कमांड लाइन से DAQ स्क्रिप्टिंग हमेशा `daq pool-*` परिवार (`pool-connect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`, `pool-disconnect`). अन्य `daq` उप-कमांड जिन्हें आपका सहायक बना सकता है, वे शिप किए गए बिल्ड्स में उपलब्ध नहीं हैं और एक त्रुटि के साथ बाहर निकल जाते हैं।
{% endhint %}

## एआई-लिखी स्क्रिप्ट्स Chloros के साथ अच्छी तरह से क्यों काम करती हैं

इनमें से प्रत्येक Chloros 1.2.0 का एक वास्तविक, सत्यापित व्यवहार है — वे मशीन-लिखी ऑटोमेशन के क्लासिक विफलता मोड को हटाते हैं:

* **कोई सेटअप डांस नहीं।**SDK के स्मार्ट-कनेक्ट हेल्पर्स (`connect_camera`, `connect_array`, `connect_daq_sensor`) और प्रोसेसिंग एंट्री पॉइंट्स (`ChlorosLocal`, `process_folder`)**स्थानीय बैकएंड को स्वचालित रूप से शुरू करते हैं**। एक उत्पन्न स्क्रिप्ट के लिए GUI खोलने या मैन्युअल रूप से शुरू किए गए सर्वर की आवश्यकता नहीं होती है — इसके लिए केवल डेस्कटॉप/CLI पैकेज स्थापित होना चाहिए।
* **पूरा पाइपलाइन एक ही कॉल है।** `chloros_sdk.process_folder("path", indices=["NDVI"])` इम्पोर्ट → कैलिब्रेशन → रिफ्लेक्टेंस → इंडेक्स एक्सपोर्ट को शुरू से अंत तक चलाता है। कम सतह क्षेत्र, एक जेनरेट की गई स्क्रिप्ट के गलत होने के कम अवसर।
* **शून्य-आउटपुट रन स्व-निदान करते हैं।** `process()` के बाद, रन का सारांश परिणाम से जुड़ा होता है, और प्रत्येक प्रसंस्करण संकेत (जैसे। *क्यों* रन ने कोई आउटपुट नहीं दिया) भी एक त्रुटि संदेश (Python) के रूप में पुनः प्रसारित किया जाता है — ताकि कोई भी स्क्रिप्ट जो कभी भी परिणाम डिक्ट (result dict) की जाँच नहीं करती, वह भी निदान को सतह पर ला सके।
* **CLI जोरदार विफलता देता है।**एक `chloros-cli process` रन जिसने उत्पादों (products) का अनुरोध किया लेकिन कोई नहीं लिखा, वह `Processing finished but wrote no image products.` प्रिंट करता है और**गैर-शून्य (non-zero) पर बाहर निकलता है**, इसलिए शेल स्क्रिप्ट और CI इसे एक साधारण एग्जिट-कोड जांच (exit-code check) से पहचान लेते हैं। सफल रन `Image products written: N` रिपोर्ट करते हैं।

एक विषमता जिसे एक सहायक को पता होनी चाहिए: SDK का `process()` जानबूझकर शून्य-उत्पाद रन पर त्रुटि संदेश नहीं दिखाता है — इसके बजाय यह सारांश/संकेतों के माध्यम से रिपोर्ट करता है। यदि किसी Python पाइपलाइन को खाली रन पर रुकना चाहिए, तो सारांश की जाँच करें (रेसिपी 2 ऐसा ही करती है)।

## सावधानियाँ

* **Chloros+ लॉगिन आवश्यक है।**CLI और SDK के लिए एक**भुगतान किए गए** Chloros+ स्तर की आवश्यकता होती है, जो सर्वर-साइड लागू होता है: लॉग इन नहीं होने पर `401 AUTH_REQUIRED` के साथ और मुफ्त स्तर पर `403 PLAN_UPGRADE_REQUIRED` के साथ अनुरोध विफल हो जाते हैं। उत्पन्न स्क्रिप्ट चलाने से पहले प्रत्येक मशीन पर `chloros-cli login` एक बार चलाएँ। [Chloros+ Login](chloros+-login.md) देखें।
* **कैप्चर कमांड्स वास्तविक हार्डवेयर को चलाते हैं।** `lattice` / `daq` / `project` कमांड्स और SDK सत्र ऑब्जेक्ट्स भौतिक कैमरों और सेंसरों को कनेक्ट, स्ट्रीम और ट्रिगर करते हैं। जनित स्क्रिप्ट की पहली बार चलाने से पहले समीक्षा करें, और हार्डवेयर के पास रहकर इसे चलाएँ।
* **आउटपुट की अचानक जाँच करें।** परिणाम प्रकाशित करने से पहले उत्पाद फ़ोल्डरों और कुछ पिक्सेल मानों की जाँच करें। विशेष रूप से, रिफ्लेक्टेंस TIFFs प्रत्येक स्रोत के अनुसार स्केल किए जाते हैं — `Chloros:PixelScale` XMP टैग पढ़ें (LATTICE: 32768 = 1.0 परावर्तन; Survey3: 65535) विभाजक मानने के बजाय। दोनों संदर्भ पृष्ठ इसे &quot;परावर्तन पिक्सल पढ़ना&quot; के अंतर्गत दस्तावेज़ करते हैं।
* **छोटे-मोटे अड़ंगे जो जेनरेट किए गए कोड में समस्याएँ पैदा करते हैं:**`pool-record`**बैकएंड होस्ट** के फ़ाइल सिस्टम में लिखता है (डिफ़ॉल्ट `~/Documents/DAQ Live View/`); कई नेटवर्क इंटरफ़ेस वाली मशीनों पर, ऑटो-डिस्कवरी की बजाय `daq pool-connect --eth-host <ip-or-hostname>` का उपयोग करें; और जहाँ भी कोई बैकएंड URL दिखाई देता है, वहाँ `http://127.0.0.1:5000` का उपयोग करें (कभी भी `localhost` का नहीं)।
