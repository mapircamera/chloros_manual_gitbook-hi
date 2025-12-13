# एपीआई: पायथन एसडीके

**क्लोरोस पायथन एसडीके** क्लोरोस इमेज प्रोसेसिंग इंजन तक प्रोग्रामेटिक पहुंच प्रदान करता है, जो आपके पायथन अनुप्रयोगों और अनुसंधान प्रक्रियाओं के साथ स्वचालन, कस्टम वर्कफ़्लो और निर्बाध एकीकरण को सक्षम करता है।

### मुख्य विशेषताएं

* 🐍 **नेटिव पायथन** - इमेज प्रोसेसिंग के लिए स्वच्छ, पायथोनिक एपीआई
* 🔧 **पूर्ण एपीआई एक्सेस** - क्लोरोस प्रसंस्करण पर पूर्ण नियंत्रण
* 🚀 **स्वचालन** – कस्टम बैच प्रोसेसिंग वर्कफ़्लो बनाएं
* 🔗 **एकीकरण** - क्लोरोस को मौजूदा पायथन अनुप्रयोगों में एम्बेड करें।
* 📊 **शोध के लिए तैयार**: वैज्ञानिक विश्लेषण प्रक्रियाओं के लिए बिल्कुल सही।
* ⚡ **समानांतर प्रसंस्करण**: आपके सीपीयू कोर (क्लोरोस+) के अनुकूल होता है।

### आवश्यकताएं

