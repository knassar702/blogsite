---
title: "How to set cookies for zaproxy ajax spider manually"
date: 2022-12-17T16:07:24+02:00
description: "How to set cookies for zaproxy ajax spider manually "
tags: ["security","automation","webpentesting"]
type: post
weight: 25
showTableOfContents: true
draft: false
---

Greetings :wave:! In this blog, we will learn how to manually insert cookies into the Ajax spider browser in Zaproxy

#### Why?

You cannot automate the logging process because sometimes the target uses Capatch on the login page, but you can log in and copy the cookies into Zap.

You can control every aspect of Zaproxy using its scripting feature

With Selenium's Firefox or Chrome browser plugin, we can write a script that executes JavaScript code whenever the Selenium tab is opened in Zap

![selenium_tab](https://i.ibb.co/qRWHXWn/image.png)



#### Steps
1. Visit your application and log in
2. With Zap Breakpoint, inspect the request from your browser
3. Copy the value of the Cookie header
4. Execute this Python script and then copy the output into the Zap Javascript code

```bash
$ echo "login=test/test; token=12345" | python3 cookies.py
```


```python
import sys
from http.cookies import SimpleCookie
cookie = SimpleCookie()

for rawdata in sys.stdin:
    rawdata = rawdata.rstrip()
    cookie.load(rawdata)

cookies = {k: v.value for k, v in cookie.items()}

for k, v in cookie.items():
    print(f"window.localStorage.setItem('{k}','{v.value}');",end="")

```


* Replace the `jsUrl` value with your domain target
* Set the Script output in script var
```javascript

var ScriptVars = Java.type('org.zaproxy.zap.extension.script.ScriptVars');
var jsUrl = 'http://localhost:3000';

function browserLaunched(ssutils) {
    logger('browserLaunched ' + ssutils.getBrowserId());
    var wd = ssutils.getWebDriver();
    var url = ssutils.waitForURL(5000);
    if (url.startsWith(jsUrl)) {
        logger('url: ' + url + ' setting token ' + token);
        var script = 'document.cookie = \'token=' + token + '\';\n' +
            'window.localStorage.setItem(\'token\', \'' + token + '\');';
        wd.executeScript(script);
    }
}


// Logging with the script name is super helpful!
function logger() {
	print('[' + this['zap.script.name'] + '] ' + arguments[0]);
}

```
