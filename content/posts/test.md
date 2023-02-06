---
title: "Lotus"
date: 2019-08-04T17:55:28+08:00
description: "this blog is about SQL Injection on nokia subdomain"
tags: ["bugbounty","security","webpentesting"]
type: post
weight: 25
showTableOfContents: true
---
in lotus scripts the way to create your a report to save the scanner results into JSON, you've to chose the report type between 
- Cve
- Vuln

so what is the diffrence here ?
well for each script type you will need a custom faileds to save/include in your report, like for CVE scanning you have to save a few things In addition to the vulnerability information is the matchers, when you have a CVE-xxx-xxx for example and this CVE requires a GET request to `/admin/user?search=<img src=x onerror=alert()>`
and to catch this bug you've to match in response body and the response headers but you've to mention that in the report, so in lotus CVE report you can add where the payload matched in 
- response body
- response headers
- full raw resposne  
- request body
- request headers
- full raw request
- General (use it for that cases the vuln match is located in differnet ways)

and for vulnerability scanner you can set a normal data


#### Lua Code

for Cve Report you can use the global Class `CveReport` 
lets take the CVE-2022-0271 as an example

```lua
local function send_report(url)
    CveReport:setName("CVE-2022-0271")
    CveReport:setDescription("WordPress LearnPress plugin before 4.1.6 contains a cross-site scripting vulnerability. It does not sanitize and escape the lp-dismiss-notice before outputting it back via the lp_background_single_email AJAX action.")
    CveReport:setRisk("medium")
    CveReport:setUrl(url)
end


function main()
	-- SOME SCANNING LOGIC
	send_report("http://target.com/wp-admin/search?u=<img src=x onerror=alert()>")
end

```




 









o
