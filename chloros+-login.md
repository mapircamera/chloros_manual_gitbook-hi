# Chloros+ लॉगिन

## Chloros और Chloros (ब्राउज़र) लॉगिन

उपयोगकर्ता <img src=”.gitbook/assets/icon_user.JPG” alt=”” data-size=”line”> साइडबार मेनू आपको अपने Chloros+ खाते में लॉग इन करने और अतिरिक्त सुविधाओं को अनलॉक करने की अनुमति देता है।

लॉग इन करने पर आपके खाते का विवरण दिखाया जाएगा:

<figure><img src=".gitbook/assets/user_account.JPG" alt="" width="375"><figcaption></figcaption></figure>

## CLI लॉगिन

CLI प्रोसेसिंग सक्षम करने के लिए अपने Chloros+ क्रेडेंशियल के साथ लॉगिन करें।

**वाक्यविन्यास:**

```bash
chloros-cli login <email> <password>
```

{% hint style="info" %}
**SDK उपयोगकर्ता**: Python SDK कैश्ड क्रेडेंशियल्स को साफ़ करने के लिए एक प्रोग्रामेटिक `logout()` विधि भी प्रदान करता है। विवरण के लिए [Python SDK दस्तावेज़](api-python-sdk.md#logout) देखें।
{% endhint %}

**उदाहरण:**

```powershell
chloros-cli login user@example.com 'MyP@ssw0rd123'
```

{% hint style="warning" %}
**विशेष वर्ण**: `$`, `!`, या रिक्त स्थान जैसे वर्ण वाले पासवर्ड के चारों ओर एकल उद्धरण चिह्नों का उपयोग करें।
{% endhint %}

**आउटपुट:**

<figure><img src='.gitbook/assets/cli login_w.JPG' alt=""><figcaption></figcaption></figure>

### योजना समाप्ति

जीयूआई में योजना की समाप्ति से पता चलता है कि आपका लाइसेंस कब अमान्य हो जाएगा। आवर्ती मासिक सदस्यताओं के लिए समाप्ति माह के अंत में होती है। वार्षिक सदस्यता के लिए सदस्यता शुरू करने के बाद यह एक वर्ष है। लाइसेंस जांच को सत्यापित करने के लिए 30 दिन की छूट अवधि के साथ मासिक इंटरनेट कनेक्शन की आवश्यकता होती है।

### डिवाइस सीमा

प्रत्येक Chloros+ योजना अलग-अलग संख्या में पंजीकृत डिवाइस प्रदान करती है। प्रत्येक डिवाइस जिसमें आप Chloros+ खाते से लॉग इन करते हैं, आपके पंजीकृत डिवाइसों की संख्या में गिना जाएगा। आप अपने MAPIR क्लाउड खाता पृष्ठ पर किसी डिवाइस का नाम बदल सकते हैं और उसे हटा सकते हैं।

<table><thead><tr><th width="168.5999755859375" align="right">Chloros+ प्लान</th><th align="center">COPPER</th><th align="center">BRONZE</th><th align="center">SILVER</th><th align="center">GOLD</th></tr></thead><tbody><tr><td align="right">समर्थित डिवाइस</td><td align="center">2</td><td align="center">2</td><td align="center">5</td><td align="center">10</td></tr></tbody></table>