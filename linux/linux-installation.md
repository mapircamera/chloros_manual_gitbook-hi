# Linux इंस्टॉलेशन

Chloros के लिए `.deb` पैकेजों के रूप में Linux वितरित किया जाता है जो CLI, बैकएंड सर्वर और Python SDK (सिस्टम `python3` के लिए, साथ ही वर्चुअलएनवी के लिए एक संस्करण-मिलान वाला व्हील) इंस्टॉल करते हैं।

पैकेज फ़ाइल नामों में संस्करण और आर्किटेक्चर होता है: x86_64 के लिए `chloros_1.3.0_amd64.deb`, और arm64 (NVIDIA Jetson, JetPack 6) के लिए `chloros_1.3.0_arm64_jp6.deb`। नीचे दी गई कमांड में वास्तव में आपके द्वारा डाउनलोड की गई फ़ाइल का नाम डालें।

{% hint style="info" %}
**

1.3.0 में नया: प्रोग्राम अनपैक होकर आते हैं।** बैकएंड और CLI को फ़ोल्डरों के रूप में इंस्टॉल किया जाता है, `/usr/lib/chloros/backend/` और `/usr/lib/chloros/cli/`, एकल सेल्फ-एक्सट्रैक्टिंग फ़ाइलों के बजाय। वे अब हर लॉन्च पर एक अस्थायी फ़ोल्डर में खुद को अनपैक नहीं करते हैं, इसलिए वे बहुत तेज़ी से शुरू होते हैं (x86_64 पर बैकएंड हर बार शुरू होने पर अनपैकिंग में लगभग 100 सेकंड लगा करता था) और अब `/tmp` में कई GB मुक्त स्थान की आवश्यकता नहीं है। amd64 `.deb` भी अपने पूर्व आकार का लगभग आधा है।
{% endhint %}

***

## Linux amd64 (x86_64)

### सिस्टम आवश्यकताएँ

| आवश्यकता | न्यूनतम | अनुशंसित |
| --- | --- | --- |
| **डिस्ट्रीब्यूशन** | उबंटू 22.04 LTS+ / डेबियन 12+ | उबंटू 24.04 LTS |
| **प्रोसेसर** | x86_64 (इंटेल/एएमडी) | इंटेल कोर i7 या बेहतर |
| **मेमोरी (रैम)** | 8GB | 16GB या अधिक |
| **ग्राफ़िक्स कार्ड** | कोई नहीं (CPU प्रोसेसिंग) | 4GB+ VRAM वाला NVIDIA GPU (12GB+ `GPU_PARALLEL` अनलॉक करता है, 7GB+ सिंगल-इमेज पाथ से टेक्सचर अवेयर को ऑफ रखता है) |
| **स्टोरेज** | 2GB खाली जगह | 10GB+ खाली जगह वाली SSD |
| **Python** | Python 3.7+ (SDK के लिए) | Python 3.10+ |

> **उबंटू 20.04 और डेबियन 11 समर्थित नहीं हैं।** `.deb` की निर्भरता सूची उस पर आधारित है जो Chloros बैकएंड वास्तव में लिंक करता है, और इसमें एक
> `libc6`, focal और bullseye द्वारा भेजे गए glibc 2.31 से नया है, इसलिए `apt` इसे बाद में रनटाइम पर विफल होने देने के बजाय सीधे इंस्टॉल होने से मना कर देता है।

### स्थापना

```bash
sudo apt install ./chloros_1.3.0_amd64.deb
```

{% hint style="info" %}
**`apt install ./…` का उपयोग करें, `dpkg -i` का नहीं।** `apt` पैकेज की निर्भरताओं को हल करता है और यह जो अनुशंसा करता है, उसे भी इंस्टॉल करता है: Python SDK की आवश्यकताएँ, `python3-numpy` और `python3-requests`। `dpkg -i` इनमें से कोई भी इंस्टॉल नहीं करता है। CLI और बैकएंड स्व-निहित हैं और उनके बिना चलते हैं, लेकिन `import chloros_sdk` को उनकी आवश्यकता होती है; इंस्टॉल आउटपुट उन सभी का नाम बताता है जो गायब हैं। `./` को रखें, ताकि `apt` उस नाम के पैकेज को खोजने के बजाय फ़ाइल इंस्टॉल कर दे। यह पैकेज `libgl1` को भी घोषित करता है, जिसे बंडल OpenCV लोड करता है और एक न्यूनतम सर्वर इमेज में इसकी कमी हो सकती है।
{% endhint %}

स्थापना सत्यापित करें:

```bash
chloros-cli --version    # prints "Chloros CLI 1.3.0"
```

<!-- SCREENSHOT-NEEDED: Terminal on Ubuntu 22.04 immediately after `sudo apt install ./chloros_1.3.0_amd64.deb`, showing the full postinst output: the "Chloros installed successfully!" banner, the Usage lines, the "Python SDK:" block ("chloros_sdk 1.3.0 for python3" and the "In a virtualenv: pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl" line), any "GPU Acceleration:" detection line, and the closing "Systemd Service (optional): sudo systemctl enable --now chloros-backend@<user>.service" hint -->

***

## Linux arm64 (NVIDIA Jetson)

### सिस्टम आवश्यकताएँ

