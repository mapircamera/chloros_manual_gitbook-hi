# एपीआई: पायथन एसडीके

**क्लोरोस पायथन एसडीके** क्लोरोस इमेज प्रोसेसिंग इंजन तक प्रोग्रामेटिक पहुंच प्रदान करता है, जो आपके पायथन अनुप्रयोगों और अनुसंधान पाइपलाइनों के साथ स्वचालन, कस्टम वर्कफ़्लो और निर्बाध एकीकरण को सक्षम करता है।

### प्रमुख विशेषताऐं

* 🐍 **नेटिव पायथन** - इमेज प्रोसेसिंग के लिए स्वच्छ, पायथोनिक एपीआई
* 🔧 **पूर्ण एपीआई एक्सेस** - क्लोरोस प्रसंस्करण पर पूर्ण नियंत्रण
* 🚀 **स्वचालन** - कस्टम बैच प्रोसेसिंग वर्कफ़्लोज़ बनाएं
* 🔗 **एकीकरण** - मौजूदा पायथन अनुप्रयोगों में क्लोरोस एम्बेड करें
* 📊 **अनुसंधान के लिए तैयार** - वैज्ञानिक विश्लेषण पाइपलाइनों के लिए बिल्कुल सही
* ⚡ **समानांतर प्रसंस्करण** - आपके सीपीयू कोर को स्केल करता है (क्लोरोस+)

### आवश्यकताएं

