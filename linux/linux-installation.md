Linux इंस्टॉलेशन

Chloros को Linux के लिए `.deb` पैकेजों के रूप में वितरित किया जाता है जो CLI और बैकएंड सर्वर को इंस्टॉल करते हैं। Python SDK एक अलग pip पैकेज है (जिसे `.deb` के अंदर एक संस्करण-मिलान वाले व्हील के रूप में भी बंडल किया गया है)।

पैकेज फ़ाइल नामों में संस्करण और आर्किटेक्चर होता है: x86_64 के लिए `chloros_1.2.0_amd64.deb`, और JetPack 6 Jetson बिल्ड के लिए `chloros_1.2.0_arm64_jp6.deb`। नीचे दिए गए कमांड में उस फ़ाइल को प्रतिस्थापित करें जिसे आपने वास्तव में डाउनलोड किया है।

***

## Linux amd64 (x86_64)

### सिस्टम आवश्यकताएँ

| आवश्यकता | न्यूनतम | अनुशंसित |
| --- | --- | --- |
| **डिस्ट्रीब्यूशन** | उबंटू 22.04 LTS+ / डेबियन 12+ | उबंटू 24.04 LTS |
| **प्रोसेसर** | x86_64 (इंटेल/एएमडी) | इंटेल कोर i7 या बेहतर |
| **मेमोरी (रैम)** | 8GB | 16GB या अधिक |
| **ग्राफिक्स कार्ड** | कोई नहीं (सीपीयू प्रोसेसिंग) | 4GB+ VRAM वाला NVIDIA GPU (12GB+ `GPU_PARALLEL` अनलॉक करता है, 7GB+ सिंगल-इमेज पाथ से टेक्सचर अवेयर को ऑफ रखता है) |
| **स्टोरेज** | 2GB खाली जगह | 10GB+ खाली जगह वाली SSD |
| **Python** | Python 3.7+ (SDK के लिए) | Python 3.10+ |

> **उबंटू 20.04 और डेबियन 11 समर्थित नहीं हैं।** `.deb` की निर्भरता सूची इस बात से ली गई है कि Chloros बैकएंड वास्तव में किसके साथ लिंक करता है, और इसमें शामिल हैं
> `libc6 (>= 2.34)`। फोकल और बुलज़आई दोनों glibc 2.31 के साथ आते हैं, इसलिए `apt` इसे बाद में रनटाइम पर विफल होने देने के बजाय सीधे इंस्टॉल होने से मना कर देता है।

### इंस्टॉलेशन

```bash
sudo dpkg -i chloros_1.2.0_amd64.deb
sudo apt-get install -f    # pulls the declared dependencies (libibverbs1, libcap2-bin)
```

{% hint style="info" %}
`dpkg -i` निर्भरताएँ हल नहीं करता है। यदि यह गायब पैकेजों की रिपोर्ट करता है, तो `sudo apt-get install -f` (या `sudo apt --fix-broken install`) इंस्टॉलेशन पूरा कर देता है — यह सामान्य प्रक्रिया है, कोई त्रुटि नहीं।
{% endhint %}

स्थापना सत्यापित करें:

```bash
chloros-cli --version    # prints "Chloros CLI 1.2.0"
```



<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo dpkg -i chloros_1.2.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block naming the bundled wheel path under /usr/lib/chloros/sdk/, any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend.service" hint -->***

## Linux arm64 (NVIDIA Jetson)

### सिस्टम आवश्यकताएँ

| आवश्यकता | न्यूनतम | अनुशंसित |
| --- | --- | --- |
| **प्लेटफ़ॉर्म** | JetPack 6 के साथ NVIDIA Jetson | Jetson Orin NX 16GB या AGX Orin |
| **JetPack** | JetPack 6.x | नवीनतम JetPack 6 |
| **मेमोरी (RAM)** | 8GB (साझा GPU/CPU) | 16GB+ साझा (12GB+ समानांतर GPU वर्कर्स के लिए सीमा है) |
| **स्टोरेज** | 2GB खाली जगह | 10GB+ खाली जगह वाली NVMe SSD |
| **Python** | Python 3.7+ (SDK के लिए) | Python 3.10+ |

