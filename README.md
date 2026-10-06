# Log-Analyser-V4
This is version 4 of the log analyser.
A Python-based security log analyser that processes authentication logs, identifies failed login activity, detects potential brute-force behaviour, and generates a structured JSON security report.
Log Analyser V4 builds on the previous version by adding **JSON report generation**. In addition to displaying the analysis in the console, the program saves the results to `security_report.json`, making the data easier to store, read, and use in other applications.

## Features

* Counts successful login attempts
* Counts failed login attempts
* Tracks failed login attempts by IP address
* Tracks failed login attempts by username
* Parses timestamps from log entries
* Stores failed login events
* Detects potential brute-force activity
* Uses a 60-second detection window
* Identifies IP addresses with 3 or more failed login attempts within the detection window
* Generates a structured JSON security report
* Displays the security report in the console

## How It Works

The analyser reads the security log file line by line.

For each log entry, it extracts the timestamp and converts it into a Python `datetime` object.

The program then identifies whether the event was a successful or failed login.

### Successful Logins

Successful login events are counted and included in the final report.

### Failed Logins

For failed login events, the analyser extracts:

* Username
* IP address
* Timestamp

The failed login is then stored for further analysis.

The program also maintains separate counts for failed logins by IP address and username.

## Brute-Force Detection

V4 uses a simple time-based rule to identify potential brute-force activity.

Failed login events from the same IP address are compared with each other.

An IP address is flagged when **3 or more failed login attempts occur within 60 seconds**.

The detection logic is:

```text
Same IP address
        |
        v
Failed login attempts
        |
        v
3 or more attempts
within 60 seconds
        |
        v
Potential brute-force activity
```

The result is stored as a list of potentially suspicious IP addresses.

This detection method is intended to identify potentially suspicious behaviour. A flagged IP does not necessarily mean that an attack has definitely occurred.

## JSON Report

One of the main additions in V4 is the ability to save the analysis results as a JSON file.

The generated file is:

```text
security_report.json
```

The report contains four main sections:

```json
{
    "summary": {
        "successful_logins": 2,
        "failed_logins": 5
    },
    "failed_logins_by_ip": {
        "192.0.2.15": 3,
        "192.0.2.30": 2
    },
    "failed_logins_by_username": {
        "admin": 3,
        "alex": 2
    },
    "potential_brute_force_ips": [
        "192.0.2.15"
    ]
}
```

### Report Structure

| Field                       | Description                                                       |
| --------------------------- | ----------------------------------------------------------------- |
| `summary`                   | Contains the total number of successful and failed logins         |
| `failed_logins_by_ip`       | Records failed login counts for each IP address                   |
| `failed_logins_by_username` | Records failed login counts for each username                     |
| `potential_brute_force_ips` | Contains IP addresses identified as potential brute-force sources |

The JSON report makes the results easier to process programmatically and provides a persistent copy of the analysis.

## Example Console Output

```text
==============================
       SECURITY LOG REPORT
==============================

Login Summary
------------------------------
Successful logins: 2
Failed logins: 5

Failed Logins by IP
------------------------------
192.0.2.15 : 3
192.0.2.30 : 2

Failed Logins by Username
------------------------------
admin : 3
alex : 2

Potential Brute-Force Activity
------------------------------
192.0.2.15

Report saved to security_report.json
```

## Input Log Format

The analyser expects log entries containing a timestamp, login event, username, and IP address.

For example:

```text
2026-10-06 14:32:10 LOGIN_FAILED user=admin ip=192.0.2.15
2026-10-06 14:32:25 LOGIN_FAILED user=admin ip=192.0.2.15
2026-10-06 14:32:48 LOGIN_FAILED user=admin ip=192.0.2.15
```

The timestamp must follow this format:

```text
YYYY-MM-DD HH:MM:SS
```

The current parser also expects the username and IP address to appear in the positions used by the program.

## Requirements

* Python 3
* A security log file
* Python's built-in `datetime` module
* Python's built-in `json` module

No external Python packages are required.

## Usage

Place the security log file in the project directory and run the analyser.

If using the Python script:

```bash
python log_analyser.py
```

If using the Jupyter Notebook, run the cells containing the analyser code.

After execution, the program will:

1. Read the log file
2. Analyse the login events
3. Count successful and failed logins
4. Group failed logins by IP and username
5. Detect potential brute-force activity
6. Generate `security_report.json`
7. Display the results in the console

## Project Structure

```text
log-analyser/
│
├── log_analyserV4.ipynb
├── securityloginsV2.txt
├── security_report.json
└── README.md
```

The exact filenames can be changed depending on how the project is organised.

## Version 4 Improvements

V4 introduces structured report generation while keeping the analysis functionality from the previous version.

### Previous functionality

* Successful login counting
* Failed login counting
* Failed login analysis by IP
* Failed login analysis by username
* Timestamp processing
* Potential brute-force detection

### New in V4

* JSON report generation
* Persistent security analysis results
* Structured output suitable for further processing
* Clear separation between analysis data and console output

The generated JSON report allows the results to be used by other Python programs, scripts, dashboards, or future versions of the analyser.

## Example Analysis

Using the included report, the analyser identified:

```text
Successful logins: 2
Failed logins: 5
```

The failed attempts were distributed across two IP addresses:

```text
192.0.2.15 : 3
192.0.2.30 : 2
```

The failed attempts were also associated with two usernames:

```text
admin : 3
alex : 2
```

The analyser identified:

```text
192.0.2.15
```

as a potential brute-force source.

The results are also stored in `security_report.json`, allowing the same information to be accessed without rerunning the analysis.

## Limitations

This project is intended as a learning and development project rather than a production security monitoring system.

Current limitations include:

* The log format must follow the expected structure
* The brute-force threshold is fixed at 3 failed attempts
* The detection window is fixed at 60 seconds
* The analyser processes a static log file
* The JSON report is overwritten each time the analyser runs
* Detection is based on predefined rules
* The analyser does not automatically block or respond to suspicious IP addresses

A flagged IP should therefore be considered a **potential security concern**, rather than definitive proof of malicious activity.

## Future Improvements

Possible future improvements include:

* Configurable brute-force thresholds
* Configurable detection windows
* Command-line arguments for selecting log files
* Real-time log monitoring
* CSV report generation
* HTML report generation
* A web-based security dashboard
* More detailed JSON reports
* Detection of multiple usernames targeted from a single IP
* Detection of a single username being targeted from multiple IP addresses
* Improved error handling for invalid log entries
* More advanced anomaly detection
* Automated security alerts

## Disclaimer

This project is intended for educational purposes and for analysing logs that you are authorised to access.

The brute-force detection system uses predefined rules to identify potentially suspicious activity. A detection should not be treated as definitive proof of an attack without further investigation.

## License

This project is licensed under the MIT License.
