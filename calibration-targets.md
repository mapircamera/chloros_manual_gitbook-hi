---
description: Lab-measured panels used to calibrate captured data in post processing
metaLinks:
  alternates:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# कैलिब्रेशन लक्ष्य

MAPIRविभिन्न अनुप्रयोगों की एक श्रृंखला को कवर करने के लिए विभिन्न कैलिब्रेशन लक्ष्य प्रदान करता है। नीचे दिखाए गए कॉम्पैक्ट T4-R50 में 4 पैनल होते हैं, जिनसे 250 - 2,500 nm की प्रकाश परावर्तनता मापी गई है।

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>T4 डिफ्यूज़ संदर्भ लक्ष्यों के निम्नलिखित परावर्तन वक्र हैं, [डेटा यहां डाउनलोड करें](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 परावर्तन :: 250-2500nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR टी4 परावर्तन :: 400-1000nm</p></figcaption></figure>T4P डिफ्यूज़ संदर्भ लक्ष्यों के निम्नलिखित परावर्तन वक्र हैं, [डेटा यहाँ डाउनलोड करें](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 350-2500nm.jpg" alt=""><figcaption><p>MAPIR T4P परावर्तन :: 250-2500nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4P -- 400-1000nm.jpg" alt=""><figcaption><p>MAPIR T4P परावर्तन :: 400-1000nm</p></figcaption></figure>रिफ्लेक्टेंस ग्राफ़ को देखकर आप देख सकते हैं कि मान तरंगदैर्घ्य (x-अक्ष) बनाम रिफ्लेक्टेंस प्रतिशत (y-अक्ष) हैं। जब हम कैलिब्रेशन लक्ष्य की एक छवि कैप्चर करते हैं, तो हम पिक्सेल मान और रिफ्लेक्टेंस प्रतिशत के बीच एक संबंध बनाते हैं, उस स्पेक्ट्रम के भीतर जिसके प्रति कैमरों के प्रत्येक सेंसर बैंड संवेदनशील होते हैं।

इसका मतलब है कि हमारे कैमरों से ली गई हर छवि के साथ, आप हमारे रिफ्लेक्टेंस टारगेट्स की एक तस्वीर का उपयोग कर सकते हैं, जैसे कि [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) या [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125) का उपयोग करके प्रतिबिंबों को प्रतिबिंबता के लिए कैलिब्रेट कर सकते हैं। एक बार कैलिब्रेट हो जाने पर, छवि में प्रत्येक पिक्सेल प्रतिबिंबता प्रतिशत के बराबर होता है।

**Survey3** आउटपुट के लिए, यदि आपChloros

में कैलिब्रेटेड इमेज को सामान्य JPG याTIFF

के रूप में आउटपुट करते हैं, तो परावर्तन प्रतिशत की गणना पिक्सेल मान को इमेज फॉर्मेट की बिट गहराई से विभाजित करके की जाती है। इसलिए JPG के लिए 255 से विभाजित करें, औरTIFF

के लिए 65,535 से विभाजित करें। आपChloros

में PERCENT फॉर्मेट आउटपुट का भी चयन कर सकते हैं, और फिर प्रत्येक पिक्सेल का मान 0.0 से 1.0 (0% से 100% परावर्तन) तक होगा। बस यह ध्यान रखें कि कुछ इमेज एप्लिकेशन प्रतिशत (फ्लोटिंग पॉइंट) इमेज को स्वीकार नहीं कर सकते, और ये आकार में बड़ी होती हैं।

{% hint style="info" %}
**LATTICE रिफ्लेक्टेंस एक अलग पिक्सेल स्केल का उपयोग करता है।** LATTICE रिफ्लेक्टेंस को DN 32768 = 100% रिफ्लेक्टेंस (65535 नहीं) के साथ संग्रहीत किया जाता है, और हर फ़ाइल में एक XMP `Chloros:PixelScale` टैग होता है जो उसके स्केल को बताता है। एक स्थिरांक मानने के बजाय टैग को पढ़ें और उससे विभाजित करें — [आउटपुट इमेज फ़ॉर्मेट्स](output-image-formats.md) देखें।
{% endhint %}

## LATTICE कैमरों के साथ कैलिब्रेशन टार्गेट

LATTICE कैमरों के साथ परावर्तन के लिए एक कैलिब्रेशन टार्गेट **वैकल्पिक** है: इसके बजायChloros

परावर्तन का संदर्भ DAQ लाइट सेंसर द्वारा मापे गए डाउनवेलिंग इरैडियंस (ρ = π·L/E) से ले सकता है। रेफरेंस को रिफ्लेक्टेंस-सोर्स सेटिंग के साथ चुना जाता है (GUI में प्रोजेक्ट सेटिंग्स;CLI

में `--reflectance-source`;SDK

में `reflectance_source`):

| मान | व्यवहार |
| --- | --- |
| `auto` *(डिफ़ॉल्ट)* | एक QA-पासिंग इन-फ्रेम लक्ष्य **परम संदर्भ** है; जब कोई लक्ष्य मौजूद नहीं होता है या QA विफल हो जाता है, तोChloros

DAQ डाउनवेलिंग विभाजन पर वापस चला जाता है। |
| `target` | केवल-लक्ष्य सख्त — कोई DAQ प्रतिस्थापन नहीं। |
| `daq` | DAQ-प्राधिकृत — डाउनवेलिंग माप हमेशा संदर्भ होता है। |

LATTICE के लिए अतिरिक्त लक्ष्य व्यवहार:

* **लक्ष्य ज्यामिति** — ArUco-चिह्नित पैनल, स्थिर-ROI पैनल, और स्ट्रिप लक्ष्य सभी समर्थित हैं; ज्यामिति प्रोजेक्ट की लक्ष्य कॉन्फ़िगरेशन से आती है।
* **प्रति-इकाई मापे गए लक्ष्य डेटा** — `--target-reflectance-dir DIR` प्रति-इकाई मापे गए लक्ष्य परावर्तन स्कैन (`<serial>.csv`, जिसे लक्ष्य इकाई के सीरियल/QR द्वारा देखा जाता है) की एक निर्देशिका की ओर इंगित करता है। चूक होने पर,Chloros

नाममात्र के T3/T4P स्पेक्ट्रा पर वापस चला जाता है।
* **टेम्पोरल एंकरिंग** — एक पता लगाया गया लक्ष्य अपने आसपास के फ्रेमों को कैलिब्रेट करता है और लक्ष्य के दृश्यों के बीच स्थिर रहता है।

पूर्ण फ़्लैग अर्थ और उदाहरण [CLI

संदर्भ](reference/cli-reference.md) में हैं (देखें &quot;प्रति-उत्पाद एक्सपोर्ट टॉगल&quot;)।

### F988

&quot;F988 प्रतिबिंबता का कैलिब्रेशन दृश्य-अंदर के प्रतिबिंबता पैनल का उपयोग करके किया जाता है: बैंड DAQ लाइट सेंसर की कैलिब्रेटेड रेंज से परे है, इसलिएChloros

आपके सबसे हालिया पैनल कैप्चर को लागू करता है और पैनल साइटिंग्स के बीच इसे बनाए रखता है।&quot;

यदि F988 को केवल DAQ कैलिब्रेशन के साथ चलाया जाता है, तोChloros

उस बैंड के लिए DAQ-आधारित परावर्तनांक को अस्वीकार कर देता है और कारण बताता है (skip reason `dls-uncalibrated-band-988`); पैनल वर्कफ़्लो समर्थित तरीका है।

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
