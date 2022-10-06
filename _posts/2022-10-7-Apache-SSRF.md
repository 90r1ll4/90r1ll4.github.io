---
layout: post
title: >
    Apache HTTP Server-Side Request Forgery (SSRF) (CVE-2021-40438)
tags: [CVE, Apache, SSRF, CVE-2021-40438]
---

# Apache HTTP Server-Side Request Forgery (SSRF) (CVE-2021-40438)
# Cause

The flaw can be triggered only if mod_proxy is in use (e.g. ProxyPass, ReverseProxy is used in the httpd configuration files).
Anyone running Apache HTTP Server to 2.4.48 (or earlier) should [patch their servers to version 2.4.51](https://httpd.apache.org/security/vulnerabilities_24.html#CVE-2021-42013) and either [disable your mod_proxy path](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=115522436) or ensure it’s already disabled until you do patch.
[For more detail visit](https://httpd.apache.org/security/vulnerabilities_24.html).
# Exploit

https://github.com/Kashkovsky/CVE-2021-40438
https://github.com/0day666/Vulnerability-verification
https://github.com/vulhub/vulhub/tree/master/httpd/CVE-2021-40438