# NVIDIA जेटसन गाइड

NVIDIA Jetson पर Chloros किनारे पर - फ़ील्ड में, UAV पर और दूरस्थ इंस्टॉलेशन में मल्टीस्पेक्ट्रल इमेज प्रोसेसिंग को सक्षम बनाता है। Chloros स्वचालित रूप से आपके जेटसन मॉडल का पता लगाता है और आपके हार्डवेयर के लिए इसकी प्रसंस्करण रणनीति को अनुकूलित करता है।

***

## समर्थित जेटसन मॉडल

| मॉडल | रैम | प्रसंस्करण रणनीति | अनुशंसित उपयोग |
| ------------------- | -------------- | ---------------------------------------------------------------- | ---------------------------------------------------------------- |
| **जेटसन एजीएक्स ओरिन** | 32-64जीबी साझा | `GPU_PARALLEL` (4 कर्मचारी) | अधिकतम प्रदर्शन, बड़े डेटासेट |
| **जेटसन ओरिन एनएक्स** | 8-16GB साझा | `GPU_PARALLEL` (3 कर्मचारी, 16GB) / `GPU_SINGLE` (8GB) | हवाई और क्षेत्र में तैनाती के लिए प्राथमिक अनुशंसा |
| **जेटसन ओरिन नैनो** | 8GB साझा | `GPU_SINGLE` (1 कर्मचारी) | प्रवेश स्तर की बढ़त गणना |
| **जेटसन नैनो** | 4-8GB साझा | `GPU_SINGLE` (1 कर्मचारी) | प्रवेश-स्तर, स्मृति-विवश |

{% hint style="info" %}
**लीगेसी जेटसन मॉडल** (TX2, TX1, Xavier NX) समर्थित नहीं हो सकते हैं। उपलब्ध GPU मेमोरी और CUDA क्षमताओं के आधार पर प्रदर्शन अलग-अलग होगा।
{% endhint %}

***

## आवश्यकताएं

* **जेटपैक 6.x** (नवीनतम अनुशंसित)
* **NVIDIA CUDA** (जेटपैक के साथ शामिल)
* **Chloros+ लाइसेंस** (CLI/SDK एक्सेस के लिए आवश्यक)

## स्थापना

```bash
# Install the JetPack 6 .deb package
sudo dpkg -i chloros-arm64-jp6.deb

# Verify installation
chloros-cli --version

# Install Python SDK (optional)
pip install chloros-sdk

# Run system diagnostics
chloros-cli selftest
```

सामान्य Linux इंस्टालेशन विवरण के लिए, [Linux इंस्टालेशन](linux-installation.md) देखें।

***

## जेटसन पर डायनामिक कंप्यूट अनुकूलन

Chloros स्वचालित रूप से आपके जेटसन मॉडल का पता लगाता है और इष्टतम प्रसंस्करण रणनीति का चयन करता है। **किसी मैन्युअल ट्यूनिंग की आवश्यकता नहीं है।**

### यह काम किस प्रकार करता है

स्टार्टअप पर, Chloros आपके सिस्टम को प्रोफाइल करता है:

1. **`/proc/device-tree/model` के माध्यम से जेटसन मॉडल का पता लगाता है**

2.**उपलब्ध GPU/साझा मेमोरी पढ़ता है**

3.**एक प्रसंस्करण रणनीति का चयन करता है** (`GPU_PARALLEL`, `GPU_SINGLE`, या `CPU_PARALLEL`)
4. **कर्मचारियों की संख्या, पाइपलाइन प्रकार और मेमोरी आवंटन** स्वचालित रूप से सेट करता है

### प्रति-मॉडल व्यवहार

| जेटसन मॉडल | रणनीति | श्रमिक | पाइपलाइन | समवर्ती |
| -------------------------------- | -------------- | ------- | --------------------------------- | ----------- |
| **जेटसन नैनो 8जीबी** | `GPU_SINGLE` | 1 | `tiled_gpu` (मेमोरी-कुशल) | क्रमबद्ध |
| **जेटसन ओरिन नैनो 8जीबी** | `GPU_SINGLE` | 1 | `tiled_gpu` | क्रमबद्ध |
| **जेटसन ओरिन एनएक्स 8जीबी** | `GPU_SINGLE` | 2 | `tiled_gpu` | क्रमबद्ध |
| **जेटसन ओरिन एनएक्स 16जीबी** | `GPU_PARALLEL` | 3 | `fused_gpu` (पूर्ण GPU पथ) | समवर्ती |
| **जेटसन एजीएक्स ओरिन 32-64जीबी** | `GPU_PARALLEL` | 4 | `fused_gpu` | समवर्ती |