### स्थापना

```bash
sudo dpkg -i chloros_1.2.0_arm64_jp6.deb
sudo apt-get install -f
chloros-cli --version
```

amd64 `.deb` के समान लेआउट, जिसमें Jetson Orin / Orin NX / Orin Nano के लिए ट्यून किया गया CUDA बिल्ड है। Jetson मेमोरी, थर्मल, और फील्ड-डिप्लॉयमेंट व्यवहार के लिए, [NVIDIA Jetson गाइड](nvidia-jetson-guide.md) देखें।

***

## Python SDK इंस्टॉलेशन (सभी Linux)

SDK बैकएंड के लिए एक शुद्ध-Python HTTP क्लाइंट है, इसलिए एक ही पैकेज amd64 और arm64 पर काम करता है। दो स्रोत:**PyPI से** — प्रकाशित स्थिर रिलीज़:

```bash
pip install chloros-sdk
```

**बंडल किए गए व्हील से** — यह गारंटी देता है कि यह उस CLI/बैकएंड से मेल खाएगा जिसे आपने अभी इंस्टॉल किया है (जब आपका `.deb` PyPI से नया हो तो इसका उपयोग करें):

```bash
pip install --user /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

{% hint style="warning" %}
**PEP 668 वितरण** (उबंटू 23.10+, डेबियन 12+) सिस्टम-व्यापी pip इंस्टॉल्स को अस्वीकार करते हैं। `pip install --user …`, एक वर्चुअल एनवायरनमेंट, या `sudo pip install --break-system-packages …` का उपयोग करें। पैकेज इंस्टॉलर कभी भी आपके सिस्टम में &#x27;SDK&#x27; को स्वचालित रूप से इंस्टॉल नहीं करता है Python — यह विकल्प आपके लिए छोड़ दिया गया है।
{% endhint %}

वैकल्पिक अतिरिक्त:

| अतिरिक्त | कमांड | जोड़ता है |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | `sseclient-py` लाइव प्रगति स्ट्रीमिंग के लिए |
| `camera` | `pip install chloros-sdk[camera]` | `bleak` BLE (DAQ-M) परिवहन के लिए |

SDK को सत्यापित करें:

```bash
python -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
`.deb`, Chloros CLI और बैकएंड इंस्टॉल करता है। Python SDK उस बैकएंड के साथ एक स्थानीय HTTP API (`http://127.0.0.1:5000`) पर संचार करता है और आवश्यकता पड़ने पर इसे स्वचालित रूप से शुरू करता है। हमेशा `localhost` के बजाय वास्तविक IPv4 पते का उपयोग करें। — `localhost`, `::1` को रिज़ॉल्व कर सकता है और प्रति अनुरोध में लगभग दो सेकंड का समय लगता है।
{% endhint %}

***

## पहली बार सेटअप

### 1. साइन इन करें

CLI और SDK तक पहुँच के लिए एक सशुल्क Chloros+ स्तर (**कॉपर** या उससे ऊपर) की आवश्यकता होती है, जो सर्वर-साइड पर लागू होता है: एक लॉग-आउट किए हुए कॉलर को `401 AUTH_REQUIRED` मिलता है, और एक मुफ्त-स्तर (आयरन) वाले कॉलर को `403 PLAN_UPGRADE_REQUIRED` मिलता है।

```bash
chloros-cli login your@email.com 'your-password'
```

प्रमाण-पत्र `~/.chloros/user_session.json` में कैश किए जाते हैं।

{% hint style="warning" %}
**आपको हर इंस्टॉल या अपग्रेड के बाद फिर से लॉग इन करना होगा।** पैकेज की `prerm` स्क्रिप्ट जानबूझकर मशीन पर हर उपयोगकर्ता के लिए `~/.chloros/user_session.json` और कैश किए गए लाइसेंस को साफ़ कर देती है, ताकि एक नया बिल्ड हमेशा एक पुराने कैश पर भरोसा करने के बजाय लाइसेंस को फिर से मान्य करे।
{% endhint %}

