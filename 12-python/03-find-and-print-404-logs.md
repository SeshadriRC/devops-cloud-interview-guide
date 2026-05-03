## Python script to fetch logs from a log website and print all logs with 404: not found

### Short explanation
This script fetches logs from a publicly available log file, parses it line by line, and prints all entries that include the `404` HTTP status code, which typically means "Not Found".

### Answer
Use Python’s `requests` library to fetch the log file from a public URL and filter for lines containing `404`.

### Detailed explanation (with examples)

We’ll use a real log sample from GitHub that mimics Apache HTTP access logs. Example log source:
`https://raw.githubusercontent.com/elastic/examples/master/Common%20Data%20Formats/apache_logs/apache_logs`

- Install the module

```
pip3 install requests
```

Here’s the complete script:

```
import requests

# Publicly available Apache log sample
log_url = 'https://raw.githubusercontent.com/elastic/examples/master/Common%20Data%20Formats/apache_logs/apache_logs'

try:
    # Fetch the log content
    response = requests.get(log_url)
    response.raise_for_status()
    logs = response.text.splitlines()

    print("Log lines with 404 Not Found:\n")

    # Search for lines with HTTP 404
    for line in logs:
        if ' 404 ' in line:
            print(line)

except requests.exceptions.RequestException as e:
    print(f"Error fetching logs: {e}")
```

### Example matching line from the log:

    216.46.173.126 - - [27/May/2015:10:27:47 +0000] "GET /presentations/logstash-monitorama-2013/images/kibana-search.png HTTP/1.1" 404 146

This script:
- Fetches logs using `requests.get()`
- Splits the logs line by line
- Filters for those containing ` 404 ` to avoid false matches in URLs or timestamps
- Prints all matching lines to the console

You can modify the script to write the filtered lines to a file or analyze other HTTP status codes like `500`, `403`, etc.