{% hint style="success" %}
**जेटसन ओरिन एनएक्स 16जीबी** एज परिनियोजन के लिए सबसे उपयुक्त स्थान है - यह 3 समवर्ती कर्मचारियों के साथ `GPU_PARALLEL` रणनीति प्राप्त करता है, जो एक कॉम्पैक्ट फॉर्म फैक्टर में वास्तविक समानांतर GPU प्रसंस्करण प्रदान करता है।
{% endhint %}

प्लेटफ़ॉर्म के बीच मुख्य अंतर **मेमोरी** है। 8 जीबी साझा मेमोरी वाले जेटसन नैनो को मेमोरी-कुशल टाइल वाले दृष्टिकोण का उपयोग करके एक समय में एक छवि को संसाधित करना होगा, जबकि 16 जीबी वाला ओरिन एनएक्स उच्च-थ्रूपुट फ़्यूज्ड पाइपलाइन का उपयोग करके जीपीयू के माध्यम से एक साथ 3 छवियों को चला सकता है।

संपूर्ण गणना अनुकूलन संदर्भ के लिए, [डायनामिक कंप्यूट अनुकूलन](../processing-architecture/dynamic-compute-adaptation.md) देखें।

***

## थर्मल प्रबंधन

जेटसन उपकरणों में सीमित थर्मल हेडरूम होता है, खासकर बंद या हवाई तैनाती में। Chloros में स्वचालित थर्मल मॉनिटरिंग और थ्रॉटलिंग शामिल है:

| तापमान | कार्रवाई |
| ------------------- | ------------------------------------------------- |
| **<70°C** | सामान्य संचालन - पूर्ण प्रसंस्करण गति |
| **70°सेल्सियस** (चेतावनी) | बैच का आकार स्वचालित रूप से कम करें |
| **80°C** (गंभीर) | आक्रामक थ्रॉटलिंग - निचली संगामिति |
| **90°C** (बंद) | GPU प्रसंस्करण पूरी तरह से बंद करें - ठंडा होना आवश्यक है |

{% hint style="warning" %}
**निरंतर प्रसंस्करण के लिए पर्याप्त वेंटिलेशन और हीट सिंकिंग सुनिश्चित करें**, विशेष रूप से संलग्न क्षेत्र के बाड़ों या हवाई प्रणालियों में। थर्मल थ्रॉटलिंग हार्डवेयर की सुरक्षा के लिए प्रोसेसिंग थ्रूपुट को कम कर देगी।
{% endhint %}

***

## मेमोरी प्रबंधन

जेटसन डिवाइस **एकीकृत मेमोरी** का उपयोग करते हैं - जीपीयू और सीपीयू समान भौतिक रैम साझा करते हैं। इसका मतलब है कि रिपोर्ट की गई वीआरएएम (उदाहरण के लिए, ओरिन एनएक्स 16 जीबी पर 15.3 जीबी) समर्पित जीपीयू मेमोरी नहीं है; इसे ऑपरेटिंग सिस्टम और अन्य प्रक्रियाओं के साथ साझा किया जाता है।

### अनुशंसाएँ बदलें

बड़े डेटासेट या टेक्सचर अवेयर डिबेयर प्रोसेसिंग के लिए, Chloros स्वैप स्पेस बनाने की सिफारिश कर सकता है:

```bash
# Check current memory and swap
free -h

# Create a swap file (example: 8GB)
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Make persistent across reboots
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

**प्रति छवि मेमोरी अनुमान:**

* मानक डिबेयर: \~10 एमबी प्रति छवि
* टेक्सचर अवेयर डिबेयर: \~15 एमबी प्रति छवि

Chloros स्वचालित रूप से आपके डेटासेट आकार के आधार पर आवश्यक मेमोरी की गणना करता है और यदि स्वैप की अनुशंसा की जाती है तो आपको चेतावनी देता है।

### ओओएम (स्मृति से बाहर) फ़ॉलबैक

यदि प्रसंस्करण के दौरान आउट-ऑफ-मेमोरी स्थिति का पता चलता है:

1. Chloros स्वचालित रूप से GPU कार्यकर्ताओं की संख्या कम कर देता है
2. `fused_gpu` से `tiled_gpu` पाइपलाइन पर वापस आ जाता है (अधिक मेमोरी-कुशल)
3. क्रैश होने के बजाय कम थ्रूपुट पर प्रोसेसिंग जारी रखता है

***

## फ़ील्ड परिनियोजन

### शक्ति संबंधी विचार

| जेटसन मॉडल | विशिष्ट पावर ड्रा | नोट्स |
| ---------------- | ------------------ | ---------------------- |
| जेटसन नैनो | 5-10W | यूएसबी-सी या बैरल जैक |
| जेटसन ओरिन नैनो | 7-15W | डीसी बैरल जैक |
| जेटसन ओरिन एनएक्स | 10-25W | डीसी बैरल जैक |
| जेटसन एजीएक्स ओरिन | 15-60W | यूएसबी-सी पीडी या बैरल जैक |

निरंतर प्रसंस्करण के लिए अपने पावर बजट की योजना बनाएं - जीपीयू-सघन थ्रेड 3 (प्रसंस्करण) के दौरान चरम पावर ड्रॉ होता है।

### भंडारण अनुशंसाएँ

* **एनवीएमई एसएसडी** आर्म64 परिनियोजन के लिए दृढ़ता से अनुशंसित
* एसडी कार्ड प्रसंस्करण के लिए बहुत धीमे हैं - केवल बूट मीडिया के रूप में उपयोग करें
* संसाधित आउटपुट के लिए अपने कच्चे छवि डेटा आकार को 2-3 गुना करने की योजना बनाएं

### SSH के माध्यम से हेडलेस ऑपरेशन

Chloros CLI हेडलेस जेटसन परिनियोजन के लिए आदर्श है:

```bash
# SSH into the Jetson
ssh user@jetson-hostname

