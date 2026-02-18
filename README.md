# mesh-data

Public dataset of Mesh Utility scan exports.

Live app: https://mesh-utility.org/
Source app: https://github.com/just-stuff-tm/mesh-utility-tracker

## Repository Structure

```text
mesh-data/
  scans.csv                    # Canonical append-only scan table
  deletions/YYYY-MM-DD/*.json  # Verified deletion audit records
  README.md
  LICENSE
```

## Primary Dataset

The main dataset is `scans.csv` at the repository root.

Current columns:

```text
row_id,radioId,timestamp,datetime_utc,latitude,longitude,altitude,nodeId,rssi,snr,hopLimit
```

Notes:
- `timestamp` is Unix epoch milliseconds (UTC).
- `datetime_utc` is ISO-8601 UTC.
- `rssi` is dBm (more negative is weaker).
- `snr` is dB.
- `hopLimit` may be blank for direct/unknown cases.

## Deletions

Deletion records are written to:

```text
deletions/YYYY-MM-DD/<RADIO_ID>.json
```

These files track user-requested removals handled by the worker.

## Quick Usage

Download the latest dataset:

```bash
git clone https://github.com/just-stuff-tm/mesh-data.git
cd mesh-data
```

Filter by radio ID:

```bash
# Example radio
grep "BFD65811" scans.csv
```

Load with Python:

```python
import pandas as pd

df = pd.read_csv("scans.csv")
print(df.shape)
print(df["radioId"].nunique())
```

## Contributing Data

Data is produced by users who opt in from Mesh Utility:
1. Open https://mesh-utility.org/
2. Connect radio
3. Enable sharing in app settings
4. Scans sync to the backend and append into `scans.csv`

## Support

- App issues: https://github.com/just-stuff-tm/mesh-utility-tracker/issues
- Community: https://discord.gg/Xyhjz7CtuW

## License

MIT. See `LICENSE`.
