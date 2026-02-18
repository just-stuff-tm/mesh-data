# mesh-data

## 💬 Community & Support

<table>
<tr>
<td align="center">

[![Discord](https://img.shields.io/badge/Discord-Join%20Server-5865F2?logo=discord&logoColor=white)](https://discord.gg/Xyhjz7CtuW)

</td>
<td align="center">

[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-Support-FFDD00?logo=buymeacoffee&logoColor=black)](https://www.buymeacoffee.com/Just_Stuff_TM)

</td>
</tr>
</table>


Public dataset of Meshcore network scans collected via [mesh-utility-tracker](https://github.com/just-stuff-tm/mesh-utility-tracker/tree/mesh-utility).
<p align="center">
  <a href="https://mesh-utility.org/"><b>🌐 Live App</b></a>
</p>

## Data Structure

```
mesh-data/
├── scans/
│   ├── 2024-01-15/
│   │   ├── batch-1705334400000.csv  ⭐ Browse/search in GitHub
│   │   ├── batch-1705334400000.json
│   │   ├── batch-1705338000000.csv
│   │   └── batch-1705338000000.json
│   └── 2024-01-16/
│       ├── batch-1705420800000.csv
│       └── batch-1705420800000.json
├── deletions/
│   └── 2024-01-15/
│       └── !abcd1234.json
├── index.csv  ⭐ Daily statistics
└── README.md
```

## Data Formats

### CSV (Recommended for Filtering)

Click any `.csv` file in GitHub to browse with built-in table viewer. Perfect for:
- ✅ Searching by radio ID
- ✅ Filtering by signal strength
- ✅ Sorting by any column
- ✅ Quick visual inspection
- ✅ Download filtered results

**Columns:**
\`radioId, timestamp, datetime_utc, latitude, longitude, altitude, nodeId, rssi, snr, hopLimit\`

**Example CSV row:**
```csv
!abcd1234,1705334400000,2024-01-15T10:00:00.000Z,37.774900,-122.419400,50.0,!def45678,-85,8.5,0
```

**Zero-hop scans:** If `hopLimit` is empty or `0`, it's a direct (zero-hop) connection.

### JSON (For API Integration)

Each batch file contains an array of scan records:

```json
[
  {
    "radioId": "!abcd1234",
    "timestamp": 1705334400000,
    "location": {
      "lat": 37.7749,
      "lon": -122.4194,
      "altitude": 50
    },
    "nodes": [
      {
        "nodeId": "!def45678",
        "rssi": -85,
        "snr": 8.5,
        "hopLimit": 0
      },
      {
        "nodeId": "!ghi91011",
        "rssi": -92,
        "snr": 5.2,
        "hopLimit": 0
      }
    ]
  }
]
```

### Field Descriptions

- **radioId**: Unique identifier for the scanning device (Meshtastic node ID)
- **timestamp**: Unix timestamp in milliseconds (UTC)
- **location**: GPS coordinates of the scan
  - `lat`: Latitude in decimal degrees
  - `lon`: Longitude in decimal degrees
  - `altitude`: Altitude in meters (optional)
- **nodes**: Array of detected mesh nodes
  - `nodeId`: Unique identifier of detected node
  - `rssi`: Received Signal Strength Indicator (dBm)
  - `snr`: Signal-to-Noise Ratio (dB)


## Data Privacy

### User Data Deletion

Users can request deletion of their data by:

1. Opening mesh-utility-tracker Settings
2. Clicking "Delete My Data"
3. Confirming the radio ID
4. Creating a GitHub issue

Deletion records are stored in `deletions/YYYY-MM-DD/`:

```json
{
  "radioId": "!abcd1234",
  "deletedAt": "2024-01-15T10:30:00.000Z",
  "action": "deletion"
}
```

### Data Anonymization

- Radio IDs are anonymized (only public node identifiers visible)
- No personal information is collected
- GPS coordinates are user-provided and can be obfuscated in the client
- All data is public and contributed voluntarily

## Searching & Filtering in GitHub

### Browse CSV Files

1. Navigate to `scans/YYYY-MM-DD/` folder
2. Click any `batch-*.csv` file
3. GitHub renders it as a searchable table
4. Use filter box to search columns
5. Click "Raw" → Save to download filtered data

### Search All Files

**Find scans from specific radio:**
```
!abcd1234 path:scans/ l (CSV)

```python
import pandas as pd
import glob

# Load all CSV files
csv_files = glob.glob('scans/**/*.csv', recursive=True)
dfs = [pd.read_csv(f) for f in csv_files]
all_scans = pd.concat(dfs, ignore_index=True)

print(f"Total scans: {len(all_scans)}")
print(f"Unique radios: {all_scans['radioId'].nunique()}")

# Filter by radio ID
my_scans = all_scans[all_scans['radioId'] == '!abcd1234']

# Filter by signal strength
strong_signals = all_scans[all_scans['rssi'] > -80]

# Export filtered data
my_scans.to_csv('my-filtered-scans.csv', index=False)
```

### Filter with Command Line

```bash
# Find all scans from a radio
grep "!abcd1234" scans/**/*.csv


# Find strong signals (rssi > -70)
awk -F',' '$8 > -70' scans/**/*.csv

# Count scans per day
wc -l scans/*/*.csv

# Combine all CSVs for a date range
cat scans/2024-01-{15,16,17}/*.csv > january-week3.csv
### Download Specific Day (CSV)

```bash
curl -O https://raw.githubusercontent.com/just-stuff-tm/mesh-data/main/scans/2024-01-15/batch-1705334400000.csv
```

### Download All Data

```bash
git clone https://github.com/just-stuff-tm/mesh-data.git
cd mesh-data
```

### Process with Python

```python
import json
import glob

scan_files = glob.glob('scans/**/*.json', recursive=True)
all_scans = []

for file in scan_files:
    with open(file) as f:
        all_scans.extend(json.load(f))

print(f"Total scans: {len(all_scans)}")
print(f"Unique radios: {len(set(s['radioId'] for s in all_scans))}")
```

### Process with JavaScript

```javascript
import fs from 'fs';
import path from 'path';

const scanDir = 'scans';
const allScans = [];

function readScans(dir) {
  const files = fs.readdirSync(dir, { withFileTypes: true });
  
  for (const file of files) {
    const fullPath = path.join(dir, file.name);
    if (file.isDirectory()) {
      readScans(fullPath);
    } else if (file.name.endsWith('.json')) {
      const data = JSON.parse(fs.readFileSync(fullPath, 'utf-8'));
      allScans.push(...data);
    }
  }
}

readScans(scanDir);
console.log(`Total scans: ${allScans.length}`);
```

## Data Quality

- Scans are validated before storage
- Duplicate detection based on radioId + timestamp
- Invalid GPS coordinates are rejected
- RSSI/SNR values must be within reasonable ranges

## Update Frequency

- New batches committed every 20 scans or 5 minutes
- Batch size may vary based on network activity
- Historical data is never modified (append-only)

## License

This dataset is released under CC0 1.0 Universal (Public Domain Dedication).

You are free to:
- Copy, modify, and distribute the data
- Use the data for any purpose, including commercial
- No attribution required (but appreciated!)

## Contributing

Data is automatically collected from mesh-utility-tracker users who opt-in to sharing.

To contribute scans:
1. Install [mesh-utility-tracker](https://mesh-utility-tracker.pages.dev)
2. Connect your Meshcore companion device
3. Enable "Share scan data" in Settings
4. Scans are automatically uploaded to this repository

## Contact

- Report issues: [mesh-utility-tracker/issues](https://github.com/just-stuff-tm/mesh-data/issues)
- Request data deletion: connect your radio that you want deleted and  Click "Delete My Data"

## Statistics

_Updated automatically via GitHub Actions_

- **Total Scans**: TBD
- **Unique Radios**: TBD
- **Date Range**: TBD
- **Last Updated**: TBD
