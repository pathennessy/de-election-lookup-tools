# DE Election Lookup Tools

A collection of command-line tools for looking up Delaware election and legislative district information using the [Delaware FirstMap](https://firstmap.delaware.gov/) Elections Hub.

## Tools

### de_elections_address_lookup

The comprehensive tool. Takes a CSV of Delaware addresses and enriches each row with state legislative, county, Wilmington city council, and school board district information.

### de_legislator_lookup

A focused tool that returns only state legislative district information (Senate and House). Use this if you only need legislator data and want faster, simpler output.

---

## de_elections_address_lookup

### What it returns

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
| `county` | County name |
| `county_district` | County council / levy court district |
| `county_executive` | County Executive (New Castle County only) |
| `county_council_president` | Council or Levy Court president |
| `county_council_member` | District council member or levy court commissioner |
| `county_sheriff` | County Sheriff |
| `clerk_of_peace` | Clerk of the Peace |
| `register_of_wills` | Register of Wills |
| `recorder_of_deeds` | Recorder of Deeds |
| `wilm_city_council_district` | Wilmington City Council district (Wilmington addresses only) |
| `wilm_council_president` | Wilmington City Council President |
| `wilm_council_member` | Wilmington district council member |
| `wilm_council_at_large` | Wilmington at-large council members (semicolon-separated) |
| `school_district` | School district name |
| `school_board_district` | School board nominating district |
| `school_board_members_district` | District school board seat holders (semicolon-separated) |
| `school_board_members_at_large` | At-large school board members (semicolon-separated) |

**Notes on county structure:** Delaware's three counties have different administrative organizations.
- **New Castle** has a County Executive and a County Council with a President.
- **Kent** uses a Levy Court instead of a County Council. The commissioner with `Title = "President"` is mapped to `county_council_president`.
- **Sussex** has a County Council but no County Executive.

`county_executive` will be empty for Kent and Sussex addresses. `wilm_city_council_*` columns will be empty for all non-Wilmington addresses.

### Usage

#### Python

```bash
python de_elections_address_lookup.py input.csv --output results.csv
```

With an explicit address column name:

```bash
python de_elections_address_lookup.py input.csv --address-col "Property Address" --output results.csv
```

All options:

```
usage: de_elections_address_lookup.py [-h] [--address-col COLUMN] [--output FILE] [--delay SECONDS] input

positional arguments:
  input                 Path to input CSV file

options:
  -h, --help            show this help message and exit
  --address-col, -a COLUMN
                        Name of the address column (auto-detected if omitted)
  --output, -o FILE     Output CSV path (default: output.csv)
  --delay, -d SECONDS   Pause between lookups in seconds (default: 0.25)
```

#### PowerShell

```powershell
.\de_elections_address_lookup.ps1 -InputFile input.csv -OutputFile results.csv
```

With an explicit address column name:

```powershell
.\de_elections_address_lookup.ps1 -InputFile input.csv -AddressCol "Property Address" -OutputFile results.csv
```

All options:

| Parameter | Default | Description |
|---|---|---|
| `-InputFile` | (required) | Path to input CSV file |
| `-AddressCol` | auto-detect | Name of the address column |
| `-OutputFile` | `output.csv` | Path for the output CSV |
| `-Delay` | `0.25` | Seconds to pause between lookups |

---

## de_legislator_lookup

### What it returns

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

### Usage

#### Python

```bash
python de_legislator_lookup.py input.csv --output results.csv
```

#### PowerShell

```powershell
.\de_legislator_lookup.ps1 -InputFile input.csv -OutputFile results.csv
```

---

## Requirements

Two versions are provided for each tool: Python and PowerShell. Use whichever fits your environment.

### Python

Requires Python 3.9+ and the `requests` library:

```bash
pip install requests
```

### PowerShell

Requires PowerShell 5.1+ (Windows) or [PowerShell 7+](https://github.com/PowerShell/PowerShell) (cross-platform). No additional modules needed — it uses the built-in `Invoke-RestMethod` cmdlet.

If you get an execution policy error on Windows, you can either unblock just this file:

```powershell
Unblock-File -Path .\de_elections_address_lookup.ps1
```

Or allow locally-created scripts for your user account (run once):

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

---

## Sample input

See `sample_input.csv` for an example. Addresses should be full single-line strings including city and state:

```
Address,Name
23737 Dupont Blvd Georgetown DE 19947,Sussex County Example
800 N French St Wilmington DE 19801,Wilmington Example
35 Loockerman Plaza Dover DE 19901,Kent County Example
```

The address column is auto-detected if named `address`, `addr`, `street`, `street_address`, `full_address`, `mailing_address`, or `property_address`. For any other column name, pass it explicitly with `--address-col` / `-AddressCol`.

```bash
# Python
python de_elections_address_lookup.py sample_input.csv --output sample_output.csv

# PowerShell
.\de_elections_address_lookup.ps1 -InputFile sample_input.csv -OutputFile sample_output.csv
```

---

## Data source

All district and official data comes from the Delaware FirstMap Elections Hub:

- Geocoder: `enterprise.firstmap.delaware.gov` — Delaware FirstMap Locator
- Political boundaries: `enterprise.firstmap.delaware.gov` — DE Political Boundaries FeatureServer (layers 1, 2, 4, 5)
- County officials: `services1.arcgis.com` — County Officials geodatabase

Data reflects current officeholders and district boundaries. State legislative districts were drawn after the 2020 decennial census.

---

## AI attribution

This project was created with [Claude](https://claude.ai), an AI assistant made by Anthropic. The code, README, and supporting files were generated in a Cowork session and reviewed by the project author.

## License

MIT — see [LICENSE](LICENSE).
