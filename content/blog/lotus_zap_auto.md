---
title: "How to optimize Zapoxy as an authorized crawler for Lotus"
date: 2023-02-06T21:52:54+02:00
draft: false
---

**TLDR** > Optimize Zapoxy as an authorized crawler for Lotus using its scripting and automation framework
 
# List of Content
- Introduction
- What's Zaproxy Scripting Works
- Exporting customs sitemap in JSON output
- Using the Output with Lotus


## Introduction
Zaproxy is a powerful security automation tool with numerous hidden features, in this blog post we will explore its capabilities for authentication, crawling, scoping, and utilizing the results in another tool
Specifically, we will focus on using [Lotus](https://github.com/bugBlocker/lotus), a tool that supports custom sitemap JSON format, to efficiently scan full requests


## What's Zaproxy Scripting 

Zaproxy Scripting empowers users to execute custom scripts within Zaproxy, 
These scripts provide the ability to extend Zaproxy's functionality according to specific requirements, users can automate tasks, employ advanced scanning techniques, manipulate requests and responses, implement custom logic, and gain control over the HTTP sender, authentication, the browser, and more

This flexibility allows users to write scripts that can be executed at any time to control different aspects of their security automation workflow
In this blog, we will utilize the `standalone` script type to export the sitemap in our custom JSON schema

But it's important to note that standalone scripts require manual execution by the user since they lack any triggers, unlike HTTP scripts which can be automatically triggered after sending an HTTP request
As a result of this, we will utilize a Zaproxy plugin called `Automation` which enables the automation of various Zaproxy components through a straightforward YAML file.

## Exporting customs sitemap in JSON output

To export the JSON output, we will modify a standalone script called [Traverse sites tree.js"](https://github.com/zaproxy/community-scripts/blob/main/standalone/Traverse%20sites%20tree.js) from the Zaproxy community scripts repository, we will rewrite it in Python and utilize modules such as json and regex to save the output in JSON format 

```python
"""
This script utilizes the Automation Framework to retrieve Spider Results and pass the requests to another tool.
By: Khaled Nassar (aka nas.k) 
EmaiL knassar702@gmail.com 
Site: (blog.knas.me)
"""

from org.parosproxy.paros.model import Model
from os.path import expanduser
import json
import re

# Convert string headers to dictionary
def extract_headers(headers):
    """
    >>> extract_headers('User-agent: YES')
    {'User-agent':'YES'}
    >>> extract_headers('User-agent: YES\nHacker: 3')
    {'User-agent':'YES','Hacker':'3'}
    """
    headers = headers.replace("\\n", "\n")
    sorted_headers = {}
    matches = re.findall(r"(.*):\s(.*)", headers)
    for match in matches:
        header = match[0]
        value = match[1]
        if value[-1] == ",":
            value = value[:-1]
        sorted_headers[header] = value.split()[0]
    return sorted_headers

def list_children(node, level):
    global reqs
    for j in range(0, node.getChildCount()):
        current_req = {}
        msg = node.getChildAt(j).getHistoryReference().getHttpMessage()
        if msg.isInScope() == False:
            continue
        req_headers = msg.getRequestHeader().toString()
        current_req["method"] = req_headers.split(" ")[0]
        current_req["url"] = req_headers.split(" ")[1]
        current_req["headers"] = extract_headers(req_headers)
        current_req["body"] = msg.getRequestBody().toString()
        reqs.append(current_req)
        list_children(node.getChildAt(j), level + 1)

reqs = []
root = Model.getSingleton().getSession().getSiteTree().getRoot()
list_children(root, 0)

with open("{}/reqs.json".format(expanduser("~")), "w") as f:
    f.write(json.dumps(reqs))
```

To achieve the desired outcome, follow these steps:

- Create a new script in Zaproxy with the script type set to `standalone`.
- Copy and paste the provided code into the script editor.
- Send an HTTP request through Zaproxy.
- Run the script by clicking on the "Run" button.
- After the script execution, you will find the JSON output saved at `/home/{USER}/reqs.json`.

> Note: Replace {USER} in the file path with the appropriate user directory on your system



#### Zaproxy Automation Framework
As mentioned earlier in the blog, you have the ability to control various components of Zaproxy using a simple YAML file. To access this feature, follow the steps below:

- Open Zaproxy and click on the "New" tab.
- Select "Automation" from the options after installing it from the marketplace.
- Choose the desired components, such as Spider and script components.
- In the "Scripts" tab, select the JSON output script we discussed earlier.
- Modify the scope according to your requirements by adjusting the settings.
- Click on the "Run" button to execute the automation process.

[zap_automation](/images/zap_automation.png)

Make sure to refer to the screenshots for a visual reference on how to access the Zaproxy Automation Framework and configure the scope.

![zap_automation_scope](/images/zap_automation_scope.png)

After configuring the automation settings, click on the "Run" button and wait for the process to finish. Once completed, you can find the sitemap of your specified scope at `/home/{USER}/reqs.json`, where `{USER}` represents the appropriate user directory on your system.



## Using the Output with Lotus
you can use the `SCAN_TYPE` number 0 

> Note: this script is not offical, just a simple example to scan url,body parameters in multi-threading

```lua
-- xss.lua
SCAN_TYPE = 0
PAYLOADS = {"\"><img src=x onerror=alert()>","\"><svg/onload=alert()>"}

function scanner(param_type, payload)
    local reqs = full_req:set(param_type, payload)
    for _, req in pairs(reqs) do
        local resp = req:send(req,http)
        local body = resp["body"]
        if str_contains(body,payload) == true then 
                Reports:add {
                    name = "Reflected XSS",
                    url = resp.url,
                    risk = "mideum",
                }
        end
    end
end

function main()
    http:merge_headers(true)
    LuaThreader:iter_scan({full_req:body(), full_req:url()}, PAYLOADS, scanner, 10)
end

```


```bash
$ cat /home/user/reqs.txt | lotus scan xss.lua

[knas@Home ~]$ cat reqs.json | ./lotus scan xss.lua
[INFO] Number of URLs: 2
[INFO] Number of hosts: 0
[INFO] Number of HTTP MSGS: 2
[INFO] Number of paths: 2
[INFO] Number of custom entries: 0

{
  [#] name: "Reflected XSS"

  [#] risk: "mideum"

  [#] url: "http://testphp.vulnweb.com/listproducts.php?cat=1%22%3E%3Cimg+src%3Dx+onerror%3Dalert%28%29%3E"
}

{
  [#] name: "Reflected XSS"

  [#] risk: "mideum"

  [#] url: "http://testphp.vulnweb.com/listproducts.php?cat=1%22%3E%3Csvg%2Fonload%3Dalert%28%29%3E"
}
```


It's important to note that Lotus can be used with Zaproxy in various scan types, including host scan, passive scan, and URL scan, not limited to just full scans 
This flexibility allows you to leverage Lotus's capabilities across different scanning scenarios

In conclusion, by harnessing the power of Zaproxy's authorized crawling capabilities, scripting, and automation framework, combined with Lotus's custom sitemap JSON format, you can optimize your security testing and automation efforts 
Whether it's authenticating, crawling, scoping, or integrating the results with other tools, this comprehensive approach enables you to enhance your security posture effectively

Remember to experiment, explore, and stay updated with the latest features and developments in both Zaproxy and Lotus to make the most of their combined potential in securing your applications and systems
