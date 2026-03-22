---
metaLinks: {}
---

# शुरू करना

<div data-full-width='false'><figure><img src='.gitbook/assets/cloros_logo_transparent.png' alt=""><figcaption></figcaption></figure></div>

Chloros छवियों और अन्य सेंसर डेटा को संसाधित करने के लिए [MAPIR](https://www.mapir.camera) का एक सॉफ्टवेयर एप्लिकेशन है।

***{% hint style="success" %}**Chloros 1.1.0 में नया क्या है**: नेटिव Linux सपोर्ट (amd64 और Arm64), NVIDIA जेटसन एज कंप्यूटिंग, डायनामिक कंप्यूट एडाप्टेशन, 4-थ्रेड प्रोसेसिंग पाइपलाइन, नए CLI कमांड और विकल्प। संपूर्ण चेंजलॉग के लिए [डाउनलोड](download.md) देखें।
{% endhint %}

Chloros 3 एप्लिकेशन मोड में उपलब्ध है:

## Chloros: डेस्कटॉप GUI एप्लिकेशन

सभी सुविधाओं के साथ स्टैंडअलोन अलग विंडो। _केवल विंडोज़._

## [Chloros CLI: कमांड लाइन इंटरफ़ेस](CLI.md)

कमांड-लाइन बैच प्रोसेसिंग। स्वचालन, स्क्रिप्टिंग और हेडलेस ऑपरेशन के लिए बिल्कुल सही। **Windows, Linux amd64, और Linux Arm64 (NVIDIA Jetson)** पर उपलब्ध है। _CLI को एक्सेस करने के लिए Chloros+ लाइसेंस की आवश्यकता होती है।_

## [Chloros API: Python SDK](api-python-sdk.md)

स्वचालन और कस्टम वर्कफ़्लो के लिए प्रोग्रामेटिक Python इंटरफ़ेस। अनुसंधान पाइपलाइनों, मौजूदा Python अनुप्रयोगों के साथ एकीकरण और कस्टम टूल के निर्माण के लिए बिल्कुल सही। `pip install chloros-sdk` के माध्यम से **सभी प्लेटफ़ॉर्म** पर उपलब्ध है। _API को एक्सेस करने के लिए Chloros+ लाइसेंस की आवश्यकता होती है।_***

## समर्थित प्लेटफार्म

| प्लेटफार्म | जीयूआई | CLI | Python SDK |
| --- | --- | --- | --- |
| **Windows 10/11** | हाँ | हाँ | हाँ |
| **Linux amd64 (x86_64)** | नहीं | हाँ | हाँ |
| **Linux Arm64 (NVIDIA Jetson)** | नहीं | हाँ | हाँ |

Linux इंस्टालेशन निर्देशों के लिए, [Linux & Edge Computing](linux/linux-overview.md) अनुभाग देखें।

***

## Chloros+

जबकि Chloros अधिकांश कार्यों के लिए उपयोग करने के लिए निःशुल्क है, आप पाएंगे कि आप और अधिक चाहते हैं। यहीं पर Chloros+ का सशुल्क लाइसेंस आपको लाभ पहुंचा सकता है। Chloros+ लाइसेंस के साथ आप नई सुविधाओं को अनलॉक कर सकते हैं जैसे:

* **मल्टी-थ्रेडेड प्रोसेसिंग**: पाइपलाइन के माध्यम से एक साथ छवियों को संसाधित करके बड़ी परियोजनाओं के लिए छवि प्रसंस्करण को बहुत तेज करता है।
* **जीपीयू (सीयूडीए) एक्सेलेरेशन**: इमेज प्रोसेसिंग पाइपलाइन को और तेज करने के लिए आज के उच्च जीपीयू मेमोरी विकल्पों का लाभ उठाएं। सर्वोत्तम परिणामों के लिए हम 4GB या अधिक VRAM की अनुशंसा करते हैं।
* **Chloros+**[**CLI**](CLI.md)**एक्सेस**: स्वचालित करने और अपने सॉफ़्टवेयर में एकीकृत करने के लिए कमांड लाइन से Chloros+ चलाएं।
* **Chloros+**[**API**](api-python-sdk.md)**पहुंच:** प्रोग्रामेटिक नियंत्रण के लिए Python से Chloros+ चलाएं, जिससे आपके शोध पाइपलाइनों, डेटा विश्लेषण वर्कफ़्लो और कस्टम अनुप्रयोगों के साथ सहज एकीकरण सक्षम हो सके।
* **एकाधिक डिवाइस उपयोग**: प्रत्येक Chloros+ लाइसेंस 2+ डिवाइस को पंजीकृत करने की अनुमति देता है। पंजीकृत उपकरणों को प्रबंधित करने के लिए अपने MAPIR क्लाउड खाते का उपयोग करें। अपने Chloros+ लाइसेंस को अपग्रेड करके अधिक उपकरणों के लिए समर्थन जोड़ें।
* **उन्नत टेक्सचर अवेयर डिबेयर विधि:** एक उच्च गुणवत्ता वाला एज-अवेयर डिबेयर एक एआई/एमएल डीनोइज़िंग मॉडल के साथ संयुक्त है जो लगभग सभी डिबेयरिंग शोर को हटा देता है।&#x20;
* **कस्टम मल्टीस्पेक्ट्रल इंडेक्स फॉर्मूला:** प्रसंस्करण और छवि देखने वाले सैंडबॉक्स दोनों के लिए, Chloros रैस्टर कैलकुलेटर में कस्टम मल्टीस्पेक्ट्रल इंडेक्स दर्ज करें।
* **Linux और एज कंप्यूटिंग:** फील्ड और एज प्रोसेसिंग के लिए NVIDIA Jetson सहित Linux x86\_64 और ARM64 प्लेटफॉर्म पर Chloros चलाएं। [Linux अवलोकन](linux/linux-overview.md) देखें।

<p संरेखित करें = केंद्र साइनअप</a></p>

<figure><img src=".gitbook/assets/plus_prog.JPG" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cloros_grid_zoom.gif" alt=""><figcaption></figcaption></figure>

<figure><img src='gitbook/assets/cloros_grid_mode.gif' alt=""><figcaption></figcaption></figure>

<figure><img src='gitbook/assets/cloros_grid_meta.gif' alt=""><figcaption></figcaption></figure>

<figure><img src='gitbook/assets/cloros_map_markers.gif' alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/cli.JPG" alt=""><figcaption></figcaption></figure>