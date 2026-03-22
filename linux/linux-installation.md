# Linux इंस्टालेशन

Chloros को Linux के लिए `.deb` पैकेज के रूप में वितरित किया जाता है जो CLI और बैकएंड स्थापित करते हैं। Python SDK को पाइप के माध्यम से अलग से स्थापित किया गया है।

***

## Linux amd64 (x86_64)

### सिस्टम आवश्यकताएं

| आवश्यकता | न्यूनतम | अनुशंसित |
| --- | --- | --- |
| **वितरण** | उबंटू 20.04+ / डेबियन 11+ | उबंटू 22.04+ |
| **प्रोसेसर** | x86_64 (इंटेल/एएमडी) | इंटेल कोर i7 या बेहतर |
| **मेमोरी (रैम)** | 8जीबी | 16जीबी या अधिक |
| **ग्राफिक्स कार्ड** | कोई नहीं (सीपीयू प्रोसेसिंग) | 4GB+ VRAM के साथ NVIDIA GPU |
| **भंडारण** | 2 जीबी खाली जगह | 10GB+ खाली जगह के साथ SSD |
| **Python** | Python 3.7+ (SDK के लिए) | Python 3.10+ |

### स्थापना

`.deb` पैकेज डाउनलोड करें और इंस्टॉल करें:

```bash
sudo dpkg -i chloros-amd64.deb
```

स्थापना सत्यापित करें:

```bash
chloros-cli --version
```

***

## Linux आर्म64 (NVIDIA जेटसन)

### सिस्टम आवश्यकताएं

| आवश्यकता | न्यूनतम | अनुशंसित |
| --- | --- | --- |
| **मंच** | जेटपैक 6 के साथ एनवीडिया जेटसन | जेटसन ओरिन एनएक्स 16जीबी या एजीएक्स ओरिन |
| **जेटपैक** | जेटपैक 6.x | नवीनतम जेटपैक 6 |
| **मेमोरी (रैम)** | 8जीबी (साझा जीपीयू/सीपीयू) | 16जीबी+ साझा |
| **भंडारण** | 2 जीबी खाली जगह | NVMe SSD 10GB+ के साथ मुफ़्त |
| **Python** | Python 3.7+ (SDK के लिए) | Python 3.10+ |

### स्थापना

JetPack 6 `.deb` पैकेज डाउनलोड करें और इंस्टॉल करें:

```bash
sudo dpkg -i chloros-arm64-jp6.deb
```

स्थापना सत्यापित करें:

```bash
chloros-cli --version
```

थर्मल प्रबंधन और फ़ील्ड परिनियोजन सहित विस्तृत जेटसन सेटअप के लिए, [NVIDIA जेटसन गाइड] (nvidia-jetson-guide.md) देखें।

***

## Python SDK इंस्टालेशन (सभी Linux)

Python SDK को पाइप के माध्यम से अलग से स्थापित किया गया है और यह amd64 और Arm64 दोनों पर काम करता है:

```bash
pip install chloros-sdk
```

वैकल्पिक प्रगति स्ट्रीमिंग समर्थन शामिल करने के लिए:

```bash
pip install chloros-sdk[progress]
```

SDK सत्यापित करें:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
`.deb` पैकेज Chloros CLI और बैकएंड स्थापित करता है। Python SDK एक अलग पिप पैकेज है जो स्थानीय HTTP API के माध्यम से बैकएंड के साथ संचार करता है।
{% endhint %}

***

## कॉन्फ़िगरेशन निर्देशिकाएँ

Linux पर Chloros [XDG बेस डायरेक्ट्री विशिष्टता](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html) का अनुसरण करता है:

