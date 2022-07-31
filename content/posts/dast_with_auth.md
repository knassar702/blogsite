---
title: "DAST with authentication"
date: 2022-07-30T16:35:19+02:00
draft: false
toc: false
images: 
tags:
  - automation
---
Hello, sometimes when you trying to automate something like XSS scanning you will find a big wall in your way that is called *Authentication*
there are a lot of methods to use to solve this problem, in this blog I will show can we automate it
first, we start with a small description of the authentication method with the solution in different tools and cases
***

### Form-based (Cookies)
this is the easiest one to automate because it doesn't require many steps just send a request to a custom endpoint with your credentials in the request body and you will the authentication cookies in the `Set-Cookie` response header
easy right? let's automate it

#### >> BurpSuite 

you have to Go to `Application Login` in your scanning dashboard 

![burp_dashboard](/images/burp_dashboard.png) 

the first option requires
the user name and password so burp will struggle to find the right login form, and it will check randomly for some HTML forms
with those input names ( pass, password,uname, username, etc.. ) so
this option is not the best, to solve this problem burp offers the second
option so you can tell burp where's the right form/inputs to choose by
recording your login steps in your browser with [Burp Recorder Chrome
exteion](https://chrome.google.com/webstore/detail/burp-suite-navigation-rec/anpapjclbjicacakeoggghfldppbkepg?hl=en-GB),
after closing your browser you will find JSON output in your clipboard,
take the output and past it here in the second option


#### >> ZAPROXY
zaproxy doesn't have the same feature as Burp Suite Chrome Extention but you can use something similar called ZEST Scripts, all you've to do is 
1. Open ZAPROXY
2. Click on Recorder Icon in the TOP
3. Select your target
4. Click on Record
![zest](/images/zest.png)

5. open your browser and Login
6. Stop recording and Go to Session settings and select the authentication to `Script` and Load your Zest Script
![zest_png](/images/session.png)

this is not the only way you can write a python script if you need extra steps like Handling the CSRF token



#### >> Separate Script
you may need to write your scanning script, so you can do one of these methods, the first one is to send the request to the login endpoint
(you can do this also with a custom token part)

```python
import requests

def main():
    resp = requests.post("http://testphp.vulnweb.com/userinfo.php",data={'uname':'test','pass':'test'})
    if "Logout" in resp.content.decode():
        print(resp.cookies())
    else:
        print("Empty ..")

if __name__ == "__main__":
    main()
```

the second one is using selenium and selecting the login inputs with Xpath or CSS Selectors and matching with the response `Set-Cookie` header

```python
# <input name="username" type="text">
driver.find_element_by_xpath("//input[@name='username']")
```

***

### Custom API key

Some apps use custom HTTP header with JWT keys, you have to send a request to a custom endpoint and you will get the token key in JSON response so you will take the header value and put it in your HTTP request header
```
# Response
HTTP/1.1 200 OK
X-API-KEY: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoia25hcy5tZSJ9.fb8Xti0rji8foYmpY18ewvMWcz0nbxE7FPHajK-7dXc


# Request
GET /user/1 
Host: target.com
X-API-KEY: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuYW1lIjoia25hcy5tZSJ9.fb8Xti0rji8foYmpY18ewvMWcz0nbxE7FPHajK-7dXc
```

#### >> ZAPROXY
Open the `Request Editor` then send the login request to the authentication endpoint, click on the request in the `History tab`, Mark the Token value from the response, and flag it as an Authentication Loggin

![zap_session](/images/zap_session.png)

#### >> BurpSuite 
(doesn't support)


*** 
### SAML (SSO)
Security Authentication Markup Language (SAML) is used to verify ID and authenticate. It connects you (the identity provider) to the service provider by making you correctly verify your credentials


#### >> ZAPROXY
you have to install the `SAML plugin` first from MarketPlace, then follow these steps
- Open the Browser in ZAP for Manual Exploring
- Sign into the app using SAML SSO
- Close Browser
- Find the folder holding the traffic to your app, right-click. Select Include in Context -> Default Context
- Open Tools -> SAML Settings
    - Add or remove the same auth and response attributes as needed
    - save
- View the Alerts tab and finds SAML requests
        - Right-click on the GET with the SAML auth request, selecting SAML Actions View/Resend. 

#### >> Separate Script
you can find a lot of SSO clients in your programming language, here in python you have the [python-saml](https://pypi.org/project/python-saml/) module, follow the demo folders for code examples, or read the docs for more

#### >> BurpSuite 
you have to pay for the Burp Enterprise to access this feature


#### OAUTH
(coming soon)