### 2. अपने लाइसेंस की स्थिति की जाँच करें

```bash
chloros-cli status
```

`chloros-cli status` किसी भी स्तर (मुफ़्त सहित) पर काम करता है, इसलिए आप हमेशा देख सकते हैं कि पहुँच क्यों उपलब्ध है या उपलब्ध नहीं है।

### 3. सिस्टम डायग्नोस्टिक्स चलाएँ

```bash
chloros-cli selftest
```

सात जाँचें क्रम में चलती हैं, और यदि उनमें से कोई भी विफल हो जाती है तो कमांड शून्य-से-अन्य मान पर बाहर निकलती है:

| # | जाँच | यह क्या साबित करती है |
| --- | --- | --- |
| 1 | **संस्करण** | CLI अपनी संस्करण संख्या (`v1.2.0`) रिपोर्ट करता है। |
| 2 | **पोर्ट उपलब्ध** | पोर्ट 5000 खाली है, *या* एक स्वस्थ Chloros बैकएंड द्वारा पहले से ही उत्तर दिया गया है (जिसे पास माना जाता है)। |
| 3 | **बैकएंड स्टार्टअप** | बैकएंड बाइनरी लॉन्च होती है। |
| 4 | **API परीक्षण (`/api/test`)** | बैकएंड `status: ok` का जवाब देता है। |
| 5 | **सिस्टम जानकारी** | `/api/system-info` से `GPU: <name>, CUDA: <bool>, PyTorch: <version>` प्रिंट करता है। |
| 6 | **डेनॉइज़र मॉडल** | `*.pth.enc` मॉडल खोजता है (Linux पर: `/usr/lib/chloros/models`)। |
| 7 | **क्यूडा + डेनॉइज़र**| टेक्सचर अवेयर वास्तव में उपयोगी है — इसके लिए क्यूडा**और** कम से कम एक मॉडल फ़ाइल की आवश्यकता है। |

रन `N/7 checks passed` के साथ समाप्त होता है, जो नाम के अनुसार किसी भी विफलता को सूचीबद्ध करता है।

### 4. अपना पहला डेटासेट प्रोसेस करें

```bash
chloros-cli process ~/datasets/flight001
```

***

## फ़ाइलें और निर्देशिकाएँ

### प्रति-उपयोगकर्ता

