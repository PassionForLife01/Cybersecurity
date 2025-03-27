# Traffic Analysis

SQL 注入流量分析：
```python
import re
import os
import pyshark
import urllib.parse


os.chdir(os.path.dirname(__file__))

cap = pyshark.FileCapture(".\\sql.pcapng", display_filter="frame.len == 695 && http.response", tshark_path="D:\\SecTools\\Wireshark\\tshark.exe")

asciis = []

for packet in cap:
    request_uri = urllib.parse.unquote(str(packet.http.request_uri))
    pattern = r"ascii\(substring\(\(select keyid from flag limit 0,1\),(\d+),1\)\)=(\d+)"
    match = re.search(pattern, request_uri)
    # print(match.group(1))
    print(match[2])
    asciis.append(int(match.group(2)))
    # print(str(request_uri))

for ascii in asciis:
    print(chr(ascii), end="")
```