| आवश्यकता | न्यूनतम | अनुशंसित |
| --- | --- | --- |
| **प्लेटफ़ॉर्म** | NVIDIA Jetson with JetPack 6 | Jetson Orin NX 16GB या AGX Orin |
| **JetPack** | JetPack 6.x | नवीनतम JetPack 6 |
| **मेमोरी (RAM)** | 8GB (साझा GPU/CPU) | 16GB+ साझा (12GB+ समानांतर प्रसंस्करण के लिए सीमा है) |
| **स्टोरेज** | 2GB खाली जगह | 10GB+ खाली जगह के साथ NVMe SSD |
| **Python** | Python 3.7+ (SDK के लिए) | Python 3.10+ |

### इंस्टॉलेशन

```bash
sudo apt install ./chloros_1.3.0_arm64_jp6.deb
chloros-cli --version
```

amd64 `.deb` (Python SDK, रिसीव बफ़र्स और बैकएंड सेवाएँ शामिल हैं) के समान लेआउट, **JetPack 6** (L4T R36) पर Jetson Orin / Orin NX / Orin Nano के लिए। इसका PyTorch, Orin के लिए JetPack 6 का CUDA बिल्ड है, इसलिए GPU प्रोसेसिंग, जिसमें Texture Aware debayer भी शामिल है, Jetson के GPU पर चलती है। यह पैकेज स्वयं CUDA को शामिल नहीं करता है: यह उन CUDA और cuDNN लाइब्रेरियों का उपयोग करता है जिन्हें JetPack 6 इंस्टॉल करता है (उन छवियों पर `sudo apt install nvidia-jetpack` जिनमें वे मौजूद नहीं हैं)। जहाँ कोई CUDA GPU उपयोगी नहीं है, वहाँ प्रोसेसिंग CPU पर चलती है और `--debayer texture-aware` स्टैंडर्ड डेबयेर का उपयोग करता है (बैकएंड एक `[DEBAYER] … needs a CUDA GPU …` लाइन लॉग करता है जो कारण बताती है). Jetson की मेमोरी, थर्मल, और फील्ड-डिप्लॉयमेंट व्यवहार के लिए, [NVIDIA Jetson गाइड](nvidia-jetson-guide.md) देखें।***

## Python SDK इंस्टॉलेशन (सभी Linux)**`.deb` आपके लिए SDK इंस्टॉल करता है।** दोनों पैकेज (amd64 और Jetson) सिस्टम `python3` के लिए इसे इंस्टॉल करते हैं, इसलिए `import chloros_sdk` इंस्टॉल होने के तुरंत बाद, बिना pip और बिना नेटवर्क के काम करता है। इसके पैकेज (`chloros_sdk`, `lattice_sdk`, `lattice_cam`, `arena_api` और प्रोजेक्ट लोडर्स) `/usr/lib/python3/dist-packages` में जाते हैं, जो पैकेज के स्वामित्व में है: एक अपग्रेड उन्हें बदल देता है, और पैकेज को हटाने से वे हट जाते हैं। संस्करण हमेशा आपके द्वारा इंस्टॉल किए गए CLI और बैकएंड से मेल खाता है।

SDK की अपनी आवश्यकताएँ, `python3-numpy` और `python3-requests`, **अनुशंसित हैं, आवश्यक नहीं** पैकेज द्वारा: `sudo apt install ./chloros_<version>_<arch>.deb` उन्हें इंस्टॉल करता है, `dpkg -i` नहीं करता। इंस्टॉल आउटपुट रिपोर्ट करता है कि `python3` किस SDK संस्करण को इम्पोर्ट करता है, कोई भी आवश्यकता जो गायब है, और `python3` के पाथ पर कहीं भी कोई `chloros_sdk` जो पैकेज की कॉपी को ओवरराइड करता हो (जैसे कि `pip install --user` के साथ पहले इंस्टॉल किया गया कोई)।**एक वर्चुअलएन्वायरनमेंट (virtualenv) में**, जो सिस्टम के पैकेज नहीं देखता है, उसमें बंडल किया गया व्हील इंस्टॉल करें (पूरी तरह से ऑफ़लाइन):

```bash
pip install /usr/lib/chloros/sdk/chloros_sdk-*.whl
```

