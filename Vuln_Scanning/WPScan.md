# Use

Run with random user agent agains HTTPS and use api-token (previous registration in https://wpscan.com) for the complete report:

```bash
wpscan --url https://url.com --random-user-agent --api-token xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

# Example

```bash
[+] WordPress version 6.1.1 identified (Latest, released on 2022-11-15).
 | Found By: Rss Generator (Passive Detection)
 |  - https://url.com/feed/, <generator>https://wordpress.org/?v=6.1.1</generator>
 |  - https://url.com/comments/feed/, <generator>https://wordpress.org/?v=6.1.1</generator>
 |
 | [!] 1 vulnerability identified:
 |
 | [!] Title: WP <= 6.1.1 - Unauthenticated Blind SSRF via DNS Rebinding
 |     References:
 |      - https://wpscan.com/vulnerability/c8814e6e-78b3-4f63-a1d3-6906a84c1f11
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-3590
 |      - https://blog.sonarsource.com/wordpress-core-unauthenticated-blind-ssrf/
```

