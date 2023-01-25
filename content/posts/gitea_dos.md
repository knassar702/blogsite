---
title: "With a single request, you can kill any Gitea server"
date: 2023-01-25T11:37:10+02:00
draft: false
toc: false
images:
tags:
  - cybersecurity
  - bugbounty
  - dos
---

Hey :smiley: 

in this blog I'm going to tell you how I was able to kill/dos any Gitea server
I discovered this bug in Gitea version 1.14.2 in 2021

which was fixed in 1.14.3 by the Gitea team \
Please note that I wasn't targeting Gitea at all, I was doing some bug bounty hunting on a private target
That target's staging-dev.target.com has gitea on it 

and I thought, "Okay, I think there's CVES for that product" \
Unfortunately, the POC isn't included in the CVE report (only the PR on github) 


so I decided to play around and see if there's any private keys in the repos on this gitea server (I didn't find any)


and my mind give me an idea, what If I created an account on this server?
I thought I could find out more because maybe there's an option that lets the user make the repositories private for people with an account on Gitea 

After creating my account, I didn't see any difference 
so the last option I have is to send emojis to Gitea's issues page for trolling or joking


Then I opened Zaproxy and sent many of these emojis to the issues body API in one request 
Afterwards, I noticed that subdomain response time is going up
I got a 500 error, and boom, the server wasn't responding when I try to visit the gitea issue that I opened


![Yayy](https://media.tenor.com/oo6YIBG8_xIAAAAd/excited-hockey.gif)



When I reported this bug to my target's security team, 
the response was too fast, saying "Sorry, this is not a part of our products. Could you please forward this report to the Gitea security team"
But what's good is they provide me with more info to make a good report for the Gitea security team like logs, screenshots of CPU usage, etc.
![cpu](/images/gitea_cpu.png)

When someone visits my gitea issue containing the emojis, the CPU usage gets high. That's because there's too much **text formatting/processing** happening in the back-end for the comment 
After I reported the bug to Gitea, they asked me some questions about the environment and if I could reproduce it at the last version (it's on try.gitea.io)

I've included it in the report 
- Gitea version : 1.14.2
- Git version:  2.32.0
- Operating system: Linux
- Database: MySQL
- Can you reproduce the bug at https://try.gitea.io: Yes (https://try.gitea.io/dtestman/test/issues/1)

They respond after 2 days with validating the bug and a PR, and my name is on the release page for my next release :smile:

![gitea_mail](/images/gitea_mail.png)

here's the release page: https://blog.gitea.io/2021/06/gitea-1.14.3-is-released/

and the fix PR: https://github.com/go-gitea/gitea/pull/16185


See you 

![bye](https://media.tenor.com/b8DvJ53k8soAAAAd/rat-bye.gif)