**PyPI से** — केवल उस मशीन के लिए जिसमें कोई Chloros पैकेज स्थापित नहीं है, जिसका SDK एक दूसरी मशीन पर बैकएंड को चलाता है (देखें [रिमोट-बैकएंड मोड](../reference/sdk-reference.md#remote-backend-mode-pip-only-host-via-tunnel)):

```bash
pip install chloros-sdk
```

वैकल्पिक अतिरिक्त, pip इंस्टॉल के लिए (एक वर्चुअलएन्वायरन या PyPI):

| अतिरिक्त | कमांड | जोड़ता है |
| --- | --- | --- |
| `progress` | `pip install chloros-sdk[progress]` | `sseclient-py` लाइव प्रगति स्ट्रीमिंग के लिए |
| `camera` | `pip install chloros-sdk[camera]` | `bleak` BLE (DAQ-M) परिवहन के लिए |

SDK को सत्यापित करें:

```bash
python3 -c "import chloros_sdk; print(chloros_sdk.__version__)"
```

{% hint style="info" %}
Python SDK बैकएंड से बात करता है जिसे `.deb` एक लोकल HTTP API API (`http://127.0.0.1:5000`) पर इंस्टॉल करता है। `ChlorosLocal` और कनेक्ट हेल्पर्स ज़रूरत पड़ने पर इसे स्वचालित रूप से शुरू करते हैं; एक सहेजे गए प्रोजेक्ट का `connect_all()` कभी ऐसा नहीं करता, इसलिए एक हेडलेस मशीन पर बैकएंड को एक [service](#always-on-ptp-for-headless-hosts) के रूप में चलाएँ. हमेशा `localhost` के बजाय शाब्दिक IPv4 पता का उपयोग करें — `localhost`, `::1` को हल कर सकता है और प्रति अनुरोध लगभग दो सेकंड का समय ले सकता है।
{% endhint %}

***

## पहली बार सेटअप

### 1. साइन इन

CLI और SDK एक्सेस के लिए एक सशुल्क Chloros+ टियर (**कॉपर** या उससे ऊपर) की आवश्यकता होती है, जो सर्वर-साइड पर लागू होता है: एक लॉग-आउट किए हुए कॉलर को `401 AUTH_REQUIRED` मिलता है, और एक मुफ्त-टियर (आयरन) कॉलर को `403 PLAN_UPGRADE_REQUIRED` मिलता है।

```bash
chloros-cli login your@email.com 'your-password'
```

क्रेडेंशियल `~/.chloros/user_session.json` में कैश किए जाते हैं।

{% hint style="info" %}
**अपग्रेड आपके लॉगिन को बनाए रखते हैं; हटाने पर यह साफ़ हो जाता है।** मौजूदा इंस्टॉल पर एक नया `.deb` इंस्टॉल करने से (`chloros-cli update` के माध्यम से भी) `~/.chloros/user_session.json` और कैश्ड लाइसेंस बरकरार रहता है, ताकि हेडलेस CLI/SDK जॉब्स काम करते रहें। पैकेज (`sudo apt remove chloros`) को हटाने से मशीन पर हर उपयोगकर्ता के लिए सहेजा गया लॉगिन और लाइसेंस कैश साफ़ हो जाता है, इसलिए एक नए इंस्टॉल के लिए फिर से `chloros-cli login` की आवश्यकता होती है।
{% endhint %}

### 2. अपने लाइसेंस की स्थिति की जाँच करें

```bash
chloros-cli status
```

`chloros-cli status` किसी भी स्तर (फ्री सहित) पर काम करता है, इसलिए आप हमेशा देख सकते हैं कि एक्सेस क्यों उपलब्ध है या उपलब्ध नहीं है।

### 3. सिस्टम डायग्नोस्टिक्स चलाएँ

```bash
chloros-cli selftest
```

सात जाँचें क्रम में चलती हैं, और यदि उनमें से कोई भी विफल हो जाती है तो कमांड शून्य-से-अन्य मान पर बाहर निकलती है:

| # | जाँच | यह क्या साबित करती है |
| --- | --- | --- |
| 1 | **संस्करण** | CLI अपना संस्करण (`v1.3.0`) रिपोर्ट करता है। |
| 2 | **पोर्ट उपलब्ध** | पोर्ट 5000 खाली है, *या* पहले से ही एक स्वस्थ Chloros बैकएंड द्वारा उत्तर दिया गया है (जो पास के रूप में गिना जाता है)। |
| 3 | **बैकएंड स्टार्टअप** | बैकएंड बाइनरी लॉन्च होती है। |
| 4 | **API परीक्षण (`/api/test`)** | बैकएंड `status: ok` का उत्तर देता है। |
| 5 | **सिस्टम जानकारी** | `/api/system-info` से `GPU: <name>, CUDA: <bool>, PyTorch: <version>` प्रिंट करता है। |
| 6 | **डेनॉइज़र मॉडल** | `*.pth.enc` मॉडल खोजता है (Linux पर: `/usr/lib/chloros/models`). |
| 7 | **CUDA + Denoiser**| टेक्सचर अवेयर वास्तव में उपयोगी है — CUDA**और** कम से कम एक मॉडल फ़ाइल की आवश्यकता है। |

रन `N/7 checks passed` के साथ समाप्त होता है, जो किसी भी विफलता को नाम के अनुसार सूचीबद्ध करता है।

### 4. अपना पहला डेटासेट प्रोसेस करें

```bash
chloros-cli process ~/datasets/flight001
```

***

## फ़ाइलें और निर्देशिकाएँ

### प्रत्येक-उपयोगकर्ता

Chloros अपने क्रेडेंशियल और CLI कॉन्फ़िगरेशन को एक ही क्रॉस-प्लेटफ़ॉर्म निर्देशिका, **`~/.chloros/`** (Windows पर, `%USERPROFILE%\.chloros\`) में रखता है। दो Linux-विशिष्ट कैश इसके बजाय XDG कन्वेंशन का पालन करते हैं — ये सेट होने पर `XDG_CONFIG_HOME` / `XDG_CACHE_HOME` का सम्मान करते हैं।

| पथ | उद्देश्य |
| --- | --- |
| `~/.chloros/user_session.json` | `chloros-cli login` द्वारा लिखा गया लॉगिन सत्र कैश (पैकेज अपग्रेड के दौरान रखा जाता है; पैकेज हटाने पर साफ़ किया जाता है) |
| `~/.chloros/working_directory.txt` | डिफ़ॉल्ट प्रोजेक्ट-फ़ोल्डर ओवरराइड (`chloros-cli set-project-folder` / `get-project-folder` / `reset-project-folder`) |
| `~/.chloros/cli_language.json` | CLI भाषा प्राथमिकता (`chloros-cli language <code>`) |
| `~/.chloros/user.json` | Windows GUI के साथ साझा की गई भाषा सेटिंग — यहाँ `language`, `cli_language.json` पर प्राथमिकता रखता है |
| `~/.chloros/update_cache.json` | Linux/Jetson स्टार्टअप अपडेट चेक के लिए एक घंटे का कैश |
| `~/.chloros/backend.log` | बैकएंड लॉग जब बैकएंड को CLI द्वारा लॉन्च किया गया था |
| `~/.chloros/camera_cal/<serial>/<bundle_sha>/` | कैश किए गए प्रति-कैमरा LATTICE कैलिब्रेशन पैक, सीरियल और बंडल हैश द्वारा की गई |
| `~/.chloros/daq_cap_profiles/<u\|m\|e>/<cap_id>.json` | DAQ कैप-करेक्शन प्रोफाइल के लिए वैकल्पिक उपयोगकर्ता ओवरराइड |
| `~/.config/chloros/system_config.json` | डायनामिक कम्प्यूट एडैप्शन से कैश की गई हार्डवेयर प्रोफ़ाइल — ताज़ा हार्डवेयर डिटेक्शन के लिए इसे हटाएँ |
| `~/.cache/chloros/logs/backend_<YYYYMMDD_HHMMSS>.log` | बैकएंड सर्वर लॉग, हर लॉन्च के लिए एक फ़ाइल |
| `~/Documents/MAPIR/Chloros Projects/` | जब कोई ओवरराइड सेट नहीं होता है तो डिफ़ॉल्ट प्रोजेक्ट फ़ोल्डर |

### सिस्टम-व्यापी

| पथ | उद्देश्य |
| --- | --- |
| `/usr/bin/chloros-cli` | रैपर स्क्रिप्ट — बंडल की गई नेटिव लाइब्रेरियों के लिए `LD_LIBRARY_PATH` सेट करती है (और जब वह फ़ोल्डर मौजूद हो तो `TMPDIR` से `/mnt/ssd/tmp` तक, फिर वास्तविक बाइनरी को एक्ज़ीक्यूट करता है |
| `/usr/bin/chloros-backend` | रैपर स्क्रिप्ट — बैकएंड के लिए भी यही, जो इसे एक इंस्टॉल किए गए प्रोडक्शन बिल्ड के रूप में चिह्नित करता है |
| `/usr/lib/chloros/cli/`, `/usr/lib/chloros/backend/` | संकलित CLI और बैकएंड, अनपैक किए बिना इंस्टॉल किए गए (1.3.0 में नया); ये प्रोग्राम `cli/chloros-cli` और `backend/chloros-backend` हैं |
| `/usr/lib/chloros/chloros-cli`, `/usr/lib/chloros/chloros-backend` | दो-पंक्ति वाली लॉन्चर स्क्रिप्ट जो ऊपर दिए गए वास्तविक प्रोग्रामों को शुरू करती हैं — इन्हें इन पथों पर इसलिए रखा गया है क्योंकि SDK, CLI और पुरानी स्क्रिप्ट इन्हें यहाँ खोजती हैं |
| `/usr/lib/chloros/arena_runtime/` | LATTICE कैमरों द्वारा आवश्यक Arena SDK रनटाइम |
| `/usr/lib/chloros/models/*.pth.enc` | टेक्सचर अवेयर डेबयेर द्वारा उपयोग किए जाने वाले एन्क्रिप्टेड डिनॉइज़र मॉडल |
| `/usr/lib/python3/dist-packages/chloros_sdk/` (और `lattice_sdk`, `lattice_cam`, `arena_api`, प्रोजेक्ट लोडर) | सिस्टम `python3` के लिए इंस्टॉल किया गया Python SDK, और पैकेज |
| `/usr/lib/chloros/sdk/chloros_sdk-*.whl` | के स्वामित्व में |
| `/usr/lib/chloros/exiftool` | बंडल किया गया ExifTool लॉन्चर (सिस्टम exiftool के अस्तित्व में न होने पर केवल `/usr/local/bin/exiftool` से सिमलिंक किया गया)
| `/usr/lib/chloros/exiftool` | बंडल किया गया ExifTool लॉन्चर (सिस्टम `/usr/local/bin/exiftool` का सिमलिंक केवल तभी होता है जब कोई सिस्टम exiftool मौजूद नहीं होता). 1.3.0 से, ExifTool स्क्रिप्ट `/usr/lib/chloros/exiftool-dist/` में अपनी खुद की पर्ल लाइब्रेरी के साथ आती है, इसलिए यह सिस्टम `libimage-exiftool-perl` पैकेज के बिना चलती है |
| `/etc/chloros/update.conf` | `chloros-cli update` द्वारा पढ़ी जाने वाली अपडेट-चैनल कॉन्फ़िगरेशन |
| `/etc/sysctl.d/60-chloros-ptp.conf` | `net.ipv4.ip_unprivileged_port_start = 319` को सेट करता है ताकि बैकएंड रूट के बिना PTP पोर्ट बाइंड कर सके |
| `/etc/sysctl.d/60-chloros-gige.conf` | कर्नेल के सॉकेट रिसीव बफ़र्स को बढ़ाता है (`net.core.rmem_max`, `net.core.rmem_default`) को LATTICE कैमरा स्ट्रीम के लिए 32 MB तक बढ़ाता है, और इंस्टॉल के समय लागू करता है (पहले से ही उच्च सेट किया गया मान जस का तस रहता है)। कर्नेल डिफ़ॉल्ट पर, कैमरा पैकेट कर्नेल में ड्रॉप हो जाते हैं और एक एरे फ्रेम और फ्रेम दर खो देता है; `chloros-cli lattice network` तब बफ़र्स की रिपोर्ट करता है जब वे कम होते हैं। देखें [Linux होस्ट्स — सॉकेट रिसीव बफ़र्स](../reference/cli-reference.md#linux-hosts--socket-receive-buffers) |
| `/etc/ld.so.conf.d/Arena_SDK.conf` | डायनामिक लोडर को `/usr/lib/chloros/arena_runtime` पर इंगित करता है |
| `/lib/udev/rules.d/70-chloros-daq.rules` | लॉग-इन किए हुए उपयोगकर्ता को DAQ-U यूएसबी सीरियल ब्रिज तक पहुंच प्रदान करता है (CP2102N, `10c4:ea60`) |
| `/lib/systemd/system/chloros-backend@.service` | ऑप्ट-इन हमेशा-चालू बैकएंड सेवा जो आपके द्वारा नामित उपयोगकर्ता के रूप में बैकएंड चलाती है (इंस्टॉल की गई, **सक्रिय नहीं**) — जिसे सक्रिय करने के लिए, [हमेशा-चालू PTP] देखें(#always-on-ptp-for-headless-hosts) |
| `/lib/systemd/system/chloros-backend.service` | वही सेवा जो बैकएंड को **रूट**के रूप में चलाती है (इंस्टॉल की गई,**सक्षम नहीं**), इसे इस तरह रखा गया है ताकि इसे सक्षम करने वाली मशीन चलती रहे |
| `/var/log/chloros/` | रूट सेवा का विस्तृत लॉग, जिसे `adm` समूह द्वारा पढ़ा जा सकता है (पैकेज को पर्स (purged) करने पर हटाया जाता है) |
| `/usr/share/applications/chloros-cli.desktop` | "Chloros" एप्लिकेशन-मेनू प्रविष्टि जो एक टर्मिनल खोलती है |

**फ़ायरवॉल (1.3.0 में नया)।** यदि स्थापना के समय `ufw` या `firewalld` सक्रिय है, तो पैकेज UDP पोर्ट `5002` खोलता है (DAQ-E डेटा), `5353` (mDNS डिस्कवरी), `319` और `320` (PTP), और जब पैकेज हटाया जाता है तो उन नियमों को हटा देता है। यदि कोई फ़ायरवॉल स्थापित है लेकिन सक्रिय नहीं है, तो यह कुछ भी नहीं जोड़ता है और बाद में यदि आप इसे सक्षम करते हैं तो अनुमति देने वाले पोर्ट प्रिंट करता है। यह कोई TCP पोर्ट नहीं खोलता है: एक DAQ-E फर्मवेयर अपडेट, जिसमें सेंसर छवि एकत्र करने के लिए TCP पर इस कंप्यूटर से वापस जुड़ता है, `… device never connected back on TCP <port> …` के साथ विफल हो जाता है जबकि एक सक्रिय फ़ायरवॉल उस कनेक्शन को अवरुद्ध कर देता है।

## बैकएंड एक्ज़ीक्यूटेबल स्थान

CLI और SDK बैकएंड का स्वतः पता लगाते हैं:

| घटक | पथ |
| --- | --- |
| CLI | `/usr/bin/chloros-cli` |
| बैकएंड | `/usr/lib/chloros/chloros-backend` (`/usr/lib/chloros/backend/chloros-backend` के लिए एक लॉन्चर) |

`--backend-exe` CLI फ़्लैग या `backend_exe` SDK कंस्ट्रक्टर पैरामीटर के साथ बैकएंड पथ को ओवरराइड करें, और पोर्ट को `--port` (डिफ़ॉल्ट `5000`) के साथ।

{% hint style="info" %}
`CHLOROS_BACKEND_URL` **`lattice`**को इंगित करता है,**`project`**, और**`daq pool-*`** कमांड परिवारों को एक दूरस्थ बैकएंड पर। कोर कमांड (`process`, `login`, `logout`, `status`, `export-status`, `time-sync`, `selftest`) जानबूझकर इसे अनदेखा करते हैं और हमेशा `http://127.0.0.1:<port>` को लक्षित करते हैं।
{% endhint %}

***

## Linux पर LATTICE कैमरे और DAQ लाइट सेंसर

सभी लाइव-हार्डवेयर कमांड परिवार Linux (amd64 और Jetson) पर काम करते हैं:

* **`chloros-cli lattice`** — LATTICE कैमरों और सिंक्रनाइज़ किए गए ऐरे से डिस्कवर, कनेक्ट, कॉन्फ़िगर और कैप्चर करें। `.deb` उनमें आवश्यक Arena SDK रनटाइम को बंडल करता है और इसे डायनामिक लोडर के साथ पंजीकृत करता है।
* **`chloros-cli daq pool-*`** — बैकएंड पूल के माध्यम से DAQ-U/M/E लाइट सेंसर को कनेक्ट करें, कैलिब्रेटेड स्पेक्ट्रा को स्ट्रीम करें, `.daq` फ़ाइलों को रिकॉर्ड करें और कैप प्रोफाइल को स्वैप करें, और DAQ-E के लिए इसकी नेटवर्क सेटिंग्स बदलें, इसे रीस्टार्ट करें और इसके फर्मवेयर को अपडेट करें। `chloros-cli daq discover` उन सेंसरों को स्कैन करता है जिन्हें आप कनेक्ट कर सकते हैं, और `chloros-cli daq --help`, `pool-*` सबकमांड की सूची देता है।
* **`chloros-cli project`** — एक सहेजे गए प्रोजेक्ट (इसके कैमरे, सेंसर और प्रोसेसिंग सेटिंग्स) को हेडलेस रूप से चलाएँ।
* **`chloros-cli time-sync`** — PTP ग्रैंडमास्टर का निरीक्षण करता है जिसे Chloros बैकएंड LATTICE कैमरों और DAQ-E सेंसरों के लिए चलाता है।

```bash
# DAQ-E at a known address — the reliable path on multi-homed hosts
chloros-cli daq pool-connect --eth-host 192.168.2.50

# DAQ-U over USB serial
chloros-cli daq pool-connect --port /dev/ttyUSB0

# What is connected, then the latest calibrated spectrum as JSON
chloros-cli daq pool-list
chloros-cli daq pool-latest --sensor-id daq-e-a1b2c3 --json
```

`--sensor-id` की आवश्यकता `pool-latest` को होती है, `pool-stream`, `pool-record`, और `pool-set-cap`; `pool-list` वर्तमान में पूल में मौजूद आईडी दिखाता है।

{% hint style="info" %}
**`--eth-host` डिस्कवरी को छोड़ देता है।** `pool-connect --eth` mDNS द्वारा एक DAQ-E खोजता है, जिसमें एक ARP फॉलबैक होता है जो एक खाली ARP कैश से काम करता है; `--eth-host` के साथ सेंसर का IP या होस्टनेम पास करके यह सीधे उससे जुड़ता है।
{% endhint %}

**DAQ-U सीरियल परमिशन** इंस्टॉल किए गए udev नियम (`uaccess` + समूह `dialout`) द्वारा संभाला जाता है। यदि पहले से प्लग-इन किया गया कोई सेंसर एक्सेस न हो पा रहा हो, तो नियमों को रीलोड करें या उसे दोबारा प्लग-इन करें:

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger --subsystem-match=tty
```

पूरे कमांड सेट के लिए [CLI संदर्भ](../CLI.md) देखें।

हेडलेस होस्ट के लिए हमेशा चालू रहने वाला PTP

यह पैकेज बैकएंड के लिए दो systemd सेवाएँ इंस्टॉल करता है, जिनमें से कोई भी सक्षम नहीं है। हेडलेस जेटसन या सर्वर पर जहाँ DAQ-E सेंसर और LATTICE कैमरों के लिए PTP टाइम सिंक को लगातार चलता रखना चाहिए, या जहाँ SDK स्क्रिप्ट्स सहेजी गई परियोजनाओं को जोड़ती हैं (`connect_all()` कभी भी बैकएंड शुरू नहीं करता), **अपने उपयोगकर्ता के रूप में** सेवा को सक्षम करें:

```bash
sudo systemctl enable --now chloros-backend@$USER.service    # $USER expands to your user name
systemctl status chloros-backend@$USER.service
```

`chloros-backend@<user>.service` उस उपयोगकर्ता के रूप में बैकएंड चलाता है: इसके द्वारा लिखी गई कैप्चर उनकी होती हैं, इसका विस्तृत लॉग उनके `~/.cache/chloros/logs` में होता है, और यह उनके `chloros-cli login` का उपयोग करता है (`~/.chloros`), इसलिए पहले उस उपयोगकर्ता के रूप में एक बार साइन इन करें। पैकेज इंस्टॉल करने पर यह कमांड आपके उपयोगकर्ता नाम के साथ प्रिंट होती है।

सर्विस के बिना, बैकएंड (और इसके साथ ही PTP ग्रैंडमास्टर) केवल तब तक चलता है जब तक कि इसे कोई शुरू न कर दे — एक `chloros-cli` कमांड या एक SDK स्क्रिप्ट।

यूनिट बैकएंड को `127.0.0.1:5000` से बाँधता है (यूनिट के अंदर `CHLOROS_HOST` / `CHLOROS_PORT` वातावरण सेटिंग्स; `sudo systemctl edit chloros-backend@.service` से ओवरराइड करें) और विफलता के बाद 5 सेकंड के बाद इसे फिर से शुरू करता है।

**रूट सेवा।**`chloros-backend.service` बैकएंड को**रूट** के रूप में चलाता है। यह अभी भी इंस्टॉल रहता है ताकि इसे सक्षम करने वाली मशीन चलती रहे, लेकिन रूट बैकएंड द्वारा लिखी गई हर चीज़ रूट की होती है, इसलिए कोई उपयोगकर्ता अपनी कैप्चर को नहीं मिटा सकता; इसका लॉग `/var/log/chloros` में है, जिसे `adm` समूह पढ़ सकता है। अपने उपयोगकर्ता पर स्विच करने के लिए:

```bash
sudo systemctl disable --now chloros-backend.service
sudo systemctl enable --now chloros-backend@$USER.service
```

**एक सेवा का बैकएंड साझा होता है, इसे कभी भी क्लाइंट द्वारा रोका नहीं जाता है।** `chloros-cli` और SDK स्क्रिप्ट्स इसका उपयोग वैसे ही करती हैं जैसे वे इसे पाती हैं: `process` इस पर चलती है, और पिछली वाली की जगह अपना प्रोजेक्ट लोड करती है। जहाँ अन्यथा CLI बैकएंड को फिर से शुरू कर देगा (`--restart`, या एक `process` जिसके इम्पोर्ट में कुछ भी नहीं मिला), यह यूनिट का नाम बताता है, `sudo systemctl restart <unit>` प्रिंट करता है और `1` से बाहर निकलता है, क्योंकि इस तरह से रुक गया बैकएंड बंद ही रहेगा। इसे `sudo systemctl restart chloros-backend@$USER.service` के साथ स्वयं पुनः आरंभ करें। एक सेवा का बैकएंड कभी भी निष्क्रिय होने पर स्वयं बंद नहीं होता, और `chloros-cli backend stop` इसे अस्वीकार कर देता है, `systemctl stop` कमांड का नाम लेते हुए।**PTP अपने पोर्ट कैसे प्राप्त करता है।** PTP UDP 319/320 का उपयोग करता है, जो दोनों सामान्य 1024 विशेषाधिकार प्राप्त-पोर्ट सीमा से नीचे हैं। पैकेज का `postinst`, `/etc/sysctl.d/60-chloros-ptp.conf` को `net.ipv4.ip_unprivileged_port_start = 319` के साथ लिखता है, जो बैकएंड को आपके उपयोगकर्ता के रूप में चलने के दौरान उन्हें बाइंड करने की अनुमति देता है। 1.3.0 के अनुसार, यह पैकेज बैकएंड पर कोई फ़ाइल क्षमताएँ (`setcap`) सेट नहीं करता है, और `libcap2-bin` अब एक निर्भरता नहीं है: अनपैक्ड बैकएंड पर एक फ़ाइल क्षमता इसे शुरू होने से रोक देगी, इसलिए स्वयं इसे न जोड़ें।***

## Bash स्क्रिप्टिंग उदाहरण

{% hint style="info" %}
**स्क्रिप्टिंग-अनुकूल एग्जिट कोड।**`chloros-cli process` सफलता पर `0` से बाहर निकलता है और**विफलता पर गैर-शून्य — जिसमें एक रन भी शामिल है जिसने इमेज उत्पादों का अनुरोध किया लेकिन कोई नहीं लिखा** (यह `Processing finished but wrote no image products.` प्रिंट करता है और प्रोजेक्ट फ़ोल्डर और सामान्य कारणों का नाम बताता है)। सफल रन यह रिपोर्ट करते हैं कि कितने इमेज उत्पाद लिखे गए थे (`Image products written: N`, साथ ही जब मास्क लिखे गए थे तो `(plus M clamp masks)`). एग्जिट कोड: `0` सफलता, `1` विफलता, `2` तर्क त्रुटि, `130` बाधित।
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
    --indices NDVI GNDVI OSAVI \
    --debayer texture-aware \
    --no-vignette
```

वैध `--format` मान ठीक चार हैं, और उनमें रिक्त स्थान होते हैं — हमेशा उन्हें उद्धरण चिह्न में रखें:

| `--format` मान | आउटपुट फ़ोल्डर |
| --- | --- |
| `TIFF (16-bit)` *(डिफ़ॉल्ट)* | `tiff16` |
| `TIFF (8-bit)` | `tiff8` |
| `TIFF (32-bit, Percent)` | `tiff32` |
| `PNG (8-bit)` | `png8` |
| `JPG (8-bit)` | `jpg8` |

`--debayer`, `standard` (डिफ़ॉल्ट) या `texture-aware` (Chloros+) को स्वीकार करता है।

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
    indices=["NDVI", "OSAVI"],
    export_format="TIFF (32-bit, Percent)"
)
```

***

## समस्या निवारण

### इंस्टॉलेशन के बाद CLI नहीं मिला

```bash
# Check if the binary exists
which chloros-cli
ls -la /usr/bin/chloros-cli

# List everything the package installed
dpkg -L chloros

# Reload your shell
source ~/.bashrc
```

### अनुमति अस्वीकृत

```bash
sudo chmod +x /usr/bin/chloros-cli
sudo chmod +x /usr/lib/chloros/chloros-backend /usr/lib/chloros/backend/chloros-backend
```

### फ़ाइल क्षमताओं को जोड़ने के बाद बैकएंड शुरू नहीं होगा

`setcap` को बैकएंड पर न चलाएँ। 1.3.0 से, पैकेज कोई फ़ाइल क्षमताएँ सेट नहीं करता है, और PTP के पोर्ट 319/320 [How PTP gets its ports](#always-on-ptp-for-headless-hosts) के अंतर्गत वर्णित `sysctl` सेटिंग से आते हैं। एक फ़ाइल कैपेबिलिटी अनपैक्ड बैकएंड को एक सुरक्षित-कार्यकारी मोड में डाल देती है जिसमें यह अपनी लाइब्रेरीज़ नहीं ढूंढ पाता है, इसलिए यह शुरू नहीं होता है। यदि आपने कोई जोड़ा है, तो उसे हटा दें:

```bash
sudo setcap -r /usr/lib/chloros/backend/chloros-backend
```

### PTP शुरू नहीं होगा / पोर्ट 319 बाइंड नहीं कर सकता

पक्का करें कि अनप्राivileged-पोर्ट फ़्लोर को कम किया गया था, और यदि ऐसा नहीं था तो वर्तमान बूट के लिए इसे फिर से लागू करें:

```bash
sysctl net.ipv4.ip_unprivileged_port_start     # expect 319
sudo sysctl -w net.ipv4.ip_unprivileged_port_start=319
```

फिर ग्रैंडमास्टर की जाँच करें:

```bash
chloros-cli time-sync status
chloros-cli time-sync peers
```

### `lattice` कमांड "LATTICE कैमरा ड्राइवर नहीं मिले" के साथ विफल हो जाते हैं

बैकएंड, LATTICE कैमरा रनटाइम को लोड नहीं कर सका जिसके साथ यह इंस्टॉल होता है। हर `lattice` कमांड बैकएंड पर चलता है; CLI में स्वयं कोई कैमरा ड्राइवर नहीं होते हैं। Chloros को फिर से इंस्टॉल करें (`sudo apt install --reinstall ./chloros_1.3.0_<arch>.deb`): `.deb` बैकएंड के बगल में रनटाइम इंस्टॉल करता है, `/usr/lib/chloros/arena_runtime` में, और इसे डायनामिक लोडर के साथ पंजीकृत करता है। उस पंजीकरण की जाँच करने के लिए:

```bash
cat /etc/ld.so.conf.d/Arena_SDK.conf     # expect /usr/lib/chloros/arena_runtime
ls /usr/lib/chloros/arena_runtime | head
```

### बैकएंड शुरू होने में विफल

```bash
# Check if port 5000 is already in use
lsof -i :5000

# Kill any existing process on port 5000
kill $(lsof -t -i :5000)

# Try starting with a different port
chloros-cli --port 5001 process ~/datasets/flight001
```

विफल लॉन्च के लिए बैकएंड लॉग्स `~/.cache/chloros/logs/` में हैं। यदि पोर्ट 5000 किसी बैकएंड सेवा (`systemctl status chloros-backend@$USER.service`) का है, तो उसकी प्रक्रिया को बंद करने के बजाय सेवा को पुनः आरंभ करें।

### CUDA का पता नहीं चला

```bash
# Check NVIDIA driver installation (x86_64)
nvidia-smi

# Check CUDA availability
nvcc --version

# On Jetson, check the L4T release (JetPack 6 is R36) and that JetPack's CUDA libraries are installed
cat /etc/nv_tegra_release
sudo apt install nvidia-jetpack
```

`chloros-cli selftest` एक ही पंक्ति में यही बात रिपोर्ट करता है: `GPU: <name>, CUDA: <bool>, PyTorch: <version>`.

### साझा लाइब्रेरी अनुपस्थित हैं

```bash
sudo apt-get update
sudo apt-get install -f

# Check for missing libraries (the program itself, not the launcher script)
ldd /usr/lib/chloros/backend/chloros-backend | grep "not found"
```

### एसडी-कार्ड सिस्टम पर धीमी शुरुआत

संस्करण 1.3.0 से, बैकएंड और CLI अनपैक किए हुए इंस्टॉल होते हैं, इसलिए वे अब हर लॉन्च पर खुद को एक अस्थायी निर्देशिका में एक्सट्रेक्ट नहीं करते हैं। जब वह फ़ोल्डर मौजूद होता है तो अस्थायी फ़ाइलें अपने आप `/mnt/ssd/tmp` में जाती हैं; अन्यथा आप उन्हें एक तेज़ फ़ाइलसिस्टम पर इंगित कर सकते हैं:

```bash
export TMPDIR=/mnt/nvme/tmp
```

***

## पर Chloros को अपडेट करना Linux

`update` कमांड केवल Linux/Jetson के लिए है। यह `/etc/chloros/update.conf` पर कॉन्फ़िगर किए गए अपडेट चैनल में प्रकाशित संस्करण की जाँच करता है और मिलान वाले `.deb` को डाउनलोड और इंस्टॉल करने की पेशकश करता है:

```bash
# Check for updates without installing
chloros-cli update --check

# Check for and install updates
chloros-cli update
```

Linux/Jetson पर CLI हर स्टार्टअप पर एक नॉन-ब्लॉकिंग अपडेट चेक भी चलाता है (परिणाम `~/.chloros/update_cache.json` में एक घंटे के लिए कैश किया जाता है) और जब कोई नया संस्करण मौजूद होता है तो `Update available: vX.Y.Z` प्रिंट करता है। आपके सेटिंग्स, प्रोजेक्ट और लॉगिन अपडेट के बाद भी बने रहते हैं।

पैकेज को अपग्रेड करने पर इंस्टॉल होते समय हर बैकएंड सेवा रुक जाती है, इसलिए एक चल रही स्क्रिप्ट अपने कैमरों और सेंसरों को खो देती है, और फिर प्रत्येक सक्षम सेवा को फिर से शुरू करती है (एक जिसे हाथ से शुरू किया गया हो लेकिन कभी सक्षम न किया गया हो, वह रुक जाती है, जैसे रीबूट के बाद)।

## अनइंस्टॉल करना

```bash
sudo apt remove chloros
```

हटाने से हर बैकएंड सेवा रुक जाती है और अक्षम हो जाती है, डिफ़ॉल्ट अनप्राivileged-पोर्ट फ़्लोर (1024) को बहाल करता है, receive-buffer सेटिंग फ़ाइल को हटाता है (चल रही वैल्यूज़ रीबूट तक बनी रहती हैं), बंडल-exiftool symlink, एरिना लोडर कॉन्फ़िगरेशन, पैकेज द्वारा जोड़े गए फ़ायरवॉल नियम और इसने जो Python SDK इंस्टॉल किया है, उन्हें हटाता है, और कैश्ड क्रेडेंशियल्स को साफ़ करता है। आपकी परियोजनाओं और `~/.chloros/` डेटा फ़ाइलों को जस का तस छोड़ दिया जाता है। `sudo apt purge chloros` भी `/var/log/chloros` को हटा देता है।

***

## अगले कदम

* [NVIDIA Jetson गाइड](nvidia-jetson-guide.md) — जेटसन-विशिष्ट अनुकूलन और परिनियोजन
* [CLI : कमांड लाइन](../CLI.md) — CLI गाइड
* [API : Python SDK](../api-python-sdk.md) — SDK गाइड
* [CLI संदर्भ](../reference/cli-reference.md) और [SDK संदर्भ](../reference/sdk-reference.md) — 1.3.0 के लिए विस्तृत कमांड/API सूचियाँ
* [डायनामिक कम्प्यूट एडैप्शन](../processing-architecture/dynamic-compute-adaptation.md) — कि कैसे Chloros आपके हार्डवेयर के अनुसार खुद को अनुकूलित करता है
