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

{% hint style="info" %}
**First-Time Setup**: Before using the SDK, activate your Chloros+ license by opening Chloros, Chloros (Browser) or Chloros CLI and logging in with your credentials. This only needs to be done once.
{% endhint %}

### Basic Usage

Process a folder with just a few lines:

```python
from chloros_sdk import process_folder

# One-line processing
results = process_folder("C:\\DroneImages\\Flight001")
```

### Full Control

For advanced workflows:

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

## Installation Guide

### Prerequisites

Before installing the SDK, ensure you have:

1. **Chloros Desktop** installed ([डाउनलोड करें](download.md))
2. **Python 3.7+** installed ([python.org](https://www.python.org))
3. **Active Chloros+ license** ([अपग्रेड करें](https://cloud.mapir.camera/pricing))

### Install via pip

**Standard installation:**

```bash
pip install chloros-sdk
```

**With progress monitoring support:**

```bash
pip install chloros-sdk[progress]
```

**Development installation:**

```bash
pip install chloros-sdk[dev]
```

### Verify Installation

Test that the SDK is installed correctly:

```python
import chloros_sdk
print(f"Chloros SDK version: {chloros_sdk.__version__}")
```

***

## First-Time Setup

### License Activation

The SDK uses the same license as Chloros, Chloros (Browser), and Chloros CLI. Activate once via the GUI or CLI:

1. Open **Chloros or Chloros (Browser)** and login on the User <img src=".gitbook/assets/icon_user.JPG" alt="" data-size="line"> tab. Or, open the **CLI**.
2. Enter your Chloros+ credentials and log in
3. License is cached locally (persists across reboots)

{% hint style="success" %}
**One-Time Setup**: After logging in via the GUI or CLI, the SDK automatically uses the cached license. No additional authentication needed!
{% endhint %}

### Test Connection

Verify the SDK can connect to Chloros:

```python
from chloros_sdk import ChlorosLocal

# Initialize SDK (auto-starts backend if needed)
chloros = ChlorosLocal()

# Check status
status = chloros.get_status()
print(f"Backend running: {status['running']}")
```

***

## API Reference

### ChlorosLocal Class

Main class for local Chloros image processing.

#### Constructor

```python
ChlorosLocal(
    api_url="http://localhost:5000",     # Backend URL
    auto_start_backend=True,             # Auto-start backend if not running
    backend_exe=None,                    # Backend path (auto-detected)
    timeout=30,                          # Request timeout (seconds)
    backend_startup_timeout=60           # Backend startup timeout
)
```

**Parameters:**

| Parameter                 | Type | Default                   | Description                           |
| ------------------------- | ---- | ------------------------- | ------------------------------------- |
| `api_url`     | float    | `"http://localhost:5000"` | URL of local Chloros backend          |
| `ऑटो_स्टार्ट_बैकएंड`      | bool | `बैकएंड_एक्सई`     | float    | `कोई नहीं`             | str  | `टाइमआउट`                      | Request timeout in seconds            |
| `30`                 | int  | `बैकएंड_स्टार्टअप_टाइमआउट`                      | Request timeout in seconds            |
| `60` | int  | ```                      | Timeout for backend startup (seconds) |

**Examples:**

```python
# Default (auto-start backend)
chloros = ChlorosLocal()

# Connect to running backend
chloros = ChlorosLocal(auto_start_backend=False)

# Custom backend path
chloros = ChlorosLocal(backend_exe="C:/Custom/chloros-backend.exe")

# Custom timeout
chloros = ChlorosLocal(timeout=60)
`क्रिएट_प्रोजेक्ट(प्रोजेक्ट_नाम, कैमरा=कोई नहीं)`

***

### Methods

#### `प्रोजेक्ट_नाम`

Create a new Chloros project.

**Parameters:**

| Parameter      | Type | Required | Description                                              |
| -------------- | ---- | -------- | -------------------------------------------------------- |
| ` - Compressed output

**Available Indices:**

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2, and more.

**Example:**

` | str  | Yes      | Name for the project                                     |
| ```       | str  | No       | Camera template (e.g., "Survey3N\_RGN", "Survey3W\_OCN") |

**Returns:** ```     | float    | `import_images(folder_path, recursive=False)`python
# Basic project
chloros.create_project("DroneField_A")

# With camera template
chloros.create_project("DroneField_A", camera="Survey3N_RGN")
`फ़ोल्डर_पथ`

***

#### ` - Compressed output

**Available Indices:**

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2, and more.

**Example:**

`

Import images from a folder.

**Parameters:**

| Parameter     | Type     | Required | Description                        |
| ------------- | -------- | -------- | ---------------------------------- |
| ``` | str/Path | Yes      | Path to folder with images         |
| ```     | float    | `कॉन्फ़िगर(**सेटिंग्स)` - Import results with file count

**Example:**

`विग्नेट_करेक्शन`

Get backend status information.

**Returns:** `प्रतिबिंब_अंशांकन` - Backend status

**Example:**

`सूचकांक`

Configure processing settings.

**Parameters:**

| Parameter                 | Type | Default                 | Description                     |
| ------------------------- | ---- | ----------------------- | ------------------------------- |
| `निर्यात_प्रारूप`       | Progress callback function(progress, msg) |
| `पीपीके`     | bool | `कस्टम_सेटिंग्स`                  | Enable vignette correction      |
| `` | bool | ``                  | Enable reflectance calibration  |
| ``                 | list | ``                  | Vegetation indices to calculate |
| ```           | str  | "TIFF (16-bit)"         | Output format                   |
| ```     | float    | `प्रक्रिया(मोड=समानांतर', प्रतीक्षा=सही, प्रगति_कॉलबैक=कोई नहीं)'

प्रोजेक्ट छवियों को संसाधित करें.

**पैरामीटर:**

| पैरामीटर | प्रकार | डिफ़ॉल्ट | विवरण |
| ------------------- | -------- | ------------ | ----------------------------------------- |
| `                 | Enable PPK corrections          |
| `     | float    | `         | dict | ` | प्रसंस्करण मोड: "समानांतर" या "धारावाहिक" |
| `                  | Advanced custom settings        |

**Export Formats:**

* ` | बूल | 'सच' | पूरा होने की प्रतीक्षा करें |
| ` - Recommended for GIS/photogrammetry
* ` | कॉल करने योग्य | 'कोई नहीं' | प्रगति कॉलबैक फ़ंक्शन (प्रगति, संदेश) |
| ` - Scientific analysis
* ` | तैरना | ` - Visual inspection
* ` | प्रगति के लिए मतदान अंतराल (सेकंड) |

**रिटर्न:** ` - Compressed output

**Available Indices:**

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2, and more.

**Example:**

` - प्रसंस्करण परिणाम

{% संकेत शैली = "चेतावनी" %}
**समानांतर मोड**: क्लोरोस+ लाइसेंस की आवश्यकता है। स्वचालित रूप से आपके सीपीयू कोर (16 श्रमिकों तक) को स्केल करता है।
{%अंतसंकेत%}

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
```     | float    | `get_config()`

Process the project images.

**Parameters:**

| Parameter           | Type     | Default      | Description                               |
| ------------------- | -------- | ------------ | ----------------------------------------- |
| ` - Compressed output

**Available Indices:**

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2, and more.

**Example:**

`              | str      | ``` | Processing mode: "parallel" or "serial"   |
| ```     | float    | `get_status()`       | Wait for completion                       |
| ` - Compressed output

**Available Indices:**

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2, and more.

**Example:**

` | callable | ```       | Progress callback function(progress, msg) |
| ```     | float    | `शटडाउन_बैकएंड()`        | Polling interval for progress (seconds)   |

**Returns:** ``` - Processing results

{% hint style="warning" %}
**Parallel Mode**: Requires Chloros+ license. Automatically scales to your CPU cores (up to 16 workers).
{% endhint %}

**Example:**

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
`प्रक्रिया_फ़ोल्डर(फ़ोल्डर_पथ, **विकल्प)`

***

#### `फ़ोल्डर_पथ`

Get current project configuration.

**Returns:** `प्रोजेक्ट_नाम` - Current project configuration

**Example:**

`सूचकांक`python
config = chloros.get_config()
print(config['Project Settings'])
`["एनडीवीआई"]`

***

#### `विग्नेट_करेक्शन`

Get backend status information.

**Returns:** `प्रतिबिंब_अंशांकन` - Backend status

**Example:**

`निर्यात_प्रारूप`       | Progress callback function(progress, msg) |
| `                 | Enable PPK corrections          |
| `     | float    | `         | dict | `

Shutdown the backend (if started by SDK).

**Example:**

` - Recommended for GIS/photogrammetry
* `python
chloros.shutdown_backend()
` - Compressed output

**Available Indices:**

NDVI, NDRE, GNDVI, OSAVI, CIG, EVI, SAVI, MSAVI, MTVI2, and more.

**Example:**

`

***

### Convenience Functions

#### ```

One-line convenience function to process a folder.

**Parameters:**

| Parameter                 | Type     | Default         | Description                    |
| ------------------------- | -------- | --------------- | ------------------------------ |
| ```             | str/Path | Required        | Path to folder with images     |
| ```            | str      | Auto-generated  | Project name                   |
| ```                  | str      | ```          | Camera template                |
| ```                 | list     | ```      | Indices to calculate           |
| ```     | bool     | ```          | Enable vignette correction     |
| ``` | bool     | ```          | Enable reflectance calibration |
| ```           | str      | "TIFF (16-bit)" | Output format                  |
| ```                    | str      | ```    | Processing mode                |
| ```       | callable | ```          | Progress callback              |

**Returns:** ``` - Processing results

**Example:**

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

## Context Manager Support

The SDK supports context managers for automatic cleanup:

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

## Complete Examples

### Example 1: Basic Processing

Process a folder with default settings:

```python
from chloros_sdk import process_folder

# Process with default settings
results = process_folder("C:\\Datasets\\Field_A_2025_01_15")

print(f"Processing complete: {results}")
```

***

### Example 2: Custom Workflow

Full control over processing pipeline:

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

### Example 3: Batch Processing Multiple Folders

Process multiple flight datasets:

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

### Example 4: Research Pipeline Integration

Integrate Chloros with data analysis:

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

### Example 5: Custom Progress Monitoring

Advanced progress tracking with logging:

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

### Example 6: Error Handling

Robust error handling for production use:

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

### Example 7: Command-Line Tool

Build a custom CLI tool with the SDK:

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

**Usage:**

```bash
python my_processor.py "C:\Flight001" "C:\Flight002" --indices NDVI NDRE GNDVI
`ModuleNotFoundError: 'क्लोरोस_एसडीके' नाम का कोई मॉड्यूल नहीं`

***

## Exception Handling

The SDK provides specific exception classes for different error types:

### Exception Hierarchy

```python
ChlorosError                    # Base exception
├── ChlorosBackendError        # Backend startup/connection issues
├── ChlorosLicenseError        # License validation issues
├── ChlorosConnectionError     # Network/connection failures
├── ChlorosProcessingError     # Image processing failures
├── ChlorosAuthenticationError # Authentication failures
└── ChlorosConfigurationError  # Configuration errors
```

### Exception Examples

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

## Advanced Topics

### Custom Backend Configuration

Use a custom backend location or configuration:

```python
chloros = ChlorosLocal(
    backend_exe="C:\\Custom\\chloros-backend.exe",
    api_url="http://localhost:5001",  # Custom port
    timeout=60,                        # Longer timeout
    backend_startup_timeout=120        # 2 minutes startup
)
```

### Non-Blocking Processing

Start processing and continue with other tasks:

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

### Memory Management

For large datasets, process in batches:

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

## Troubleshooting

### Backend Not Starting

**Issue:** SDK fails to start backend

**Solutions:**

1. Verify Chloros Desktop is installed:

```python
import os
backend_path = r"C:\Program Files\MAPIR\Chloros\resources\backend\chloros-backend.exe"
print(f"Backend exists: {os.path.exists(backend_path)}")
```

2. Check Windows Firewall isn't blocking
3. Try manual backend path:

```python
chloros = ChlorosLocal(backend_exe="C:\\Path\\To\\chloros-backend.exe")
```

***

### License Not Detected

**Issue:** SDK warns about missing license

**Solutions:**

1. Open Chloros, Chloros (Browser) or Chloros CLI and login.
2. Verify license is cached:

```python
from pathlib import Path
import os

# Check cache location (Windows)
cache_path = Path(os.getenv('APPDATA')) / 'Chloros' / 'cache'
print(f"Cache exists: {cache_path.exists()}")
```

3. Contact support: info@mapir.camera

***

### Import Errors

**Issue:** ```

**Solutions:**

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

### Processing Timeout

**Issue:** Processing times out

**Solutions:**

1. Increase timeout:

```python
chloros = ChlorosLocal(timeout=120)  # 2 minutes
```

2. Process smaller batches
3. Check available disk space
4. Monitor system resources

***

### Port Already in Use

**Issue:** Backend port 5000 occupied

**Solutions:**

```python
# Use different port
chloros = ChlorosLocal(api_url="http://localhost:5001")
```

Or find and close conflicting process:

```powershell
# PowerShell
Get-NetTCPConnection -LocalPort 5000
```

***

## Performance Tips

### Optimize Processing Speed

1. **Use Parallel Mode** (requires Chloros+)

```python
chloros.process(mode="parallel")  # Up to 16 workers
```

2. **Reduce Output Resolution** (if acceptable)

```python
chloros.configure(export_format="PNG (8-bit)")  # Faster than TIFF
```

3. **Disable Unnecessary Indices**

```python
# Only calculate needed indices
chloros.configure(indices=["NDVI"])  # Not all indices
`wait=False`

4. **Process on SSD** (not HDD)

***

### Memory Optimization

For large datasets:

```python
# Process in batches instead of all at once
# See "Memory Management" in Advanced Topics
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