| आवश्यकता | विवरण |
| -------------------- | ------------------------------------------------------------------- |
| **क्लोरोस डेस्कटॉप** | स्थानीय रूप से स्थापित किया जाना चाहिए |
| **लाइसेंस** | क्लोरोस+ ([भुगतान योजना आवश्यक](https://cloud.mapir.camera/pricing)) |
| **ऑपरेटिंग सिस्टम** | विंडोज़ 10/11 (64-बिट) |
| **पायथन** | पायथन 3.7 या उच्चतर |
| **स्मृति** | न्यूनतम 8जीबी रैम (16जीबी अनुशंसित) |
| **इंटरनेट** | लाइसेंस सक्रियण के लिए आवश्यक |

{% संकेत शैली = "चेतावनी" %}
**लाइसेंस आवश्यकता**: पायथन एसडीके को एपीआई एक्सेस के लिए सशुल्क क्लोरोस+ सदस्यता की आवश्यकता होती है। मानक (निःशुल्क) योजनाओं में एपीआई/एसडीके पहुंच नहीं है। अपग्रेड करने के लिए [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) पर जाएं।
{%अंतसंकेत%}

## त्वरित शुरुआत

### स्थापना

पिप के माध्यम से स्थापित करें:

```bash
pip install chloros-sdk
```

{% संकेत शैली = "जानकारी" %}
**पहली बार सेटअप**: एसडीके का उपयोग करने से पहले, क्लोरोस, क्लोरोस (ब्राउज़र) या क्लोरोस सीएलआई खोलकर और अपने क्रेडेंशियल्स के साथ लॉग इन करके अपने क्लोरोस+ लाइसेंस को सक्रिय करें। इसे केवल एक बार ही करना होगा.
{%अंतसंकेत%}

### मूल उपयोग

किसी फ़ोल्डर को केवल कुछ पंक्तियों के साथ संसाधित करें:

```python
from chloros_sdk import process_folder

# One-line processing
results = process_folder("C:\\DroneImages\\Flight001")
```

### पूर्ण नियंत्रण

उन्नत वर्कफ़्लोज़ के लिए:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project
chloros.create_project("MyProject", camera="Survey3N_RGN")

# Import images
chloros.import_images("C:\\DroneImages\\Flight001")

# Configure settings
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE", "GNDVI"]
)

# Process images
chloros.process(mode="parallel", wait=True)
```

***

## इंस्टालेशन गाइड

### पूर्वावश्यकताएँ

एसडीके स्थापित करने से पहले, सुनिश्चित करें कि आपके पास:

1. **क्लोरोस डेस्कटॉप** स्थापित ([डाउनलोड](download.md))
2. **पायथन 3.7+** स्थापित ([python.org](https://www.python.org))
3. **एक्टिव क्लोरोस+ लाइसेंस** ([अपग्रेड](https://cloud.mapir.camera/pricing))

### पिप के माध्यम से स्थापित करें

**मानक स्थापना:**

```bash
pip install chloros-sdk
```

**प्रगति निगरानी समर्थन के साथ:**

```bash
pip install chloros-sdk[progress]
```

**विकास स्थापना:**

```bash
pip install chloros-sdk[dev]
```

### स्थापना सत्यापित करें

परीक्षण करें कि SDK सही ढंग से स्थापित है:

```python
import chloros_sdk
print(f"Chloros SDK version: {chloros_sdk.__version__}")
```

***

## पहली बार सेटअप

### लाइसेंस सक्रियण

एसडीके क्लोरोस, क्लोरोस (ब्राउज़र), और क्लोरोस सीएलआई के समान लाइसेंस का उपयोग करता है। GUI या CLI के माध्यम से एक बार सक्रिय करें:

1. **क्लोरोस या क्लोरोस (ब्राउज़र)** खोलें और उपयोगकर्ता <img src=”.gitbook/assets/icon_user.JPG” alt=”” data-size=”line”> टैब पर लॉगिन करें। या, **सीएलआई** खोलें।
2. अपना क्लोरोस+ क्रेडेंशियल दर्ज करें और लॉग इन करें
3. लाइसेंस स्थानीय रूप से कैश किया जाता है (रीबूट के दौरान बना रहता है)

{%संकेत शैली='सफलता'%}
**वन-टाइम सेटअप**: जीयूआई या सीएलआई के माध्यम से लॉग इन करने के बाद, एसडीके स्वचालित रूप से कैश्ड लाइसेंस का उपयोग करता है। किसी अतिरिक्त प्रमाणीकरण की आवश्यकता नहीं!
{%अंतसंकेत%}

### टेस्ट कनेक्शन

सत्यापित करें कि SDK क्लोरोस से कनेक्ट हो सकता है:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK (auto-starts backend if needed)
chloros = ChlorosLocal()

# Check status
status = chloros.get_status()
print(f"Backend running: {status['running']}")
```

***

## एपीआई संदर्भ

### क्लोरोसलोकल क्लास

स्थानीय क्लोरोस छवि प्रसंस्करण के लिए मुख्य वर्ग।

#### कंस्ट्रक्टर

```python
ChlorosLocal(
    api_url="http://localhost:5000",     # Backend URL
    auto_start_backend=True,             # Auto-start backend if not running
    backend_exe=None,                    # Backend path (auto-detected)
    timeout=30,                          # Request timeout (seconds)
    backend_startup_timeout=60           # Backend startup timeout
)
```

**पैरामीटर:**

| पैरामीटर | प्रकार | डिफ़ॉल्ट | विवरण |
| ------------------------- | ---- | ------------------------- | ------------------------------------- |
| `api_url` | स्ट्र | `"http://localhost:5000"` | स्थानीय क्लोरोस बैकएंड का यूआरएल |
| `ऑटो_स्टार्ट_बैकएंड` | बूल | 'सच' | यदि आवश्यक हो तो स्वचालित रूप से बैकएंड प्रारंभ करें |
| `बैकएंड_एक्सई` | स्ट्र | `कोई नहीं` (स्वतः पता लगाएं) | निष्पादन योग्य बैकएंड का पथ |
| `टाइमआउट` | int | `30` | सेकंड में टाइमआउट का अनुरोध करें |
| `बैकएंड_स्टार्टअप_टाइमआउट` | int | `60` | बैकएंड स्टार्टअप के लिए टाइमआउट (सेकंड) |

**उदाहरण:**

```python
# Default (auto-start backend)
chloros = ChlorosLocal()

# Connect to running backend
chloros = ChlorosLocal(auto_start_backend=False)

# Custom backend path
chloros = ChlorosLocal(backend_exe="C:/Custom/chloros-backend.exe")

# Custom timeout
chloros = ChlorosLocal(timeout=60)
```

***

### तरीके

#### `क्रिएट_प्रोजेक्ट(प्रोजेक्ट_नाम, कैमरा=कोई नहीं)`

एक नया क्लोरोस प्रोजेक्ट बनाएं।

**पैरामीटर:**

| पैरामीटर | प्रकार | आवश्यक | विवरण |
| -------------- | ---- | -------- | -------------------------------------------------------- |
| `प्रोजेक्ट_नाम` | स्ट्र | हाँ | प्रोजेक्ट का नाम |
| 'कैमरा' | स्ट्र | नहीं | कैमरा टेम्प्लेट (उदा., "Survey3N\_RGN", "Survey3W\_OCN") |

**रिटर्न:** `dict` - प्रोजेक्ट निर्माण प्रतिक्रिया

**उदाहरण:**

```python
# Basic project
chloros.create_project("DroneField_A")

# With camera template
chloros.create_project("DroneField_A", camera="Survey3N_RGN")
```

***

#### `import_images(folder_path, recursive=False)`

किसी फ़ोल्डर से छवियाँ आयात करें.

**पैरामीटर:**

| पैरामीटर | प्रकार | आवश्यक | विवरण |
| ------------- | -------- | -------- | ---------------------------------- |
| `फ़ोल्डर_पथ` | स्ट्र/पथ | हाँ | छवियों वाले फ़ोल्डर का पथ |
| 'पुनरावर्ती' | बूल | नहीं | सबफ़ोल्डर खोजें (डिफ़ॉल्ट: गलत) |

**रिटर्न:** `dict` - फ़ाइल संख्या के साथ परिणाम आयात करें

**उदाहरण:**

```python
# Import from folder
chloros.import_images("C:\\DroneImages\\Flight001")

# Import recursively
chloros.import_images("C:\\DroneImages", recursive=True)
```

***

#### `कॉन्फ़िगर(**सेटिंग्स)`

प्रोसेसिंग सेटिंग्स कॉन्फ़िगर करें.

**पैरामीटर:**

| पैरामीटर | प्रकार | डिफ़ॉल्ट | विवरण |
| ------------------------- | ---- | ----------------------- | ------------------------------- |
| 'बहस करनेवाला' | स्ट्र | "उच्च गुणवत्ता (तेज़)" | डिबायर विधि |
| `विग्नेट_करेक्शन` | बूल | 'सच' | विग्नेट सुधार सक्षम करें |
| `प्रतिबिंब_अंशांकन` | बूल | 'सच' | परावर्तन अंशांकन सक्षम करें |
| `सूचकांक` | सूची | 'कोई नहीं' | गणना करने के लिए वनस्पति सूचकांक |
| `निर्यात_प्रारूप` | स्ट्र | "टीआईएफएफ (16-बिट)" | आउटपुट स्वरूप |
| `पीपीके` | बूल | 'झूठा' | पीपीके सुधार सक्षम करें |
| `कस्टम_सेटिंग्स` | हुक्म | 'कोई नहीं' | उन्नत कस्टम सेटिंग्स |

**निर्यात प्रारूप:**

* ``टीआईएफएफ (16-बिट)'' - जीआईएस/फोटोग्राममेट्री के लिए अनुशंसित
* ``टीआईएफएफ (32-बिट, प्रतिशत)'' - वैज्ञानिक विश्लेषण
* ``पीएनजी (8-बिट)'' - दृश्य निरीक्षण
* ``जेपीजी (8-बिट)'' - संपीड़ित आउटपुट

**उपलब्ध सूचकांक:**

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2, और बहुत कुछ।

**उदाहरण:**

```python
# Basic configuration
chloros.configure(
    vignette_correction=True,
    reflectance_calibration=True,
    indices=["NDVI", "NDRE"]
)

# Advanced configuration
chloros.configure(
    debayer="High Quality (Faster)",
    vignette_correction=True,
    reflectance_calibration=True,
    ppk=True,
    export_format="TIFF (32-bit, Percent)",
    indices=["NDVI", "NDRE", "GNDVI", "OSAVI", "CIG"]
)
```

***

#### `प्रक्रिया(मोड=समानांतर', प्रतीक्षा=सही, प्रगति_कॉलबैक=कोई नहीं)'

प्रोजेक्ट छवियों को संसाधित करें.

**पैरामीटर:**

| पैरामीटर | प्रकार | डिफ़ॉल्ट | विवरण |
| ------------------- | -------- | ------------ | ----------------------------------------- |
| `मोड` | स्ट्र | `"समानांतर"` | प्रसंस्करण मोड: "समानांतर" या "धारावाहिक" |
| `रुको` | बूल | 'सच' | पूरा होने की प्रतीक्षा करें |
| `प्रगति_कॉलबैक` | कॉल करने योग्य | 'कोई नहीं' | प्रगति कॉलबैक फ़ंक्शन (प्रगति, संदेश) |
| `मतदान_अंतराल` | तैरना | `2.0` | प्रगति के लिए मतदान अंतराल (सेकंड) |

**रिटर्न:** `dict` - प्रसंस्करण परिणाम

{% संकेत शैली = "चेतावनी" %}
**समानांतर मोड**: क्लोरोस+ लाइसेंस की आवश्यकता है। स्वचालित रूप से आपके सीपीयू कोर (16 श्रमिकों तक) को स्केल करता है।
{%अंतसंकेत%}

**उदाहरण:**

```python
# Simple processing
results = chloros.process()

# With progress monitoring
def show_progress(progress, message):
    print(f"[{progress}%] {message}")

chloros.process(
    mode="parallel",
    progress_callback=show_progress,
    wait=True
)

# Fire-and-forget (non-blocking)
chloros.process(wait=False)
```

***

#### `get_config()`

वर्तमान प्रोजेक्ट कॉन्फ़िगरेशन प्राप्त करें.

**रिटर्न:** `dict` - वर्तमान प्रोजेक्ट कॉन्फ़िगरेशन

**उदाहरण:**

```python
config = chloros.get_config()
print(config['Project Settings'])
```

***

#### `get_status()`

बैकएंड स्थिति की जानकारी प्राप्त करें.

**रिटर्न:** `dict` - बैकएंड स्थिति

**उदाहरण:**

```python
status = chloros.get_status()
print(f"Running: {status['running']}")
print(f"URL: {status['url']}")
```

***

#### `शटडाउन_बैकएंड()`

बैकएंड बंद करें (यदि एसडीके द्वारा शुरू किया गया हो)।

**उदाहरण:**

```python
chloros.shutdown_backend()
```

***

### सुविधा कार्य

#### `प्रक्रिया_फ़ोल्डर(फ़ोल्डर_पथ, **विकल्प)`

किसी फ़ोल्डर को संसाधित करने के लिए एक-पंक्ति सुविधा फ़ंक्शन।

**पैरामीटर:**

| पैरामीटर | प्रकार | डिफ़ॉल्ट | विवरण |
| ------------------------- | -------- | --------------- | ------------------------------ |
| `फ़ोल्डर_पथ` | स्ट्र/पथ | आवश्यक | छवियों वाले फ़ोल्डर का पथ |
| `प्रोजेक्ट_नाम` | स्ट्र | स्वतः उत्पन्न | प्रोजेक्ट का नाम |
| 'कैमरा' | स्ट्र | 'कोई नहीं' | कैमरा टेम्पलेट |
| `सूचकांक` | सूची | `["एनडीवीआई"]` | गणना करने के लिए सूचकांक |
| `विग्नेट_करेक्शन` | बूल | 'सच' | विग्नेट सुधार सक्षम करें |
| `प्रतिबिंब_अंशांकन` | बूल | 'सच' | परावर्तन अंशांकन सक्षम करें |
| `निर्यात_प्रारूप` | स्ट्र | "टीआईएफएफ (16-बिट)" | आउटपुट स्वरूप |
| `मोड` | स्ट्र | `"समानांतर"` | प्रोसेसिंग मोड |
| `प्रगति_कॉलबैक` | कॉल करने योग्य | 'कोई नहीं' | प्रगति कॉलबैक |

**रिटर्न:** `dict` - प्रसंस्करण परिणाम

**उदाहरण:**

```python
from chloros_sdk import process_folder

# Simple one-liner
results = process_folder("C:\\DroneImages\\Flight001")

# With custom settings
results = process_folder(
    "C:\\DroneImages\\Flight001",
    project_name="Field_A_Survey",
    camera="Survey3N_RGN",
    indices=["NDVI", "NDRE", "GNDVI"],
    mode="parallel"
)

# With progress monitoring
def show_progress(progress, message):
    print(f"[{progress}%] {message}")

results = process_folder(
    "C:\\DroneImages\\Flight001",
    progress_callback=show_progress
)
```

***

## संदर्भ प्रबंधक समर्थन

एसडीके स्वचालित सफाई के लिए संदर्भ प्रबंधकों का समर्थन करता है:

```python
from chloros_sdk import ChlorosLocal

# Auto-cleanup when done
with ChlorosLocal() as chloros:
    chloros.create_project("MyProject")
    chloros.import_images("C:\\Images")
    chloros.configure(indices=["NDVI"])
    chloros.process()
# Backend automatically shut down here
```

***

## संपूर्ण उदाहरण

### उदाहरण 1: बुनियादी प्रसंस्करण

डिफ़ॉल्ट सेटिंग्स वाले फ़ोल्डर को संसाधित करें:

```python
from chloros_sdk import process_folder

# Process with default settings
results = process_folder("C:\\Datasets\\Field_A_2025_01_15")

print(f"Processing complete: {results}")
```

***

### उदाहरण 2: कस्टम वर्कफ़्लो

प्रसंस्करण पाइपलाइन पर पूर्ण नियंत्रण:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK
chloros = ChlorosLocal()

# Create project with camera template
chloros.create_project("Research_Plot_A", camera="Survey3N_RGN")

# Import images
import_results = chloros.import_images("C:\\Research\\PlotA")
print(f"Imported {len(import_results.get('files', []))} images")

# Configure advanced settings
chloros.configure(
    debayer="High Quality (Faster)",
    vignette_correction=True,
    reflectance_calibration=True,
    ppk=False,
    export_format="TIFF (16-bit)",
    indices=["NDVI", "NDRE", "GNDVI", "OSAVI"]
)

# Process with progress monitoring
def show_progress(progress, message):
    print(f"Progress: {progress}% - {message}")

chloros.process(
    mode="parallel",
    progress_callback=show_progress,
    wait=True
)

print("Processing complete!")
```

***

### उदाहरण 3: एकाधिक फ़ोल्डरों का बैच प्रसंस्करण

एकाधिक उड़ान डेटासेट संसाधित करें:

```python
from chloros_sdk import ChlorosLocal
from pathlib import Path

# Initialize SDK once
chloros = ChlorosLocal()

# List of flight folders
flights = [
    "C:\\Datasets\\Flight_001",
    "C:\\Datasets\\Flight_002",
    "C:\\Datasets\\Flight_003"
]

for flight_path in flights:
    flight_name = Path(flight_path).name
    print(f"\n{'='*60}")
    print(f"Processing: {flight_name}")
    print('='*60)
    
    try:
        # Create project
        chloros.create_project(flight_name, camera="Survey3N_RGN")
        
        # Import images
        chloros.import_images(flight_path)
        
        # Configure
        chloros.configure(
            vignette_correction=True,
            reflectance_calibration=True,
            indices=["NDVI", "NDRE", "GNDVI"]
        )
        
        # Process
        chloros.process(mode="parallel", wait=True)
        
        print(f"✓ {flight_name} completed successfully")
    
    except Exception as e:
        print(f"✗ {flight_name} failed: {e}")

print("\n" + "="*60)
print("All flights processed!")
```

***

### उदाहरण 4: अनुसंधान पाइपलाइन एकीकरण

डेटा विश्लेषण के साथ क्लोरोस को एकीकृत करें:

```python
from chloros_sdk import ChlorosLocal
import pandas as pd
import matplotlib.pyplot as plt

# Initialize Chloros
chloros = ChlorosLocal()

# Field survey data
surveys = [
    {"name": "Plot_A", "folder": "C:\\Research\\PlotA", "biomass": 4500},
    {"name": "Plot_B", "folder": "C:\\Research\\PlotB", "biomass": 3800},
    {"name": "Plot_C", "folder": "C:\\Research\\PlotC", "biomass": 5200}
]

results = []

for survey in surveys:
    # Process with Chloros
    chloros.create_project(survey['name'])
    chloros.import_images(survey['folder'])
    chloros.configure(indices=["NDVI", "NDRE"])
    chloros.process(mode="parallel", wait=True)
    
    # Get results
    config = chloros.get_config()
    
    # Extract NDVI values (example - adjust based on your needs)
    # In real implementation, you would read the processed TIFF files
    
    results.append({
        'plot': survey['name'],
        'biomass': survey['biomass'],
        # Add your NDVI extraction here
    })

# Statistical analysis
df = pd.DataFrame(results)
print("\nResults:")
print(df)

# Create correlation plot
# plt.scatter(df['ndvi'], df['biomass'])
# plt.xlabel('NDVI')
# plt.ylabel('Biomass (kg/ha)')
# plt.title('NDVI vs Biomass Correlation')
# plt.show()
```

***

### उदाहरण 5: कस्टम प्रगति निगरानी

लॉगिंग के साथ उन्नत प्रगति ट्रैकिंग:

```python
from chloros_sdk import ChlorosLocal
from datetime import datetime
import logging

# Setup logging
logging.basicConfig(
    filename=f'processing_{datetime.now():%Y%m%d_%H%M%S}.log',
    level=logging.INFO,
    format='%(asctime)s - %(message)s'
)

# Progress callback with logging
def log_progress(progress, message):
    log_msg = f"[{progress}%] {message}"
    logging.info(log_msg)
    print(log_msg)

# Process with logging
chloros = ChlorosLocal()
chloros.create_project("LoggedProcess")
chloros.import_images("C:\\DroneImages")
chloros.configure(indices=["NDVI", "NDRE"])

logging.info("Starting processing...")
chloros.process(
    mode="parallel",
    progress_callback=log_progress,
    wait=True
)
logging.info("Processing complete!")
```

***

### उदाहरण 6: त्रुटि प्रबंधन

उत्पादन उपयोग के लिए मजबूत त्रुटि प्रबंधन:

```python
from chloros_sdk import ChlorosLocal
from chloros_sdk.exceptions import (
    ChlorosError,
    ChlorosBackendError,
    ChlorosLicenseError,
    ChlorosProcessingError
)

def process_safely(folder_path):
    """Process with comprehensive error handling"""
    try:
        with ChlorosLocal() as chloros:
            chloros.create_project("SafeProcess")
            chloros.import_images(folder_path)
            chloros.configure(indices=["NDVI"])
            chloros.process()
            
        return True, "Success"
    
    except ChlorosLicenseError as e:
        return False, f"License error: {e}. Upgrade to Chloros+ at cloud.mapir.camera/pricing"
    
    except ChlorosBackendError as e:
        return False, f"Backend error: {e}. Ensure Chloros Desktop is installed."
    
    except ChlorosProcessingError as e:
        return False, f"Processing error: {e}"
    
    except FileNotFoundError as e:
        return False, f"Folder not found: {e}"
    
    except ChlorosError as e:
        return False, f"Chloros error: {e}"
    
    except Exception as e:
        return False, f"Unexpected error: {e}"

# Use the safe function
success, message = process_safely("C:\\DroneImages\\Flight001")
if success:
    print(f"✓ {message}")
else:
    print(f"✗ {message}")
```

***

### उदाहरण 7: कमांड-लाइन टूल

SDK के साथ एक कस्टम CLI टूल बनाएं:

```python
#!/usr/bin/env python
"""
Custom Chloros CLI Tool
Process multiple folders from command line
"""

import sys
import argparse
from pathlib import Path
from chloros_sdk import process_folder

def main():
    parser = argparse.ArgumentParser(description='Custom Chloros Processor')
    parser.add_argument('folders', nargs='+', help='Folders to process')
    parser.add_argument('--indices', nargs='+', default=['NDVI'],
                       help='Indices to calculate (default: NDVI)')
    parser.add_argument('--camera', default=None,
                       help='Camera template')
    parser.add_argument('--format', default='TIFF (16-bit)',
                       help='Export format')
    
    args = parser.parse_args()
    
    successful = []
    failed = []
    
    for folder in args.folders:
        folder_path = Path(folder)
        
        if not folder_path.exists():
            print(f"✗ Skipping {folder}: not found")
            failed.append(folder)
            continue
        
        print(f"\nProcessing: {folder_path.name}...")
        
        try:
            process_folder(
                folder_path,
                camera=args.camera,
                indices=args.indices,
                export_format=args.format
            )
            print(f"✓ {folder_path.name} complete")
            successful.append(folder)
        
        except Exception as e:
            print(f"✗ {folder_path.name} failed: {e}")
            failed.append(folder)
    
    # Summary
    print(f"\n{'='*60}")
    print(f"Summary: {len(successful)} successful, {len(failed)} failed")
    
    return 0 if not failed else 1

if __name__ == '__main__':
    sys.exit(main())
```

**उपयोग:**

```bash
python my_processor.py "C:\Flight001" "C:\Flight002" --indices NDVI NDRE GNDVI
```

***

## एक्सेप्शन हेंडलिंग

एसडीके विभिन्न त्रुटि प्रकारों के लिए विशिष्ट अपवाद वर्ग प्रदान करता है:

### अपवाद पदानुक्रम

```python
ChlorosError                    # Base exception
├── ChlorosBackendError        # Backend startup/connection issues
├── ChlorosLicenseError        # License validation issues
├── ChlorosConnectionError     # Network/connection failures
├── ChlorosProcessingError     # Image processing failures
├── ChlorosAuthenticationError # Authentication failures
└── ChlorosConfigurationError  # Configuration errors
```

### अपवाद उदाहरण

```python
from chloros_sdk import ChlorosLocal
from chloros_sdk.exceptions import *

try:
    chloros = ChlorosLocal()
    chloros.process()

except ChlorosLicenseError:
    print("Chloros+ license required. Upgrade at cloud.mapir.camera/pricing")

except ChlorosBackendError:
    print("Backend failed to start. Ensure Chloros Desktop is installed.")

except ChlorosProcessingError as e:
    print(f"Processing failed: {e}")

except ChlorosError as e:
    print(f"General Chloros error: {e}")
```

***

## उन्नत विषय

### कस्टम बैकएंड कॉन्फ़िगरेशन

कस्टम बैकएंड स्थान या कॉन्फ़िगरेशन का उपयोग करें:

```python
chloros = ChlorosLocal(
    backend_exe="C:\\Custom\\chloros-backend.exe",
    api_url="http://localhost:5001",  # Custom port
    timeout=60,                        # Longer timeout
    backend_startup_timeout=120        # 2 minutes startup
)
```

### नॉन-ब्लॉकिंग प्रोसेसिंग

प्रसंस्करण प्रारंभ करें और अन्य कार्य जारी रखें:

```python
# Start processing (non-blocking)
chloros.process(wait=False)

# Do other work here...
print("Processing started in background...")

# Check status later
import time
while True:
    status = chloros.get_config()
    if status.get('processing_complete'):
        break
    time.sleep(5)

print("Processing complete!")
```

### मेमोरी प्रबंधन

बड़े डेटासेट के लिए, बैचों में प्रक्रिया करें:

```python
from pathlib import Path

base_folder = Path("C:\\LargeDataset")
batch_size = 100

# Get all image files
images = list(base_folder.glob("*.RAW"))

# Process in batches
for i in range(0, len(images), batch_size):
    batch = images[i:i+batch_size]
    batch_folder = base_folder / f"batch_{i//batch_size}"
    
    # Create batch folder and move images
    # ... (implementation details)
    
    # Process batch
    process_folder(batch_folder)
```

***

## समस्या निवारण

### बैकएंड प्रारंभ नहीं हो रहा है

**समस्या:** SDK बैकएंड प्रारंभ करने में विफल रहता है

**समाधान:**

1. सत्यापित करें कि क्लोरोस डेस्कटॉप स्थापित है:

```python
import os
backend_path = r"C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe"
print(f"Backend exists: {os.path.exists(backend_path)}")
```

2. जांचें कि विंडोज़ फ़ायरवॉल अवरुद्ध तो नहीं हो रहा है
3. मैन्युअल बैकएंड पथ आज़माएँ:

```python
chloros = ChlorosLocal(backend_exe="C:\\Path\\To\\chloros-backend.exe")
```

***

### लाइसेंस का पता नहीं चला

**मुद्दा:** एसडीके ने लाइसेंस गुम होने की चेतावनी दी है

**समाधान:**

1. क्लोरोस, क्लोरोस (ब्राउज़र) या क्लोरोस सीएलआई खोलें और लॉगिन करें।
2. सत्यापित करें कि लाइसेंस कैश किया गया है:

```python
from pathlib import Path
import os

# Check cache location (Windows)
cache_path = Path(os.getenv('APPDATA')) / 'Chloros' / 'cache'
print(f"Cache exists: {cache_path.exists()}")
```

3. समर्थन से संपर्क करें: info@mapir.camera

***

### आयात त्रुटियाँ

**समस्या:** `ModuleNotFoundError: 'क्लोरोस_एसडीके' नाम का कोई मॉड्यूल नहीं`

**समाधान:**

```bash
# Verify installation
pip show chloros-sdk

# Reinstall if needed
pip uninstall chloros-sdk
pip install chloros-sdk

# Check Python environment
python -c "import sys; print(sys.path)"
```

***

### प्रोसेसिंग टाइमआउट

**समस्या:** प्रसंस्करण समय समाप्त

**समाधान:**

1. टाइमआउट बढ़ाएँ:

```python
chloros = ChlorosLocal(timeout=120)  # 2 minutes
```

2. छोटे बैचों में प्रक्रिया करें
3. उपलब्ध डिस्क स्थान की जाँच करें
4. सिस्टम संसाधनों की निगरानी करें

***

### पोर्ट पहले से ही उपयोग में है

**समस्या:** बैकएंड पोर्ट 5000 पर कब्जा

**समाधान:**

```python
# Use different port
chloros = ChlorosLocal(api_url="http://localhost:5001")
```

या विरोधाभासी प्रक्रिया ढूंढें और बंद करें:

```powershell
# PowerShell
Get-NetTCPConnection -LocalPort 5000
```

***

## प्रदर्शन युक्तियाँ

### प्रसंस्करण गति को अनुकूलित करें

1. **समानांतर मोड का उपयोग करें** (क्लोरोस+ की आवश्यकता है)

```python
chloros.process(mode="parallel")  # Up to 16 workers
```

2. **आउटपुट रिज़ॉल्यूशन कम करें** (यदि स्वीकार्य हो)

```python
chloros.configure(export_format="PNG (8-bit)")  # Faster than TIFF
```

3. **अनावश्यक सूचकांक अक्षम करें**

```python
# Only calculate needed indices
chloros.configure(indices=["NDVI"])  # Not all indices
```

4. **एसएसडी पर प्रक्रिया** (एचडीडी नहीं)

***

### मेमोरी अनुकूलन

बड़े डेटासेट के लिए:

```python
# Process in batches instead of all at once
# See "Memory Management" in Advanced Topics
```

***

### पृष्ठभूमि प्रसंस्करण

अन्य कार्यों के लिए पायथन को मुक्त करें:

```python
chloros.process(wait=False)  # Non-blocking

# Continue with other work
# ...
```

***

## एकीकरण उदाहरण

### Django एकीकरण

```python
# views.py
from django.http import JsonResponse
from chloros_sdk import process_folder

def process_images_view(request):
    if request.method == 'POST':
        folder_path = request.POST.get('folder_path')
        
        try:
            results = process_folder(folder_path)
            return JsonResponse({'success': True, 'results': results})
        except Exception as e:
            return JsonResponse({'success': False, 'error': str(e)})
```

### फ्लास्क एपीआई

```python
# app.py
from flask import Flask, request, jsonify
from chloros_sdk import process_folder

app = Flask(__name__)

@app.route('/api/process', methods=['POST'])
def process():
    data = request.get_json()
    folder_path = data.get('folder_path')
    
    try:
        results = process_folder(folder_path)
        return jsonify({'success': True, 'results': results})
    except Exception as e:
        return jsonify({'success': False, 'error': str(e)}), 500

if __name__ == '__main__':
    app.run()
```

### ज्यूपिटर नोटबुक

```python
# notebook.ipynb
from chloros_sdk import ChlorosLocal
import matplotlib.pyplot as plt

# Initialize
chloros = ChlorosLocal()

# Process
chloros.create_project("JupyterTest")
chloros.import_images("C:\\Data")
chloros.configure(indices=["NDVI"])

# Progress in notebook
from IPython.display import clear_output

def notebook_progress(progress, message):
    clear_output(wait=True)
    print(f"Progress: {progress}%")
    print(message)

chloros.process(progress_callback=notebook_progress)

# Visualize results
# ... (your visualization code)
```

***

## अक्सर पूछे जाने वाले प्रश्न

### प्रश्न: क्या एसडीके को इंटरनेट कनेक्शन की आवश्यकता है?

**ए:** केवल प्रारंभिक लाइसेंस सक्रियण के लिए। क्लोरोस, क्लोरोस (ब्राउज़र) या क्लोरोस सीएलआई के माध्यम से लॉग इन करने के बाद लाइसेंस स्थानीय रूप से कैश किया जाता है और 30 दिनों के लिए ऑफ़लाइन काम करता है।

***

### प्रश्न: क्या मैं जीयूआई के बिना सर्वर पर एसडीके का उपयोग कर सकता हूं?

**ए:** हाँ! आवश्यकताएं:

*विंडोज सर्वर 2016 या बाद का संस्करण
*क्लोरोस स्थापित (एक बार)
* किसी भी मशीन पर लाइसेंस सक्रिय (कैश्ड लाइसेंस सर्वर पर कॉपी किया गया)

***

### प्रश्न: डेस्कटॉप, सीएलआई और एसडीके के बीच क्या अंतर है?

| फ़ीचर | डेस्कटॉप जीयूआई | सीएलआई कमांड लाइन | पायथन एसडीके |
| --------------- | ----------- | ---------------- | ----------- |
| **इंटरफ़ेस** | प्वाइंट-क्लिक करें | आदेश | पायथन एपीआई |
| **के लिए सर्वश्रेष्ठ** | दृश्य कार्य | स्क्रिप्टिंग | एकीकरण |
| **स्वचालन** | सीमित | अच्छा | उत्कृष्ट |
| **लचीलापन** | बुनियादी | अच्छा | अधिकतम |
| **लाइसेंस** | क्लोरोस+ | क्लोरोस+ | क्लोरोस+ |

***

### प्रश्न: क्या मैं एसडीके के साथ निर्मित ऐप्स वितरित कर सकता हूं?

**ए:** एसडीके कोड को आपके एप्लिकेशन में एकीकृत किया जा सकता है, लेकिन:

* अंतिम उपयोगकर्ताओं को क्लोरोस स्थापित करने की आवश्यकता है
* अंतिम उपयोगकर्ताओं को सक्रिय क्लोरोस+ लाइसेंस की आवश्यकता होती है
* वाणिज्यिक वितरण के लिए ओईएम लाइसेंसिंग की आवश्यकता होती है

OEM पूछताछ के लिए info@mapir.camera से संपर्क करें।

***

### प्रश्न: मैं एसडीके को कैसे अपडेट करूं?

```bash
pip install --upgrade chloros-sdk
```

***

### प्रश्न: संसाधित छवियाँ कहाँ सहेजी जाती हैं?

डिफ़ॉल्ट रूप से, प्रोजेक्ट पथ में:

```
Project_Path/
└── MyProject/
    └── Survey3N_RGN/          # Processed outputs
```

***

### प्रश्न: क्या मैं निर्धारित समय पर चल रही पायथन स्क्रिप्ट से छवियों को संसाधित कर सकता हूं?

**ए:** हाँ! पायथन स्क्रिप्ट के साथ विंडोज टास्क शेड्यूलर का उपयोग करें:

```python
# scheduled_processing.py
from chloros_sdk import process_folder

# Process today's flights
results = process_folder("C:\\Flights\\Today")
```

प्रतिदिन चलने के लिए टास्क शेड्यूलर के माध्यम से शेड्यूल करें।

***

### प्रश्न: क्या एसडीके एसिंक/प्रतीक्षा का समर्थन करता है?

**ए:** वर्तमान संस्करण समकालिक है। एसिंक व्यवहार के लिए, `wait=False` का उपयोग करें या अलग थ्रेड में चलाएँ:

```python
import threading

def process_thread():
    chloros.process()

thread = threading.Thread(target=process_thread)
thread.start()

# Continue with other work...
```

***

## मदद प्राप्त करें

### दस्तावेज़ीकरण

* **एपीआई संदर्भ**: यह पृष्ठ

### सहायता चैनल

* **ईमेल**: info@mapir.camera
* **वेबसाइट**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **मूल्य निर्धारण**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

### नमूना कोड

यहां सूचीबद्ध सभी उदाहरण परीक्षणित और उत्पादन के लिए तैयार हैं। उन्हें कॉपी करें और अपने उपयोग के मामले में अनुकूलित करें।

***

## लाइसेंस

**मालिकाना सॉफ्टवेयर** - कॉपीराइट (सी) 2025 एमएपीआईआर इंक।

एसडीके को एक सक्रिय क्लोरोस+ सदस्यता की आवश्यकता है। अनधिकृत उपयोग, वितरण या संशोधन निषिद्ध है।
