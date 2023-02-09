---
title: "How I Solved the intigriti CURL challenge"
date: 2023-02-09T18:32:20+02:00
draft: false
---

I woke up this morning to something interesting from Initgrit on Twitter. That's a good challenge for reading files from servers

My fast break is over, so I turn on my computer and start looking at the code they provided


![init_twitter](/images/init_twitter.png)
https://twitter.com/intigriti/status/1623663004532281348


## Source Code Review
Basically, it accepts POST method with url and image parameters for 'update picture'.

There are two validations here, one for the host, so the developer makes sure it's localhost, and another for the image, so the extension makes sure it's a real image

If all validations are fine, the backend will send an HTTP request with the value of the url parameter and the image name to upload the image

Alsi the Curl is executing by subprocess.run with Shell=True flag, which means it's use shell

We're good, I thought I could inject some payloads with quotes and get RCE, and say yayyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy

As fast as I figured out that the developer was using shelx.qoute to store all values between single quotes:(

```python
>>> from shlex import quote
>>> command = 'ls -l {}'.format(quote(filename))
>>> print(command)
ls -l 'somefile; rm -rf ~'
>>> remote_command = 'ssh home {}'.format(quote(command))
>>> print(remote_command)
ssh home 'ls -l '"'"'somefile; rm -rf ~'"'"''
```


## Trying to bypass
Ok, I accepted the challenge, and I didn't want to start doing random stuff like fuzzing or anything like that
Let me do a little research about `shelx.quote` and see if there are any new CVEs or warnings about it
According to the official module documentation, there's a warning
![py_warn](/images/py_warn.png)

> [POSIX](http://en.wikipedia.org/wiki/POSIX) is a family of standards, specified by the [IEEE](http://www.ieee.org/portal/site), to clarify and make uniform the application programming interfaces (and ancillary issues, such as command line shell utilities) provided by Unix operating systems.

Then I'll have to try bypassing this on a non-POSIX shell or on Windows
There are two options here, one for Windows and one for testing it on Linux
There's only one problem: Linux is POSIX, and bash is also POSIX.

> Good damn I hate computers

Anyhow, I looked for NON-POSIX shells for Linux and I found the 'fish' shell, it's awesome and non-posix, so let's try it out

After I fuzzed the parameter with singlequates bypassing all my techniques and spent an hour on it, it didn't work that much.
https://github.com/commixproject/commix/blob/master/src/core/tamper/singlequotes.py

And after I saw that the file was in /home/[USER]/hello.png, I twitted about it to make sure it's the right one
![init_twitter2](/images/init_twitter2.png)


### Playing with CURL
I took a look at curl after seeing this tweet and I noticed something
Using the @ sign at the start of a file name can be readed by CURL
As you remember, the developer makes sure the filename is PNG
so PNG is good for me if this server was my friend server that sharing a good memes in the Slack #random channel but here the case is diffrent I want to read real stuff like /etc/passwd

hmmmm what to do now ?
As I started looking at the official documentation of CURL command, I found that with the -F option, I can upload the file name while reading another file, so I can upload a different file and then read a different file
The file would be upload as meme.png, content would be /etc/passwd/ file
This isn't magic, but you can change the file parameters like the content, type, name, etc...

occurding to this page documetion page https://curl.se/docs/manpage.html#-F

> You can add custom headers to the field by setting headers=, like
```
$ curl -F "submit=OK;headers=@headerfile" example.com
```

that's intersting let's try this trick


#### Solving the Chanlleng

To get started, we need to bypass the url localhost check first. I did that with the burpsuite callback and added localhost at the beginning of the host like that: http://localhost.OAST_HOST.com
And let's use headers parameter to bypass the image extensions

Then we'll add a png file to the parameter value after adding the file we want to read in headers

and here we go
![req_1](/images/req_1.png)
the response was `200` but by looking at the OAST log you can see we got the file content finally 
![req_2](/images/req_2.png)


YAYYYY

So I send tweet about it and they said I'm right 
![init_twitter3](/images/init_twitter3.png)

And I receved their message like this


![GIF](https://media.tenor.com/HhkGoOsFrHkAAAAd/jgksjmn-girl.gif)


#### What I learned from that?
a few things
- Think before doing anything
- READ AND READ The offical documentation you will learn a lot

and yeah that's it for today I hope you find this useful

bye ..
