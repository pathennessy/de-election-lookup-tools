# DE Election Lookup Tools

A collection of command-line tools for looking up Delaware election and legislative district information using the [Delaware FirstMap](https://firstmap.delaware.gov/) Elections Hub.

## Tools

### de_legislator_lookup

Takes a CSV of Delaware addresses and enriches each row with state legislative district information.

## What it returns

For each address the script appends:

| Column | Description |
|---|---|
| `geocoded_address` | Address string matched by the geocoder |
| `geocode_score` | Match confidence (0–100) |
| `senate_district` | State Senate district number |
| `senator` | Current senator name |
| `senator_url` | Link to senator's official page |
| `house_district` | State House district number |
| `representative` | Current representative name |
| `representative_url` | Link to representative's official page |

## Requirements

Two versions are provided: a Python script and a PowerShell script. Use whichever fits your environment.

### Python

Requires Python 3.9+ and the `requests` library:

```bash
pip install requests
```

### PowerShell

Requires PowerShell 5.1+ (Windows) or [PowerShell 7+](https://github.com/PowerShell/PowerShell) (cross-platform). No additional modules needed — it uses the built-in `Invoke-RestMethod` cmdlet.

If you get an execution policy error on Windows, you can either unblock just this file:

```powershell
Unblock-File -Path .\de_legislator_lookup.ps1
```

Or allow locally-created scripts for your user account (run once):

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

## Usage

### Python

```bash
python de_legislator_lookup.py input.csv --output results.csv
```

If your address column is named `address`, `addr`, `street`, `street_address`, `full_address`, `mailing_address`, or `property_address` it will be detected automatically. For any other column name, pass it explicitly:

```bash
python de_legislator_lookup.py input.csv --address-col "Property Address" --output results.csv
```

All options:

```
usage: de_legislator_lookup.py [-h] [--address-col COLUMN] [--output FILE] [--delay SECONDS] input

positional arguments:
  input                 Path to input CSV file

options:
  -h, --help            show this help message and exit
  --address-col, -a COLUMN
                        Name of the column containing addresses (auto-detected if omitted)
  --output, -o FILE     Path for the output CSV (default: output.csv)
  --delay, -d SECONDS   Pause between lookups in seconds, to be polite to the API (default: 0.25)
```

### PowerShell

```powershell
.\de_legislator_lookup.ps1 -InputFile input.csv -OutputFile results.csv
```

With an explicit address column name:

```powershell
.\de_legislator_lookup.ps1 -InputFile input.csv -AddressCol "Property Address" -OutputFile results.csv
```

All options:

| Parameter | Default | Description |
|---|---|---|
| `-InputFile` | (required) | Path to input CSV file |
| `-AddressCol` | auto-detect | Name of the address column |
| `-OutputFile` | `output.csv` | Path for the output CSV |
| `-Delay` | `0.25` | Seconds to pause between lookups |

### Sample input

See `sample_input.csv` for an example. Addresses should be full single-line strings including city and state:

```
Address,Name
23737 Dupont Blvd Georgetown DE 19947,Sussex County Example
800 N French St Wilmington DE 19801,Wilmington Example
35 Loockerman Plaza Dover DE 19901,Kent County Example
```

```bash
# Python
python de_legislator_lookup.py sample_input.csv --output sample_output.csv

# PowerShell
.\de_legislator_lookup.ps1 -InputFile sample_input.csv -OutputFile sample_output.csv
```

## Data source

All district and legislator data comes from the Delaware FirstMap Elections Hub:

- Geocoder: `enterprise.firstmap.delaware.gov` — Delaware FirstMap Locator
- Boundaries: `enterprise.firstmap.delaware.gov` — DE Political Boundaries FeatureServer

Data reflects the current General Assembly. Districts were drawn after the 2020 decennial census and will be updated after the 2030 census.

## AI attribution

This project was created with [Claude](https://claude.ai), an AI assistant made by Anthropic. The code, README, and supporting files were generated in a Cowork session and reviewed by the project author.

## License

MIT — see [LICENSE](LICENSE).
