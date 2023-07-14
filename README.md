# Anthem

Notes!
`10.10.120.63` was the AttackBox
`10.10.11.195` was the target.

## Recon

### nmap

`root@ip-10-10-120-63:~# nmap -A 10.10.11.195`

```
Starting Nmap 7.60 ( https://nmap.org ) at 2023-07-11 21:42 BST
Nmap scan report for ip-10-10-11-195.eu-west-1.compute.internal (10.10.11.195)
Host is up (0.00055s latency).
Not shown: 998 filtered ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=WIN-LU09299160F
| Not valid before: 2023-07-10T20:39:49
|_Not valid after:  2024-01-09T20:39:49
|_ssl-date: 2023-07-11T20:43:23+00:00; 0s from scanner time.
MAC Address: 02:96:AF:6E:F3:F9 (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: specialized
Running (JUST GUESSING): AVtech embedded (87%)
Aggressive OS guesses: AVtech Room Alert 26W environmental monitor (87%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

TRACEROUTE
HOP RTT     ADDRESS
1   0.55 ms ip-10-10-11-195.eu-west-1.compute.internal (10.10.11.195)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 111.49 seconds
```

### gobuster

`root@ip-10-10-120-63:~# gobuster dir -u 10.10.11.195 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`

```
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.11.195
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2023/07/11 21:50:37 Starting gobuster
===============================================================
/search (Status: 200)
/blog (Status: 200)
/sitemap (Status: 200)
/rss (Status: 200)
/archive (Status: 301)
/categories (Status: 200)
/authors (Status: 200)
/Search (Status: 200)
/tags (Status: 200)
/install (Status: 302)
/RSS (Status: 200)
/Blog (Status: 200)
/Archive (Status: 301)
/SiteMap (Status: 200)
/siteMap (Status: 200)
/INSTALL (Status: 302)
/Sitemap (Status: 200)
/1073 (Status: 200)
/Rss (Status: 200)
/Categories (Status: 200)
/1074 (Status: 301)
/1078 (Status: 200)
/Authors (Status: 200)
/1075 (Status: 200)
/1079 (Status: 200)
/1076 (Status: 200)
```
I force quit this one, it was taking a long time.



`root@ip-10-10-120-63:~# gobuster dir -u 10.10.11.195/umbraco -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt`
``` 
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.11.195/umbraco
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2023/07/11 22:23:10 Starting gobuster
===============================================================
/search (Status: 301)
/default (Status: 200)
/members (Status: 301)
/Default (Status: 200)
/assets (Status: 301)
/Search (Status: 301)
/plugins (Status: 301)
/install (Status: 301)
/lib (Status: 301)
/developer (Status: 301)
/Members (Status: 301)
/js (Status: 301)
/create (Status: 301)
/preview (Status: 302)
/config (Status: 301)
/application (Status: 200)
/settings (Status: 301)
/webservices (Status: 301)
/translation (Status: 301)
/INSTALL (Status: 301)
/dashboard (Status: 301)
/views (Status: 301)
/actions (Status: 301)
/Assets (Status: 301)
/controls (Status: 301)
/Translation (Status: 301)
/JS (Status: 301)
/Plugins (Status: 301)
/Developer (Status: 301)
/Application (Status: 200)
/Install (Status: 301)
/Controls (Status: 301)
/Config (Status: 301)
/Preview (Status: 302)
/SEARCH (Status: 301)
/Dashboard (Status: 301)
/WebServices (Status: 301)
/Lib (Status: 301)
/Create (Status: 301)
/SETTINGS (Status: 301)
/DEFAULT (Status: 200)
===============================================================
2023/07/11 22:35:52 Finished
===============================================================
```

### website

In the source code: `THM{G!T_G00D}`

`http://10.10.11.195/authors/jane-doe/` contains flag `THM{L0L_WH0_D15}`

Also contains JD@anthem.com.

http://10.10.11.195/archive/a-cheers-to-our-it-department/ contains a poem which is called Solomon Grundy. I guess the Admin is named Solomon Grundy.

Following the email scheme, the admin email is likely sg@anthem.com

`THM{L0L_WH0_US3S_M3T4}`
`THM{AN0TH3R_M3TA}`
Flags are hiding in meta content of both blog posts.



`http://10.10.11.195/robots.txt`

```
UmbracoIsTheBest!

# Use for all search robots
User-agent: *

# Define the directories not to crawl
Disallow: /bin/
Disallow: /config/
Disallow: /umbraco/
Disallow: /umbraco_client/
```
Possibly a password?


`http://10.10.11.195/umbraco` is a CMS login page.

## Gaining access

sg@anthem.com

UmbracoIsTheBest!

Gets us into the CMS pannel.

`Umbraco version 7.15.4`

Media section has an upload page.


`remmina`

using 

Username: `SG`

Password: `UmbracoIsTheBest!`

Domain: `anthem.com`


`THM{N00T_NO0T}`


## Getting root

Open file explorer, change view to show hidden files. See `C:\backup`

Check `properties`, `Security`, `Users`, `Add`, `Enter object name...`

Set it to `WIN-LU09299160F\Users`

`ChangeMeBaby1MoreTime`

`C:\Users\Administrator\Desktop`

And find `root.txt` 

`THM{Y0U_4R3_1337}`