| उद्देश्य | Linux पथ | Windows समतुल्य |
| --- | --- | --- |
| **कॉन्फ़िगरेशन** | `~/.config/chloros/` | `%APPDATA%\Chloros\` |
| **डेटा/प्रोजेक्ट** | `~/.local/share/chloros/` | `%LOCALAPPDATA%\Chloros\` |
| **कैश / क्रेडेंशियल्स** | `~/.cache/chloros/` | `%APPDATA%\Chloros\cache\` |

## बैकएंड निष्पादन योग्य स्थान

`.deb` पैकेज बैकएंड को एक मानक स्थान पर स्थापित करता है। CLI और SDK बैकएंड पथ का स्वतः पता लगाते हैं:

| स्थापना विधि | बैकएंड पथ |
| --- | --- |
| `.deb` पैकेज | `/usr/lib/chloros/chloros-backend` |
| मैनुअल/कस्टम | `/opt/mapir/chloros/backend/chloros-backend` |

आप `--backend-exe` CLI फ़्लैग या `backend_exe` SDK कंस्ट्रक्टर पैरामीटर के साथ बैकएंड पथ को ओवरराइड कर सकते हैं।

***

## पहली बार सेटअप

### 1. अपना लाइसेंस सक्रिय करें

CLI और SDK एक्सेस के लिए Chloros+ लाइसेंस आवश्यक है:

```bash
chloros-cli login your@email.com 'your-password'
```

### 2. अपने लाइसेंस की स्थिति जांचें

```bash
chloros-cli status
```

### 3. अपना पहला डेटासेट संसाधित करें

```bash
chloros-cli process ~/datasets/flight001
```

### 4. सिस्टम डायग्नोस्टिक्स चलाएँ

सत्यापित करें कि आपका सिस्टम सही ढंग से कॉन्फ़िगर किया गया है:

```bash
chloros-cli selftest
```

यह संस्करण, बैकएंड स्टार्टअप, API कनेक्टिविटी और CUDA/GPU उपलब्धता सहित 7 डायग्नोस्टिक जांच चलाता है।

***

## बैश स्क्रिप्टिंग उदाहरण

### एकाधिक डेटासेट संसाधित करें

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    chloros-cli process "$dataset" --format tiff-32
    echo "Done: $(basename "$dataset")"
done
```

### कस्टम सेटिंग्स के साथ प्रक्रिया

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format tiff-32 \
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

### क्रॉन के साथ स्वचालित प्रसंस्करण

नए डेटासेट को स्वचालित रूप से संसाधित करने के लिए अपने crontab (`crontab -e`) में जोड़ें:

```cron
# Process any new datasets at 2 AM daily
0 2 * ** /usr/bin/chloros-cli process /data/incoming --output /data/processed >> /var/log/chloros.log 2>&1
```

### Python SDK उदाहरण

```python
from chloros_sdk import process_folder

# One-line processing
result = process_folder(
    "/home/user/datasets/flight001",
    indices=["NDVI", "NDRE"],
    export_format="TIFF (32-bit, Percent)"
)
```

***

## समस्या निवारण

### CLI इंस्टालेशन के बाद नहीं मिला

यदि `.deb` पैकेज स्थापित करने के बाद `chloros-cli` नहीं मिलता है:

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# If not in PATH, check the installation
dpkg -L chloros-amd64  # or chloros-arm64-jp6

# Reload your shell
source ~/.bashrc
```

### अनुमति नहीं मिली

```bash
# Ensure the binary is executable
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### बैकएंड प्रारंभ होने में विफल

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

### CUDA का पता नहीं चला

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

### गुम साझा पुस्तकालय

```bash
# Install common dependencies
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

***

## Linux पर Chloros अपडेट कर रहा है

अद्यतनों की जाँच करने और उन्हें स्थापित करने के लिए अंतर्निहित अद्यतन कमांड का उपयोग करें:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

***

## अगले कदम

* [एनवीडिया जेटसन गाइड](nvidia-jetson-guide.md) - जेटसन-विशिष्ट अनुकूलन और परिनियोजन
* [CLI : कमांड लाइन](../CLI.md) - पूर्ण CLI कमांड संदर्भ
* [API : Python SDK](../api-python-sdk.md) — पूर्ण SDK संदर्भ
* [डायनामिक कंप्यूट अनुकूलन](../processing-architecture/dynamic-compute-adaptation.md) - कैसे Chloros आपके हार्डवेयर के अनुकूल होता है