Our webserver has a process that blocks malicious IP addresses, and then releases the block after some period of time. This process generates a log file that contains lines like this:

2021-05-07 00:01:30.034 Block 74.152.237.66
2021-05-07 00:05:05.984 Block 79.118.67.43
2021-05-07 00:05:52.435 Block 183.35.232.214
2021-05-07 00:13:08.376 Release 74.152.237.66
2021-05-07 00:15:23.802 Block 157.152.167.232

Each line contains the date and time the IP address was blocked or released, the word "Block" if the IP address was blocked, or the word "Release" if the IP address was released, and the IP address affected.

We have a log for one particular day at https://public.karat.io/content/test/test_log.txt. We would like to know how many times we blocked IP addresses this day. Write a program that fetches/downloads and reads the given log file, then outputs how many times IP addresses were blocked in this file.

Sample output (in any format)
668
"""

```python
import requests
count=0

response = requests.get('https://public.karat.io/content/test/test_log.txt')

file_txt=response.text

split_file=file_txt.split()

for word in split_file:

     if word == "Block":
         
      count += 1

print("Total count of Block is:", count)
```