| आवश्यकता | विवरण |
| ------------------- | ---------------------------------------------------------------------------------- |
| **क्लोरोस डेस्कटॉप** | स्थानीय रूप से स्थापित किया जाना चाहिए |
| **लाइसेंस** | क्लोरोस+ ([भुगतान योजना आवश्यक](https://cloud.mapir.camera/pricing)) |
| **ऑपरेटिंग सिस्टम** | विंडोज़ 10/11 (64-बिट) |
| **पायथन** | पायथन 3.7 या उच्चतर |
| **स्मृति** | न्यूनतम 8जीबी रैम (16जीबी अनुशंसित) |
| **इंटरनेट** | लाइसेंस सक्रियण के लिए आवश्यक |

{% संकेत शैली=&quot;चेतावनी&quot; %}
**लाइसेंस आवश्यकताएँ**: पायथन एसडीके को एपीआई तक पहुंचने के लिए क्लोरोस+ की सशुल्क सदस्यता की आवश्यकता होती है। मानक (निःशुल्क) योजनाओं में एपीआई/एसडीके पहुंच नहीं है। अपडेट करने के लिए [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing) पर जाएं।
{%अंतसंकेत%}

## त्वरित शुरुआत

### सुविधा

पिप के माध्यम से स्थापित करें:

```bash
pip install chloros-sdk
```

{% संकेत शैली=&quot;जानकारी&quot; %}
**प्रारंभिक सेटअप**: एसडीके का उपयोग करने से पहले, क्लोरोस, क्लोरोस (ब्राउज़र), या क्लोरोस सीएलआई खोलकर और अपने क्रेडेंशियल्स के साथ लॉग इन करके अपने क्लोरोस+ लाइसेंस को सक्रिय करें। इसे केवल एक बार ही करना होगा.
{%अंतसंकेत%}

### बुनियादी उपयोग

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
3. **एक्टिव क्लोरोस+ लाइसेंस** ([अपडेट](https://cloud.mapir.camera/pricing))

### पिप का उपयोग करके इंस्टालेशन

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

जाँचें कि SDK सही ढंग से स्थापित है:

```python
import chloros_sdk
print(f"Chloros SDK version: {chloros_sdk.__version__}")
```

***

## प्रारंभिक सेटअप

### लाइसेंस सक्रियण

एसडीके क्लोरोस, क्लोरोस (ब्राउज़र) और क्लोरोस सीएलआई के समान लाइसेंस का उपयोग करता है। इसे GUI या CLI के माध्यम से एक बार सक्रिय करें:

1. **क्लोरोस या क्लोरोस (ब्राउज़र)** खोलें और उपयोगकर्ता <img src=”.gitbook/assets/icon_user.JPG” alt=”” data-size=”line”> टैब में लॉग इन करें। या, **सीएलआई** खोलें।
2. अपना क्लोरोस+ क्रेडेंशियल दर्ज करें और लॉग इन करें
3. लाइसेंस स्थानीय कैश में संग्रहीत है (रीबूट के बाद भी बना रहता है)

{% संकेत शैली=&quot;सफलता&quot; %}
**एक बार सेटअप**: जीयूआई या सीएलआई के माध्यम से लॉग इन करने के बाद, एसडीके स्वचालित रूप से कैश्ड लाइसेंस का उपयोग करता है। किसी अतिरिक्त प्रमाणीकरण की आवश्यकता नहीं!
{%अंतसंकेत%}

### टेस्ट कनेक्शन

जांचें कि एसडीके क्लोरोस से कनेक्ट हो सकता है:

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

#### बिल्डर

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
| -------------------------------- | ---- | -------------------------------- | ------------------------------------------------ |
| `api_url` | स्ट्र | `"http://localhost:5000"` | क्लोरोस स्थानीय बैकएंड यूआरएल |
| `auto_start_backend` | बूल | `True` | यदि आवश्यक हो तो स्वचालित रूप से बैकएंड प्रारंभ करें |
| `backend_exe` | स्ट्र | `None` (स्वतः पता लगाना) | निष्पादन योग्य बैकएंड का पथ |
| `timeout` | int | `30` | सेकंड में टाइमआउट का अनुरोध करें |
| `backend_startup_timeout` | int | `60` | बैकएंड स्टार्टअप टाइमआउट (सेकंड) |

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

#### `create_project(project_name, camera=None)`

एक नया क्लोरोस प्रोजेक्ट बनाएं।

**पैरामीटर:**

| पैरामीटर | प्रकार | अनिवार्य | विवरण |
| -------------- | ---- | -------- | ---------------------------------------------------------------- |
| `project_name` | स्ट्र | हाँ | प्रोजेक्ट का नाम |
| `camera` | स्ट्र | नहीं | कैमरा टेम्प्लेट (उदा. “Survey3N\_RGN”, “Survey3W\_OCN”) |

**रिटर्न:** `dict`: प्रोजेक्ट निर्माण प्रतिक्रिया

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

| पैरामीटर | प्रकार | अनिवार्य | विवरण |
| ----------------- | -------- | -------- | ---------------------------------- |
| `folder_path` | स्ट्र/पथ | हाँ | छवियों वाले फ़ोल्डर का पथ |
| `recursive` | बूल | नहीं | सबफ़ोल्डर खोजें (डिफ़ॉल्ट: गलत) |

**रिटर्न:** `dict`: फ़ाइल संख्या के साथ परिणाम आयात करें।

**उदाहरण:**

```python
# Import from folder
chloros.import_images("C:\\DroneImages\\Flight001")

# Import recursively
chloros.import_images("C:\\DroneImages", recursive=True)
```

***

#### `configure(**settings)`

प्रोसेसिंग सेटिंग्स कॉन्फ़िगर करें.

**पैरामीटर:**

| पैरामीटर | प्रकार | डिफ़ॉल्ट | विवरण |
| -------------------------------- | ---- | ---------------------- | -------------------------------- |
| `debayer` | स्ट्र | «उच्च गुणवत्ता (तेज)» | डिबायर विधि |
| `vignette_correction` | बूल | `True` | विग्नेट सुधार सक्षम करें |
| `reflectance_calibration` | बूल | `True` | परावर्तन अंशांकन सक्षम करें |
| `indices` | सूची | `None` | गणना करने के लिए वनस्पति सूचकांक |
| `export_format` | स्ट्र | «TIFF (16 बिट)» | आउटपुट स्वरूप |
| `ppk` | बूल | `False` | पीपीके सुधार सक्षम करें |
| `custom_settings` | हुक्म | `None` | उन्नत कस्टम सेटिंग्स |

**निर्यात प्रारूप:**

* `"TIFF (16-bit)"`: जीआईएस/फोटोग्रामेट्री के लिए अनुशंसित
* `"TIFF (32-bit, Percent)"`: वैज्ञानिक विश्लेषण
* `"PNG (8-bit)"`: दृश्य निरीक्षण
* `"JPG (8-bit)"`: संपीड़ित आउटपुट

**सूचकांक उपलब्ध:**

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2 और बहुत कुछ।

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

#### `process(mode="parallel", wait=True, progress_callback=None)`

प्रक्रिया परियोजना छवियाँ.

**पैरामीटर:**

| पैरामीटर | प्रकार | डिफ़ॉल्ट | विवरण |
| ------------------- | -------- | ----------- | ------------------------------------------------ |
| `mode` | स्ट्र | `"parallel"` | प्रसंस्करण मोड: "समानांतर" या "धारावाहिक" |
| `wait` | बूल | `True` | इसके ख़त्म होने का इंतज़ार करें |
| `progress_callback` | कॉल करने योग्य | `None` | प्रगति कॉलबैक फ़ंक्शन (प्रगति, संदेश) |
| `poll_interval` | तैरना | `2.0` | प्रगति के लिए मतदान अंतराल (सेकंड) |

**रिटर्न:** `dict` - प्रसंस्करण परिणाम

{% संकेत शैली=&quot;चेतावनी&quot; %}
**समानांतर मोड**: क्लोरोस+ लाइसेंस की आवश्यकता है। स्वचालित रूप से सीपीयू कोर (16 श्रमिकों तक) के लिए अनुकूल हो जाता है।
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

वर्तमान प्रोजेक्ट कॉन्फ़िगरेशन प्राप्त करता है।

**रिटर्न:** `dict`: वर्तमान प्रोजेक्ट कॉन्फ़िगरेशन।

**उदाहरण:**

```python
config = chloros.get_config()
print(config['Project Settings'])
```

***

#### `get_status()`

बैकएंड की स्थिति के बारे में जानकारी प्राप्त करें.

**रिटर्न:** `dict` - बैकएंड स्थिति।

**उदाहरण:**

```python
status = chloros.get_status()
print(f"Running: {status['running']}")
print(f"URL: {status['url']}")
```

***

#### `shutdown_backend()`

बैकएंड बंद करें (यदि एसडीके से शुरू किया गया है)।

**उदाहरण:**

```python
chloros.shutdown_backend()
```

***

### सुविधा सुविधाएँ

#### `process_folder(folder_path, **options)`

किसी फ़ोल्डर को संसाधित करने के लिए एक पंक्ति सुविधा फ़ंक्शन।

**पैरामीटर:**

| पैरामीटर | प्रकार | डिफ़ॉल्ट | विवरण |
| -------------------------------- | -------- | --------------- | -------------------------------- |
| `folder_path` | स्ट्र/पथ | अनिवार्य | छवियों वाले फ़ोल्डर का पथ |
| `project_name` | स्ट्र | स्वचालित रूप से उत्पन्न | प्रोजेक्ट का नाम |
| `camera` | स्ट्र | `None` | कैमरा टेम्पलेट |
| `indices` | सूची | `["NDVI"]` | गणना करने के लिए सूचकांक |
| `vignette_correction` | बूल | `True` | विग्नेट सुधार सक्षम करें |
| `reflectance_calibration` | बूल | `True` | परावर्तन अंशांकन सक्षम करें |
| `export_format` | स्ट्र | «TIFF (16 बिट)» | आउटपुट स्वरूप |
| `mode` | स्ट्र | `"parallel"` | प्रोसेसिंग मोड |
| `progress_callback` | कॉल करने योग्य | `None` | प्रगति कॉलबैक |

**रिटर्न:** `dict` - प्रसंस्करण परिणाम।

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

प्रसंस्करण प्रक्रिया पर पूर्ण नियंत्रण:

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

उड़ान डेटा के एकाधिक सेट संसाधित करें:

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

### उदाहरण 4: अनुसंधान प्रक्रिया का एकीकरण

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

### उदाहरण 5: वैयक्तिकृत प्रगति निगरानी

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

### उदाहरण 7: कमांड लाइन टूल

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

##अपवाद प्रबंधन

एसडीके विभिन्न प्रकार की त्रुटियों के लिए विशिष्ट अपवाद वर्ग प्रदान करता है:

### अपवादों का पदानुक्रम

```python
ChlorosError                    # Base exception
├── ChlorosBackendError        # Backend startup/connection issues
├── ChlorosLicenseError        # License validation issues
├── ChlorosConnectionError     # Network/connection failures
├── ChlorosProcessingError     # Image processing failures
├── ChlorosAuthenticationError # Authentication failures
└── ChlorosConfigurationError  # Configuration errors
```

### अपवादों के उदाहरण

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

### ब्लॉक-मुक्त प्रसंस्करण

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

बड़े डेटा सेट के लिए, बैच प्रक्रिया:

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

### बैकएंड प्रारंभ नहीं होता

**समस्या:** एसडीके बैकएंड प्रारंभ नहीं करता है।

**समाधान:**

1. जांचें कि क्लोरोस डेस्कटॉप स्थापित है:

```python
import os
backend_path = r"C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe"
print(f"Backend exists: {os.path.exists(backend_path)}")
```

2. जांचें कि विंडोज फ़ायरवॉल आपको ब्लॉक नहीं कर रहा है।
3. मैन्युअल बैकएंड रूट आज़माएं:

```python
chloros = ChlorosLocal(backend_exe="C:\\Path\\To\\chloros-backend.exe")
```

***

### लाइसेंस का पता नहीं चला

**समस्या:** एसडीके ने चेतावनी दी है कि लाइसेंस गायब है।

**समाधान:**

1. क्लोरोस, क्लोरोस (ब्राउज़र), या क्लोरोस सीएलआई खोलें और लॉग इन करें।
2. जांचें कि लाइसेंस कैश्ड है:

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

**समस्या:** `ModuleNotFoundError: No module named 'chloros_sdk'`

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

### प्रसंस्करण समयबाह्य

**समस्या:** प्रसंस्करण समय समाप्त।

**समाधान:**

1. प्रतीक्षा समय बढ़ाएँ:

```python
chloros = ChlorosLocal(timeout=120)  # 2 minutes
```

2. छोटे बैचों में प्रक्रिया करें
3. उपलब्ध डिस्क स्थान की जाँच करें
4. सिस्टम संसाधनों की निगरानी करें

***

### पोर्ट पहले से ही उपयोग में है

**समस्या:** बैकएंड पोर्ट 5000 व्यस्त है

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

3. **अनावश्यक अनुक्रमणिका अक्षम करें**

```python
# Only calculate needed indices
chloros.configure(indices=["NDVI"])  # Not all indices
```

4. **एसएसडी पर प्रक्रिया** (एचडीडी नहीं)

***

### मेमोरी अनुकूलन

बड़े डेटा सेट के लिए:

```python
# Process in batches instead of all at once
# See "Memory Management" in Advanced Topics
```

***

### पृष्ठभूमि प्रसंस्करण

अन्य कार्यों के लिए निःशुल्क पायथन:

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

## अक्सर पूछे जाने वाले प्रश्नों

### प्रश्न: क्या एसडीके को इंटरनेट कनेक्शन की आवश्यकता है?

**ए:** केवल प्रारंभिक लाइसेंस सक्रियण के लिए। आपके द्वारा क्लोरोस, क्लोरोस (ब्राउज़र), या क्लोरोस सीएलआई के माध्यम से लॉग इन करने के बाद, लाइसेंस स्थानीय रूप से कैश किया जाता है और 30 दिनों के लिए ऑफ़लाइन काम करता है।

***

### प्रश्न: क्या मैं जीयूआई के बिना सर्वर पर एसडीके का उपयोग कर सकता हूं?

**ए:** हाँ! आवश्यकताएं:

*विंडोज सर्वर 2016 या बाद का संस्करण
*क्लोरोस स्थापित (केवल एक बार)
* किसी भी मशीन पर लाइसेंस सक्रिय (कैश्ड लाइसेंस सर्वर पर कॉपी किया गया)

***

### प्रश्न: डेस्कटॉप, सीएलआई और एसडीके के बीच क्या अंतर है?

| फ़ीचर | डेस्कटॉप जीयूआई | सीएलआई कमांड लाइन | पायथन एसडीके |
| --------------- | ----------- | ---------------- | ----------- |
| **इंटरफ़ेस** | बिंदु और क्लिक करें | आदेश | पायथन एपीआई |
| **के लिए आदर्श** | दृश्य कार्य | स्क्रिप्टिंग | एकीकरण |
| **स्वचालन** | सीमित | अच्छा | उत्कृष्ट |
| **लचीलापन** | बुनियादी | अच्छा | अधिकतम |
| **लाइसेंस** | क्लोरोस+ | क्लोरोस+ | क्लोरोस+ |

***

### प्रश्न: क्या मैं एसडीके के साथ बनाए गए एप्लिकेशन वितरित कर सकता हूं?

**ए:** एसडीके कोड को आपके एप्लिकेशन में एकीकृत किया जा सकता है, लेकिन:

*अंतिम उपयोगकर्ताओं को क्लोरोस स्थापित करना होगा।
*अंतिम उपयोगकर्ताओं के पास सक्रिय क्लोरोस+ लाइसेंस होना आवश्यक है।
*व्यावसायिक वितरण के लिए ओईएम लाइसेंस की आवश्यकता होती है।

OEM पूछताछ के लिए कृपया info@mapir.camera से संपर्क करें।

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

### प्रश्न: क्या मैं प्रोग्रामेटिक रूप से चलने वाली पायथन स्क्रिप्ट से छवियों को संसाधित कर सकता हूं?

**ए:** हाँ! पायथन स्क्रिप्ट के साथ विंडोज टास्क शेड्यूलर का उपयोग करें:

```python
# scheduled_processing.py
from chloros_sdk import process_folder

# Process today's flights
results = process_folder("C:\\Flights\\Today")
```

कार्य शेड्यूलर का उपयोग करके दैनिक निष्पादन शेड्यूल करें।

***

### प्रश्न: क्या एसडीके एसिंक/प्रतीक्षा का समर्थन करता है?

**ए:** वर्तमान संस्करण समकालिक है। अतुल्यकालिक व्यवहार के लिए, `wait=False` का उपयोग करें या इसे एक अलग थ्रेड पर चलाएँ:

```python
import threading

def process_thread():
    chloros.process()

thread = threading.Thread(target=process_thread)
thread.start()

# Continue with other work...
```

***

## मदद लें

### दस्तावेज़ीकरण

* **एपीआई संदर्भ**: यह पृष्ठ

### समर्थन चैनल

* **ईमेल**: info@mapir.camera
* **वेबसाइट**: [https://www.mapir.camera/community/contact](https://www.mapir.camera/community/contact)
* **कीमतें**: [https://cloud.mapir.camera/pricing](https://cloud.mapir.camera/pricing)

### उदाहरण कोड

यहां शामिल सभी उदाहरणों का परीक्षण किया जा चुका है और वे उत्पादन में उपयोग के लिए तैयार हैं। उन्हें कॉपी करें और उन्हें अपने उपयोग के मामले में अनुकूलित करें।

***

## लाइसेंस

**मालिकाना सॉफ्टवेयर** - कॉपीराइट (सी) 2025 एमएपीआईआर इंक।

एसडीके को एक सक्रिय क्लोरोस+ सदस्यता की आवश्यकता है। अनधिकृत उपयोग, वितरण या संशोधन निषिद्ध है।