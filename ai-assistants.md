# एआई सहायकों के साथ क्लोरोस का उपयोग

यह मैनुअल दो प्रकार के पाठकों के लिए लिखा गया है: मनुष्य, और वे एआई असिस्टेंट जिनके माध्यम से मनुष्य लगातार काम कर रहे हैं। प्रत्येक पेज पर सटीक मान, डिफ़ॉल्ट मान, और कॉपी-पेस्ट करने योग्य कमांड दिए गए हैं ताकि कोई असिस्टेंट (क्लाउड, ChatGPT, कोपायलट, एक कोडिंग एजेंट, …) पहली ही कोशिश में काम करने वाला Chloros ऑटोमेशन लिख सके।

Chloros संस्करण: **

1.3.0**। CLI/SDK प्लेटफ़ॉर्म: Windows 10/11 x64 और Linux (x86\_64 / Jetson aarch64)।

## अपने सहायक को क्या दें

| संसाधन | URL | यह किस लिए है |
| --- | --- | --- |
| **llms.txt** | `https://mapir.gitbook.io/chloros/llms.txt` | इस मैनुअल के हर पेज का मशीन-पठनीय इंडेक्स। |
| **CLI संदर्भ** | `https://mapir.gitbook.io/chloros/reference/cli-reference` | संपूर्ण `chloros-cli` कमांड सतह: हर कमांड, फ़्लैग, डिफ़ॉल्ट, एग्जिट कोड, और आउटपुट-फ़ोल्डर नियम। LLM उपभोग के लिए लिखा गया। |
| **SDK संदर्भ** | `https://mapir.gitbook.io/chloros/reference/sdk-reference` | संपूर्ण `chloros_sdk` Python API: क्लासेस, सिग्नेचर, अपवाद, और कार्य किए गए उदाहरण। LLM उपभोग के लिए लिखा गया। |
| **कोई भी पृष्ठ कच्चे मार्कडाउन के रूप में** | पृष्ठ के URL में `.md` जोड़ें | उदाहरण के लिए `https://mapir.gitbook.io/chloros/reference/sdk-reference.md` पृष्ठ को कच्चे मार्कडाउन के रूप में लौटाता है — किसी संदर्भ विंडो में पेस्ट करने या किसी एजेंट से प्राप्त करने के लिए आदर्श। |

मैनुअल में लिंक: [CLI संदर्भ](reference/cli-reference.md) · [SDK संदर्भ](reference/sdk-reference.md).

{% hint style="info" %}
ये दो संदर्भ पृष्ठ स्वयं-समावेशी हैं: इन्हें में से एक को पढ़ने वाला एक सहायक, एक सही स्क्रिप्ट लिखने के लिए मैनुअल के बाकी हिस्से की आवश्यकता नहीं रखता है।
{% endhint %}

## अपने असिस्टेंट को सीधे Chloros चलाने दें: `chloros-mcp`

