---

title: "ByteBreach 2025.2 — Token 06: Testament\_of\_Darker\_Hours"

date: 2026-01-02 00:00:00 +0100

categories: \[CTF, Forensics]

tags: \[bytebreach, forensics, pcap, wireshark, syslog, hex]

---



Token 06 writeup from \*\*ByteBreach CTF 2025.2\*\*. This challenge is a network-forensics extraction task: a `.pcapng` capture contains \*\*syslog\*\* entries whose `Msg:` fields are \*\*hex-encoded fragments\*\*. By extracting and decoding those fragments, we recover the next token. All work was performed in an authorized CTF environment.



<!--more-->



> \*\*Info\*\*  

> This post intentionally avoids embedding images to keep GitHub Pages `htmlproofer` checks green.

{: .prompt-info }



\## Challenge Overview



\- \*\*Event:\*\* ByteBreach CTF 2025.2

\- \*\*Category:\*\* Forensics / Network

\- \*\*Status:\*\* Solved

\- \*\*Artifact:\*\* `data.pcapng`

\- \*\*Token:\*\* `Testament\_of\_Darker\_Hours`



The provided Google Sites page linked to a Google Drive download. The downloaded artifact was a packet capture (`.pcapng`). The hint on the page indicated the logs were secured using \*\*hex encoding\*\*, suggesting that some payloads would need to be decoded from hex.



\## Step 1 — Confirm the Artifact



On Windows (PowerShell or CMD), you can quickly identify the file type:



```bat

dir

file data.pcapng

```



On Linux:



```bash

ls -la

file data.pcapng

capinfos data.pcapng

```



\## Step 2 — Open the Capture and Identify the Protocol



Open `data.pcapng` in Wireshark and review the protocol hierarchy:



\- `Statistics` → `Protocol Hierarchy`

\- `Statistics` → `Conversations`



The capture clearly shows \*\*syslog\*\* traffic. In Wireshark, syslog packets typically appear as UDP traffic with syslog dissection, and the decoded view contains a message field that looks like:



```text

Msg: <hex string>

```



\## Step 3 — Filter Syslog and Locate Hex Fragments



In Wireshark, apply a syslog display filter:



```text

syslog

```



Then inspect several packets and focus on the field that begins with `Msg:`. The `Msg:` content is a long hexadecimal string (or multiple hex strings across packets). These are the “patterns” referenced by the hint.



\## Step 4 — Extract the Syslog Messages via CLI (tshark)



If you prefer command-line extraction (repeatable and fast), use `tshark`:



\### Option A: Extract syslog message fields directly



Depending on your Wireshark/tshark build, these fields may exist:



```bash

tshark -r data.pcapng -Y syslog -T fields -e syslog.msg > syslog\_msgs.txt

```



If `syslog.msg` is not available, try:



```bash

tshark -r data.pcapng -Y syslog -T fields -e syslog.message > syslog\_msgs.txt

```



\### Option B: Extract UDP payload and decode later (fallback)



```bash

tshark -r data.pcapng -Y "udp" -T fields -e udp.payload > udp\_payloads\_hex.txt

```



In this challenge, the easiest path was pulling the syslog messages and isolating the `Msg:` hex content.



\## Step 5 — Decode the Hex Payload to Plaintext



Once you have lines that contain the hex strings, decode them to ASCII/UTF-8. Below is a small Python helper that:



1\. Reads extracted syslog text

2\. Finds hex strings (the fragment after `Msg:`)

3\. Hex-decodes and prints the plaintext

4\. Concatenates fragments to reconstruct the final token



Save as `decode\_syslog\_hex.py`:



```python

import re

import sys

from binascii import unhexlify



HEX\_RE = re.compile(r"(?:^|\\b)(\[0-9a-fA-F]{16,})(?:\\b|$)")



def decode\_hex(s: str) -> str:

&nbsp;   s = s.strip()

&nbsp;   if len(s) % 2 == 1:

&nbsp;       s = "0" + s

&nbsp;   try:

&nbsp;       return unhexlify(s).decode("utf-8", errors="replace")

&nbsp;   except Exception:

&nbsp;       return ""



def main(path: str) -> None:

&nbsp;   parts = \[]

&nbsp;   with open(path, "r", encoding="utf-8", errors="ignore") as f:

&nbsp;       for line in f:

&nbsp;           if "Msg:" in line:

&nbsp;               line = line.split("Msg:", 1)\[1]

&nbsp;           m = HEX\_RE.search(line)

&nbsp;           if not m:

&nbsp;               continue

&nbsp;           text = decode\_hex(m.group(1))

&nbsp;           if text:

&nbsp;               parts.append(text)

&nbsp;   print("".join(parts))



if \_\_name\_\_ == "\_\_main\_\_":

&nbsp;   if len(sys.argv) != 2:

&nbsp;       print(f"Usage: {sys.argv\[0]} <syslog\_msgs.txt>", file=sys.stderr)

&nbsp;       sys.exit(1)

&nbsp;   main(sys.argv\[1])

```



Run it:



```bash

python3 decode\_syslog\_hex.py syslog\_msgs.txt

```



In this challenge, the decoded output reconstructs the next token directly.



\## Result



```text

Token 6: Testament\_of\_Darker\_Hours

Next Token: https://sites.gooogle.com/view/hamadryad-onion/

```



\## Notes



\- In network-forensics challenges, “hex encoding” usually means the interesting content is present but not human-readable until decoded.

\- Syslog is a common choice for hiding messages in plain sight because it looks like routine telemetry.

\- If field names differ between Wireshark versions, extract broader fields (like `udp.payload`) and refine with a small parser.