Chlorosअपने क्रेडेंशियल और CLI कॉन्फ़िगरेशन को एक ही क्रॉस-प्लेटफ़ॉर्म डायरेक्टरी, **`~/.chloros/`** (Windows पर, `%USERPROFILE%\.chloros\`) में रखता है। दो Linux-विशिष्ट कैश इसके बजाय XDG कन्वेंशन का पालन करते हैं — ये सेट होने पर `XDG_CONFIG_HOME` / `XDG_CACHE_HOME` का सम्मान करते हैं।

| पथ | उद्देश्य |
| --- | --- |
| `~/.chloros/user_session.json` | `chloros-cli login` द्वारा लिखा गया लॉगिन सत्र कैश (प्रत्येक पैकेज इंस्टॉल/अपग्रेड पर साफ़ किया जाता है) |
| `~/.chloros/working_directory.txt` | डिफ़ॉल्ट प्रोजेक्ट-फ़ोल्डर ओवरराइड (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | CLI भाषा प्राथमिकता (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | Windows GUI के साथ साझा भाषा सेटिंग — यहाँ `language`, `cli_language.json` पर प्राथमिकता लेता है |
| `~/.chloros/update_cache.json` | Linux/Jetson स्टार्टअप अपडेट चेक के लिए एक घंटे का कैश |
| `~/.chloros/backend.log` | बैकएंड लॉग जब बैकएंड को CLI द्वारा लॉन्च किया गया था |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | कैश किए गए प्रति-कैमरा LATTICE कैलिब्रेशन पैक, सीरियल और बंडल हैश द्वारा कीड |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | DAQ कैप-करेक्शन प्रोफाइल के लिए वैकल्पिक उपयोगकर्ता ओवरराइड |
| `~/.config/chloros/system_config.json` | डायनामिक कम्प्यूट एडैप्शन से कैश की गई हार्डवेयर प्रोफाइल — ताज़ा हार्डवेयर डिटेक्शन करने के लिए इसे हटाएँ |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | बैकएंड सर्वर लॉग, प्रत्येक लॉन्च के लिए एक फ़ाइल |
| `~/Chloros Projects/` | जब कोई ओवरराइड सेट नहीं होता है तो डिफ़ॉल्ट प्रोजेक्ट फ़ोल्डर |

### सिस्टम-व्यापी

| पथ | उद्देश्य |
| --- | --- |
| `/usr/bin/chloros-cli` | रैपर स्क्रिप्ट — बंडल की गई नेटिव लाइब्रेरियों के लिए `LD_LIBRARY_PATH` सेट करती है, फिर असली बाइनरी को एक्ज़ीक्यूट करती है |
| `/usr/bin/chloros-backend` | रैपर स्क्रिप्ट — समान, साथ ही `CHLOROS_PRODUCTION=1` ताकि बैकएंड का auth गेट कभी भी चुपचाप स्वयं को अक्षम न कर सके |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | संकलित बाइनरी |
| `/usr/lib/chloros/arena_runtime/` | LATTICE कैमरों द्वारा आवश्यक एरिना SDK रनटाइम |
| `/usr/lib/chloros/models/*.pth.enc` | टेक्सचर अवेयर डेबयेर द्वारा उपयोग किए जाने वाले एन्क्रिप्टेड डिनॉइज़र मॉडल |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | Python SDK व्हील जो बिल्कुल इसी बिल्ड से मेल खाता है |
| `/usr/lib/chloros/exiftool` | बंडल किया गया exiftool (सिस्टम exiftool मौजूद न होने पर ही `/usr/local/bin/exiftool` से सिमलिंक्ड) |
| `/etc/chloros/update.conf` | अपडेट-चैनल कॉन्फ़िगरेशन जिसे `chloros-cli update` द्वारा पढ़ा जाता है |
| `/etc/sysctl.d/60-chloros-ptp.conf` | `net.ipv4.ip_unprivileged_port_start = 319` को सेट करता है ताकि बैकएंड रूट के बिना PTP पोर्ट बाइंड कर सके |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | डायनामिक लोडर को `/usr/lib/chloros/arena_runtime` पर इंगित करता है |
| `/lib/udev/rules.d/70-chloros-daq.rules` | लॉग इन किए हुए उपयोगकर्ता को DAQ-U USB सीरियल ब्रिज (CP2102N, `10c4:ea60`) तक पहुँच प्रदान करता है |
| `/lib/systemd/system/chloros-backend.service` | ऑप्ट-इन हमेशा-चालू बैकएंड सेवा (इंस्टॉल, **सक्षम नहीं**) |
| `/usr/share/applications/chloros-cli.desktop` | &quot;Chloros CLI&quot; एप्लिकेशन-मेनू प्रविष्टि जो एक टर्मिनल खोलती है |

## बैकएंड एक्ज़ीक्यूटेबल का स्थान

CLI और SDK बैकएंड का स्वतः पता लगाते हैं:

| घटक | पथ |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| बैकएंड | `/usr/lib/chloros/chloros-backend` |

`--backend-exe` CLI फ़्लैग या `backend_exe` SDK कंस्ट्रक्टर पैरामीटर के साथ बैकएंड पथ को ओवरराइड करें, और `--port` के साथ पोर्ट को (डिफ़ॉल्ट `5000`).

{% hint style="info" %}
`CHLOROS_BACKEND_URL` **`lattice`**को इंगित करता है,**`project`**, और**`daq pool-*`** कमांड परिवारों को एक रिमोट बैकएंड पर। कोर कमांड (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) जानबूझकर इसे अनदेखा करते हैं और हमेशा `http://127.0.0.1:<port>` को लक्षित करते हैं।
{% endhint %}

***

## Linux पर LATTICE कैमरे और DAQ लाइट सेंसर

सभी लाइव-हार्डवेयर कमांड परिवार Linux (amd64 और Jetson) पर काम करते हैं:

* **`chloros-cli lattice`** — LATTICE कैमरों और सिंक्रनाइज़्ड ऐरे से डिस्कवर, कनेक्ट, कॉन्फ़िगर और कैप्चर करें। `.deb` उनमें आवश्यक Arena SDK रनटाइम को बंडल करता है और इसे डायनामिक लोडर के साथ पंजीकृत करता है।
* **`chloros-cli daq pool-*`** — बैकएंड पूल के माध्यम से DAQ-U/M/E लाइट सेंसर को कनेक्ट करें, कैलिब्रेटेड स्पेक्ट्रा स्ट्रीम करें, और `.daq` फ़ाइलें रिकॉर्ड करें। संकलित CLI केवल `pool-*` परिवार को शिप करता है: `pool-connect`, `pool-disconnect`, `pool-list`, `pool-latest`, `pool-stream`, `pool-record`, `pool-set-cap`.
* **`chloros-cli project`** — एक सहेजे गए प्रोजेक्ट (इसके कैमरे, सेंसर और प्रोसेसिंग सेटिंग्स) को हेडलेस रूप से चलाएँ।
* **`chloros-cli time-sync`** — PTP ग्रैंडमास्टर का निरीक्षण करता है, जो Chloros बैकएंड LATTICE कैमरों और DAQ-E सेंसरों के लिए चलाता है।

```bash
# DAQ-E at a known address — the reliable path on multi-homed hosts
chloros-cli daq pool-connect --eth-host 192.168.2.50

# DAQ-U over USB serial
chloros-cli daq pool-connect --port /dev/ttyUSB0

# What is connected, then the latest calibrated spectrum as JSON
chloros-cli daq pool-list
chloros-cli daq pool-latest --sensor-id daq-e-a1b2c3 --json
```

`--sensor-id` की आवश्यकता `pool-latest`, `pool-stream`, `pool-record`, और `pool-set-cap`; `pool-list` वर्तमान में पूल में मौजूद आईडी दिखाता है।

{% hint style="info" %}
**मल्टी-होम्ड मशीन पर पहले DAQ-E कनेक्ट के लिए `--eth-host` को प्राथमिकता दें।** ऑटो-डिस्कवरी mDNS को ब्राउज़ करती है और ठंडे ARP कैश से सेंसर के इंटरफ़ेस को मिस कर सकती है, इसलिए बूट के बाद पहला `pool-connect --eth` तब भी विफल हो सकता है जब सेंसर पूरी तरह से स्वस्थ हो। सेंसर का IP या होस्टनेम पास करने पर डिस्कवरी पूरी तरह से छोड़ दी जाती है।
{% endhint %}

**DAQ-U सीरियल परमिशन** स्थापित udev नियम (`uaccess` + समूह `dialout`) द्वारा संभाला जाता है। यदि पहले से प्लग-इन किया गया कोई सेंसर पहुंच से बाहर बना रहता है, तो नियमों को रीलोड करें या उसे दोबारा प्लग-इन करें:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

पूरे कमांड सेट के लिए [CLI संदर्भ](../CLI.md) देखें।

### हेडलेस होस्ट्स के लिए ऑलवेज-ऑन PTP

पहली बार इंस्टॉल करने पर systemd यूनिट `chloros-backend.service` उत्पन्न होती है लेकिन **सक्रिय नहीं** होती है। एक हेडलेस जेटसन या सर्वर पर जहाँ DAQ-E सेंसर और LATTICE कैमरों के लिए PTP टाइम सिंक को लगातार चालू रखना चाहिए, उसे सक्रिय करें:

```bash
sudo systemctl enable --now chloros-backend.service
sudo systemctl status chloros-backend.service
```

इसके बिना, PTP केवल तब चलता है जब Chloros बैकएंड चल रहा हो — यानी, एक सक्रिय CLI / SDK सत्र के दौरान।

यह यूनिट बैकएंड को `127.0.0.1:5000` से बाँधता है (यूनिट के अंदर `CHLOROS_HOST` / `CHLOROS_PORT` पर्यावरण सेटिंग्स; `sudo systemctl edit chloros-backend.service` से ओवरराइड करें) और 5 सेकंड के बाद विफलता पर इसे फिर से शुरू करता है।

**PTP अपने पोर्ट कैसे प्राप्त करता है।** PTP UDP 319/320 का उपयोग करता है, जो दोनों सामान्य 1024 विशेषाधिकार प्राप्त-पोर्ट सीमा से नीचे हैं। पैकेज का `postinst`, `/etc/sysctl.d/60-chloros-ptp.conf` को `net.ipv4.ip_unprivileged_port_start = 319` के साथ लिखता है, जो बैकएंड को आपके उपयोगकर्ता के रूप में चलने पर उन्हें बाइंड करने की अनुमति देता है। यह एक अतिरिक्त सुरक्षा उपाय के रूप में बैकएंड बाइनरी पर `setcap cap_net_bind_service,cap_net_raw=+ep` भी लागू करता है — इसीलिए `libcap2-bin` इस पैकेज की एक घोषित निर्भरता है।***

## बाश स्क्रिप्टिंग उदाहरण

{% hint style="info" %}
**स्क्रिप्टिंग-अनुकूल एग्जिट कोड।**`chloros-cli process` सफलता पर `0` से बाहर निकलता है और**विफलता पर गैर-शून्य — जिसमें एक रन भी शामिल है जिसने इमेज उत्पादों का अनुरोध किया लेकिन कोई नहीं लिखा** (यह `Processing finished but wrote no image products.` प्रिंट करता है और प्रोजेक्ट फ़ोल्डर और सामान्य कारणों का नाम बताता है)। सफल रन यह रिपोर्ट करते हैं कि कितने इमेज उत्पाद लिखे गए थे (`Image products written: N`)। एग्जिट कोड: `0` सफलता, `1` विफलता, `2` तर्क त्रुटि, `130` बाधित।
{% endhint %}

### कई डेटासेट संसाधित करें

```bash
#!/bin/bash
for dataset in ~/datasets/2026/*/; do
    echo "Processing $(basename "$dataset")..."
    if chloros-cli process "$dataset" --format "TIFF (32-bit, Percent)"; then
        echo "Done: $(basename "$dataset")"
    else
        echo "FAILED: $(basename "$dataset")" >&2
    fi
done
```

### कस्टम सेटिंग्स के साथ संसाधित करें

```bash
#!/bin/bash
chloros-cli process ~/datasets/field_a \
    --output ~/output/field_a \
    --format "TIFF (32-bit, Percent)" \
    --indices NDVI NDRE GNDVI \
    --debayer texture-aware \
    --no-vignette
```

वैध `--format` मान ठीक चार हैं, और उनमें रिक्त स्थान होते हैं — हमेशा उन्हें उद्धृत करें:

| `--format` मान | आउटपुट फ़ोल्डर |
| --- | --- |
| `TIFF (16-bit)` *(डिफ़ॉल्ट)* | `tiff16` |
| `TIFF (32-bit, Percent)` | `tiff32` |
| `PNG (8-bit)` | `png8` |
| `JPG (8-bit)` | `jpg8` |

`--debayer`, `standard` (डिफ़ॉल्ट) या `texture-aware` (Chloros+) स्वीकार करता है।

### क्रॉन के साथ स्वचालित प्रसंस्करण

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

### स्थापना के बाद CLI नहीं मिला

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# List everything the package installed
dpkg -L chloros

# Reload your shell
source ~/.bashrc
```

### अनुमति अस्वीकार कर दी गई

```bash
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend
```

### इंस्टॉल के दौरान &quot;setcap failed&quot;

`.deb`, `cap_net_bind_service` को `/usr/lib/chloros/chloros-backend` पर लागू करता है ताकि यह रूट के बिना PTP पोर्ट 319/320 को बाइंड कर सके। यदि इंस्टॉल करते समय `libcap2-bin` गायब था तो कॉल को छोड़ दिया जाता है। इसे इंस्टॉल करें और पैकेज को फिर से इंस्टॉल करें:

```bash
sudo apt install libcap2-bin
sudo apt reinstall chloros
```

### PTP शुरू नहीं होगा / पोर्ट 319 बाइंड नहीं कर सकता

पुष्टि करें कि अनप्राivileged-पोर्ट फ़्लोर को कम किया गया था, और यदि ऐसा नहीं था तो वर्तमान बूट के लिए इसे फिर से लागू करें:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

फिर ग्रैंडमास्टर की जाँच करें:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### &quot;LATTICE कैमरा ड्राइवर नहीं मिले&quot;

एरिना SDK रनटाइम रिज़ॉल्व नहीं हो रहा है। पुष्टि करें कि पैकेज द्वारा लिखी गई लोडर कॉन्फ़िगरेशन मौजूद है और रिफ्रेश की गई है:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
sudo ldconfig
ls /usr/lib/chloros/arena_runtime | head
```

### बैकएंड शुरू करने में विफल

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

विफल लॉन्च के लिए बैकएंड लॉग्स `~/.cache/chloros/logs/` में हैं।

### CUDA का पता नहीं चला

```bash
# Check NVIDIA driver installation
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check JetPack version
cat /etc/nv_tegra_release
```

`chloros-cli selftest` एक ही बात एक पंक्ति में रिपोर्ट करता है: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`।

### मिसिंग शेयर्ड लाइब्रेरीज़

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries
ldd /usr/lib/chloros/chloros-backend | grep "not found"
```

### एसडी-कार्ड सिस्टम पर धीमी शुरुआत

कंपाइल की गई बाइनरी हर लॉन्च पर खुद को एक अस्थायी डायरेक्टरी में निकालती हैं। यदि `/mnt/ssd/tmp` मौजूद है, तो Chloros इसका स्वचालित रूप से उपयोग करता है; अन्यथा `TMPDIR` को एक तेज़ फ़ाइल सिस्टम पर सेट करें:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## Linux पर Chloros को अपडेट करना

`update` कमांड केवल Linux/Jetson के लिए है। यह `/etc/chloros/update.conf` पर कॉन्फ़िगर किए गए अपडेट चैनल में प्रकाशित संस्करण की जाँच करता है और मिलान वाले `.deb` को डाउनलोड और इंस्टॉल करने की पेशकश करता है:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

Linux/Jetson पर CLI हर स्टार्टअप पर एक नॉन-ब्लॉकिंग अपडेट चेक भी चलाता है (परिणाम `~/.chloros/update_cache.json` में एक घंटे के लिए कैश किया जाता है) और जब कोई नया संस्करण मौजूद होता है तो `Update available: vX.Y.Z` प्रिंट करता है। आपकी सेटिंग्स और प्रोजेक्ट्स अपडेट के बाद भी बनी रहती हैं; आपको बाद में फिर से साइन इन करना होगा।

## अनइंस्टॉल करना

```bash
sudo apt remove chloros
```

हटाने से `chloros-backend.service` बंद हो जाता है, डिफ़ॉल्ट अनप्राivileged-पोर्ट फ़्लोर (1024) बहाल हो जाता है, बंडल किए गए exiftool symlink और Arena लोडर कॉन्फ़िग को हटा दिया जाता है, और कैश किए गए क्रेडेंशियल साफ़ हो जाते हैं। आपकी परियोजनाएँ और `~/.chloros/` डेटा फ़ाइलें जस की तस रहती हैं।

***

## अगले कदम

* [NVIDIA Jetson गाइड](nvidia-jetson-guide.md) — Jetson-विशिष्ट ऑप्टिमाइज़ेशन और परिनियोजन
* [CLI : कमांड लाइन](../CLI.md) — CLI गाइड
* [API : Python SDK](../api-python-sdk.md) — SDK गाइड
* [CLI संदर्भ](../reference/cli-reference.md) और [SDK संदर्भ](../reference/sdk-reference.md) — 1.2.0 के लिए विस्तृत कमांड/API सूचियाँ
* [डायनामिक कम्प्यूट एडाप्शन](../processing-architecture/dynamic-compute-adaptation.md) — कैसे Chloros आपके हार्डवेयर के अनुसार अनुकूलित होता है