# Process a dataset
chloros-cli process /data/datasets/flight001 --format tiff-32

# Monitor export progress
chloros-cli export-status
```

### सिस्टमडी के साथ स्वचालित प्रसंस्करण

स्वचालित प्रसंस्करण के लिए एक सिस्टमडी सेवा बनाएं:

```ini
# /etc/systemd/system/chloros-process.service
[Unit]
Description=Chloros Automated Processing
After=network.target

[Service]
Type=oneshot
User=chloros
ExecStart=/usr/bin/chloros-cli process /data/incoming --output /data/processed
StandardOutput=append:/var/log/chloros-process.log
StandardError=append:/var/log/chloros-process.log

[Install]
WantedBy=multi-user.target
```

निर्धारित प्रसंस्करण के लिए सिस्टमडी टाइमर के साथ युग्मित करें:

```ini
# /etc/systemd/system/chloros-process.timer
[Unit]
Description=Run Chloros Processing Every Hour

[Timer]
OnCalendar=hourly
Persistent=true

[Install]
WantedBy=timers.target
```

```bash
sudo systemctl enable chloros-process.timer
sudo systemctl start chloros-process.timer
```

***

## उदाहरण वर्कफ़्लोज़

### बेसिक जेटसन प्रोसेसिंग

```bash
#!/bin/bash
# Process a drone flight dataset on Jetson
chloros-cli process /data/flights/flight_042 \
    --output /data/processed/flight_042 \
    --format tiff-32 \
    --indices NDVI NDRE GNDVI
```

### जेटसन पर Python SDK

```python
from chloros_sdk import ChlorosLocal

with ChlorosLocal() as chloros:
    chloros.create_project("field_survey_042")
    chloros.import_images("/data/flights/flight_042")
    chloros.configure(
        indices=["NDVI", "NDRE", "GNDVI"],
        export_format="TIFF (32-bit, Percent)",
        reflectance_calibration=True
    )
    chloros.process(mode="parallel")

print("Processing complete!")
```

### बैच प्रोसेसिंग एकाधिक उड़ानें

```bash
#!/bin/bash
# Process all flight datasets in a directory
for flight in /data/flights/*/; do
    name=$(basename "$flight")
    echo "Processing $name..."
    chloros-cli process "$flight" \
        --output "/data/processed/$name" \
        --format tiff-32 \
        --indices NDVI NDRE
    echo "Completed $name"
done
```

***

## फ़ील्ड उपयोग के लिए अनुशंसित जेटसन सिस्टम

फ़ील्ड और हवाई तैनाती के लिए, इन जेटसन ओरिन एनएक्स 16जीबी कैरियर बोर्ड विकल्पों पर विचार करें:

* **एयरबोर्न/ड्रोन**: कंपन रेटिंग (एमआईएल-एसटीडी), हल्के वजन (300 ग्राम से कम), निष्क्रिय शीतलन वाले सिस्टम
**बीहड़ क्षेत्र**: PoE GigE कैमरा कनेक्टिविटी के साथ IP67/IP69K वाटरप्रूफ एनक्लोजर
* **न्यूनतम/बजट**: ऐड-ऑन संलग्नकों के साथ डेवलपर किट

अपने परिनियोजन परिदृश्य के लिए विशिष्ट हार्डवेयर अनुशंसाओं के लिए [MAPIR सपोर्ट](https://www.mapir.camera/community/contact) से संपर्क करें।

***

## अगले कदम

* [Linux इंस्टालेशन](linux-installation.md) — सामान्य Linux इंस्टालेशन विवरण
* [डायनामिक कंप्यूट अनुकूलन](../processing-architecture/dynamic-compute-adaptation.md) - पूर्ण गणना रणनीति संदर्भ
* [प्रसंस्करण पाइपलाइन](../processing-architecture/processing-pipeline.md) - 4-थ्रेड पाइपलाइन को समझना
* [CLI : कमांड लाइन](../CLI.md) - पूर्ण CLI संदर्भ
* [API : Python SDK](../api-python-sdk.md) — पूर्ण SDK संदर्भ