उपरोक्त सभी में आपका असिस्टेंट एक स्क्रिप्ट या कमांड *लिखता* है। `chloros-mcp` के साथ, [Model Context Protocol](https://modelcontextprotocol.io) (MCP) बोलने वाला एक सहायक, जैसे कि क्लॉड डेस्कटॉप, क्लॉड कोड या कर्सर, स्वयं Chloros को चलाता है। आप साधारण भाषा में पूछते हैं और यह सर्वर के टूल्स को कॉल करता है:

* **इमेजरी को प्रोसेस करें:** एक प्रोजेक्ट बनाएँ, कैप्चर की गई फ़ाइलों का एक फ़ोल्डर इम्पोर्ट करें, सेटिंग्स चुनें, प्रोसेस करें, और रिपोर्ट करें कि रन ने क्या एक्सपोर्ट किया।
* **हार्डवेयर चलाएँ:** LATTICE कैमरों, ऐरे और DAQ लाइट सेंसर को कनेक्ट करें, पूर्वावलोकन करें, एक्सपोज़र और रंग सेट करें, कैप्चर करें, रिकॉर्ड करें, और फ़र्मवेयर अपडेट करें।

```text
You:  Process the flight in D:\Flights\2026-10-17 to reflectance and NDVI.

You:  Show me what the array sees, then capture reflectance every 10 seconds
      for 5 minutes.
```

सर्वर के टूल्स उन कार्यों को कवर करते हैं जो [PythonSDK](api-python-sdk.md) करता है। यह SDK के साथ इंस्टॉल होता है, इसलिए आमतौर पर इंस्टॉल करने के लिए और कुछ नहीं होता: Windows पर Chloros इंस्टॉलर आपके Python के `Scripts` फ़ोल्डर में `chloros-mcp.exe` डालता है, और Linux `.deb` `chloros-mcp` को `/usr/bin` में रखता है।

**क्लॉड कोड:**

```bash
claude mcp add chloros -- chloros-mcp
```

**क्लॉड डेस्कटॉप:**इसे `claude_desktop_config.json` में जोड़ें (**Settings > Developer > Edit Config**इसे खोलता है), फिर क्लॉड डेस्कटॉप को रीस्टार्ट करें।**Cursor** `~/.cursor/mcp.json` में वही एंट्री लेता है।

```json
{
  "mcpServers": {
    "chloros": {"command": "chloros-mcp"}
  }
}
```

यदि क्लाइंट कहता है कि सर्वर शुरू होने में विफल रहा, तो `chloros-mcp` शायद उस `PATH` पर नहीं है जिसे वह देखता है: इसके बजाय पूरा पथ दें (Windows पर `where chloros-mcp`, Linux पर `which chloros-mcp`)।

आपको उसी कंप्यूटर पर Chloros इंस्टॉल करना होगा और वहाँ, ऐप में या `chloros-cli login` के साथ, किसी भी पेड Chloros+ टियर (कॉपर या उससे ऊपर) पर साइन-इन करना होगा। सर्वर आपके लिए साइन इन नहीं कर सकता।

{% hint style="info" %}
प्रत्येक ऐप, विकल्पों, हर टूल के लिए क्लाइंट सेटअप और एक रन कैसे व्यवहार करता है: [MCP सर्वर (AI असिस्टेंट)](reference/sdk-reference.md#mcp-server-ai-assistants) SDK संदर्भ में।
{% endhint %}

## प्रॉम्प्ट रेसिपी

`<placeholders>` को कॉपी करें, भरें, और अपने असिस्टेंट में पेस्ट करें।

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

## AI-लिखी स्क्रिप्ट्स **Chloros** के साथ अच्छी तरह से क्यों काम करती हैं

इनमें से प्रत्येक **Chloros**

1.3.0 का एक वास्तविक, सत्यापित व्यवहार है — वे मशीन-लिखित ऑटोमेशन के क्लासिक विफलता मोड को हटाते हैं:

* **कोई सेटअप प्रक्रिया नहीं।**SDK के स्मार्ट-कनेक्ट हेल्पर्स (`connect_camera`, `connect_array`, `connect_daq_sensor`) और प्रोसेसिंग एंट्री पॉइंट्स (`ChlorosLocal`, `process_folder`)**स्थानीय बैकएंड को स्वचालित रूप से शुरू करते हैं**। एक उत्पन्न स्क्रिप्ट को GUI खोलने या मैन्युअल रूप से शुरू किए गए सर्वर की आवश्यकता नहीं होती है — इसे केवल डेस्कटॉप/CLI पैकेज इंस्टॉल करने की आवश्यकता है।
* **पूरा पाइपलाइन एक ही कॉल है।** `chloros_sdk.process_folder("path", indices=["NDVI"])` इम्पोर्ट → कैलिब्रेशन → रिफ्लेक्टेंस → इंडेक्स एक्सपोर्ट को शुरू से अंत तक चलाता है। कम सतह क्षेत्र, एक जेनरेट की गई स्क्रिप्ट के गलत होने के कम अवसर।
* **शून्य-आउटपुट रन स्वयं निदान करते हैं।** `process()` के बाद, रन का सारांश परिणाम से जुड़ा होता है, और प्रत्येक प्रसंस्करण संकेत (जैसे। *क्यों* किसी रन ने कोई आउटपुट नहीं दिया) को भी एक Python `UserWarning` के रूप में फिर से प्रसारित किया जाता है — ताकि कोई भी स्क्रिप्ट जो कभी भी परिणाम dict की जाँच नहीं करती है, वह भी निदान को सामने ला सके।
* **CLI जोरदार तरीके से विफल होता है।**एक `chloros-cli process` रन जिसने उत्पादों का अनुरोध किया लेकिन कोई नहीं लिखा, वह `Processing finished but wrote no image products.` प्रिंट करता है और**गैर-शून्य पर बाहर निकलता है**, इसलिए शेल स्क्रिप्ट और CI इसे एक साधारण एग्जिट-कोड जांच के साथ पहचान लेते हैं। सफल रन `Image products written: N` रिपोर्ट करते हैं, साथ ही जब मास्क लिखे गए थे तो `(plus M clamp masks)` भी। कैप्चर कमांड्स इसी तरह हर उस फ़ाइल का नाम बताते हैं जिसे वे लिख नहीं सके और `1` के साथ बाहर निकलते हैं।
* **सापेक्ष पथ वही माने जाते हैं जिसकी एक स्क्रिप्ट अपेक्षा करती है।**एक स्थानीय बैकएंड के साथ, एक सापेक्ष आउटपुट पथ (CLI में `-o out/`, SDK में `output_dir="out/"`) उस फ़ोल्डर के सापेक्ष हल किया जाता है जहाँ से कमांड या स्क्रिप्ट चलती है।

एक विषमता जिसे एक सहायक को पता होनी चाहिए:**SDK** का `process()` जानबूझकर शून्य-उत्पाद रन पर त्रुटि नहीं दिखाता है — इसके बजाय यह सारांश/संकेतों के माध्यम से रिपोर्ट करता है। यदि एक Python पाइपलाइन को खाली रन पर रुकना चाहिए, तो सारांश (recipe 2 ऐसा करता है) देखें।

## सावधानियाँ

* **Chloros+ लॉगिन आवश्यक है।**CLI और SDK को एक**भुगतान वाले** Chloros+ टियर की आवश्यकता होती है, जो सर्वर-साइड पर लागू होता है: लॉग इन नहीं होने पर अनुरोध `401 AUTH_REQUIRED` के साथ और मुफ्त टियर पर `403 PLAN_UPGRADE_REQUIRED` के साथ विफल हो जाते हैं। उत्पन्न स्क्रिप्ट चलाने से पहले प्रति मशीन एक बार `chloros-cli login` चलाएँ। [Chloros+ लॉगिन](chloros+-login.md) देखें।
* **कैप्चर कमांड्स वास्तविक हार्डवेयर को चलाते हैं।** `lattice` / `daq` / `project` कमांड, SDK सत्र ऑब्जेक्ट और `chloros-mcp` के टूल भौतिक कैमरों और सेंसरों को कनेक्ट, स्ट्रीम और ट्रिगर करते हैं। किसी जनरेट की गई स्क्रिप्ट की उसकी पहली रन से पहले समीक्षा करें, और इसे हार्डवेयर के साथ उपस्थित रहकर चलाएँ।
* **आउटपुट की जांच करें।** परिणाम प्रकाशित करने से पहले उत्पाद फ़ोल्डरों और कुछ पिक्सेल मानों की पुष्टि करें। विशेष रूप से, रिफ्लेक्टेंस TIFFs को प्रति स्रोत स्केल किया जाता है — `Chloros:PixelScale` XMP टैग पढ़ें (LATTICE फ़ाइलें इसे ले जाती हैं: 32768 = 1.0 रिफ्लेक्टेंस; Survey3 फ़ाइलें ऐसा नहीं करती हैं, और विभाजक मानने के बजाय 65535 = 1.0 का उपयोग करती हैं। दोनों संदर्भ पृष्ठ इसे "रीडिंग रिफ्लेक्टेंस पिक्सल्स" के अंतर्गत दस्तावेज़ करते हैं।
* **छोटे-मोटे अड़ंगे जो जेनरेट किए गए कोड को प्रभावित करते हैं:**`pool-record`**बैकएंड होस्ट** के फ़ाइल सिस्टम में लिखता है (डिफ़ॉल्ट `~/Documents/DAQ Live View/`); कई नेटवर्क इंटरफ़ेस वाली मशीनों पर, ऑटो-डिस्कवरी की तुलना में `daq pool-connect --eth-host <ip-or-hostname>` को प्राथमिकता दें; जहाँ भी कोई बैकएंड URL दिखाई देता है, वहाँ `http://127.0.0.1:5000` का उपयोग करें (कभी भी `localhost` का नहीं); एक स्क्रिप्ट जो प्रोजेक्ट के आउटपुट ट्री को चलाती है, उसे `clamp_masks/` फ़ोल्डरों को छोड़ देना चाहिए (उनकी `*_clampmask.tif` फ़ाइलें मास्क हैं, इमेज नहीं — देखें [आउटपुट इमेज फॉर्मैट्स](output-image-formats.md#clamp-masks)); और लंबी पथों को बंद किए हुए Windows पर, प्रोजेक्ट फ़ोल्डरों को छोटा रखें — लगभग 130 वर्णों से गहरा कोई प्रोजेक्ट फ़ोल्डर प्रोसेसिंग शुरू होने से पहले अस्वीकार कर दिया जाता है।
