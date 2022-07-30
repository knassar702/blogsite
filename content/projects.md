+++
title = "Projects"
date = "2022-07-16"
aliases = ["myprojects","projects"]
[ author ]
  name = "Khaled Nassar"
+++

### >>  [Scant3r](https://github.com/knassar702/scant3r)
this is a module-based web automation tool that I made for saving my scripting time by providing some utilizes that every web pentester needs in his automation script instead of focusing on ( logger, parsers, output function, cmd args, multi-threading), just write the logic of your scanning idea with scant3r utils without caring about these things, you can find callback/parsing/logging utils and output functions, Also we will add Restful API soon
what if you need to add a new Command option to scant3r for your script?
easy without writing any code just open the `conf/opts.YAML file and you will find all options of scant3r so you can change and add what you want;D

![scant3r_png](https://user-images.githubusercontent.com/45688522/170871350-80113c02-5733-4d04-9f09-c33bef80c2bf.png)


***


### >>  [BlackLock Log4j Scanner](https://blacklock.io/log4j/)
log4j scanner crawls your web application to identify the URLs, parameters, request headers and probe them with log4j attack payloads to detect the vulnerability


*** 


### >>  [Hacking-lab](https://github.com/knassar702/hacking-lab)
Small Vulnerable Web application that can be used for training
- path traversal
- XSS Stored/Rfelected
- SQLI
- SSTI
- CSRF
- SSRF 
- RCE
- Broken Session Management
- open redirect


***
### >>  [LorSrf](https://github.com/knassar702/lorsrf)
lorsrf is just a web pentesting tool that i wrote in Rust for find the parameters that can be used to find SSRF or Out-of-band resource load by adding OAST host like Burp Collaborator to the parameter value

![lorsrf_png](https://github.com/knassar702/lorsrf/raw/master/.github/workflows/screen.png)


***
### >>  [Hacks.rs](https://github.com/knassar702/hacks)
re-write TomNomNom tools in Rust
