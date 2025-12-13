---
विवरण: पोस्ट प्रोसेसिंग में कैप्चर किए गए डेटा को कैलिब्रेट करने के लिए लैब-मापा पैनल का उपयोग किया जाता है
मेटालिंक:
  विकल्प:
    - https://app.gitbook.com/s/o044KN3Ws0uIDvOmSkcR/calibration-targets
---

# अंशांकन लक्ष्य

MAPIR अनुप्रयोगों की एक श्रृंखला को कवर करने के लिए विभिन्न अंशांकन लक्ष्य प्रदान करता है। नीचे देखे गए कॉम्पैक्ट T4-R50 में 4 पैनल हैं जिन्हें 250 - 2,500 एनएम तक प्रकाश परावर्तन के लिए मापा गया है।

<figure><img src=".gitbook/assets/t4-r50_2.jpg" alt=""><figcaption><p>MAPIR T4-R50</p></figcaption></figure>

T4 विसरित संदर्भ लक्ष्यों में निम्नलिखित परावर्तन वक्र हैं, [डेटा यहां डाउनलोड करें](https://cdn.shopify.com/s/files/1/0972/5566/files/MAPIR_Diffuse_Reflectance_Standard_Calibration_Target_Data_T4.xlsx?v=1741759157):

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (250-2500nm).png" alt=""><figcaption><p>MAPIR T4 Reflectance :: 250-2500nm</p></figcaption></figure>

<figure><img src=".gitbook/assets/MAPIR Diffuse Reflectance Standard Calibration Target Data T4 (400-1000nm).png" alt=""><figcaption><p>MAPIR T4 Reflectance :: 400-1000nm</p></figcaption></figure>

परावर्तन ग्राफ को देखकर आप देख सकते हैं कि मान तरंग दैर्ध्य (x-अक्ष) बनाम परावर्तन प्रतिशत (y-अक्ष) हैं। जब हम अंशांकन लक्ष्य की एक छवि कैप्चर करते हैं तो हम उस स्पेक्ट्रम के भीतर पिक्सेल मान और परावर्तन प्रतिशत के बीच एक संबंध बनाते हैं, जिसके प्रति कैमरे का प्रत्येक सेंसर बैंड संवेदनशील होता है।

इसका मतलब यह है कि हमारे कैमरे से कैप्चर की गई प्रत्येक छवि के साथ, आप हमारे परावर्तन लक्ष्यों की एक तस्वीर का उपयोग कर सकते हैं, जैसे कि [T4-R50](https://www.mapir.camera/collections/calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t3-r50) या [T4-R125](https://www.mapir.camera/collections/multispectral-reflectance-reference-calibration-targets/products/diffuse-reflectance-standard-calibration-target-package-t4-r125) परावर्तन के लिए छवियों को कैलिब्रेट करने के लिए। एक बार कैलिब्रेट होने पर छवि में प्रत्येक पिक्सेल प्रतिशत परावर्तन के बराबर होता है।

यदि आप क्लोरोस में कैलिब्रेटेड छवियों को विशिष्ट जेपीजी या टीआईएफएफ के रूप में आउटपुट करते हैं तो छवि प्रारूप की बिट गहराई से पिक्सेल मान को विभाजित करके परावर्तन प्रतिशत की गणना की जाती है। तो JPG के लिए 255 से भाग दें, और TIFF के लिए 65,535 से भाग दें। आप क्लोरोस में PERCENT प्रारूप आउटपुट भी चुन सकते हैं, और फिर प्रत्येक पिक्सेल 0.0 से 1.0 (0% से 100% परावर्तन) के प्रतिशत मान तक होगा। बस यह ध्यान रखें कि कुछ छवि एप्लिकेशन प्रतिशत (फ़्लोटिंग पॉइंट) छवियों को स्वीकार नहीं कर सकते हैं, और भंडारण के लिहाज से वे आकार में बड़े हैं।

<div><figure><img src=".gitbook/assets/t3-125.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_2.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure> <figure><img src=".gitbook/assets/t3-125_closed.jpg" alt=""><figcaption><p>T4-R125</p></figcaption></figure></div>
