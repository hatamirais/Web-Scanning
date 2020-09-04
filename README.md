# Web Scanning Try Hack Me Writeup

Hatami Ra'is Bukhari (Althemier)

[Reference Writeup](https://hex-men.tech/rpwebscanning/)

## [Task 1] Pull the lever, Kronk!



## [Task 2] ...I;m supposed to scan with that?

All answer are available with 

```
nikto -H
```

1. First and foremostm what switch do we use to set the target host?

On the nikto manual, there is -host to set target host. But the page ask for 2 character, so -h it is.

```
-h
```

2. Websites don't always properly redirect to their secure transport port and can sometimes have different issues depending on the manner in which they are scanned. How do we disable secure transport?

```
HINT


Secure is the difference between HTTP and HTTPS. What does that difference entail?
```


Have no clue what that mean, Im checking the Writeup reference and i know what im looking for now

```

-nossl : Do not use SSL to connect to the server.

```

3. How about the opposite, how do we force secure transport?

```

-ssl : Only test SSL on the ports specified. Using this option will dramatically speed up requests to HTTPS ports, since otherwise the HTTP request
           will have to timeout first.

```

4. What if we want to set a spesific port to scan?

```
-p
```

Thankfully its the same as how most program

5.  As the web is constantly evolving, so is Nikto. A database of vulnerabilities represents a core component to this web scanner, how do we verify that this database is working and free from error?

'database' is what im looking for in the manual, so its

```
-dbcheck
```

6.  If instructed to, Nitko will attempt to guess and test both files within directories as well as usernames. Which switch and numerical value do we use to set Nikto to enumerate usernames in Apache? Keep in mind, this option is deprecated in favor of plugins, however, it's still a great option to be aware of for situational usage.

Its all in here

```
-mutate
           Specify mutation technique. A mutation will cause Nikto to combine tests or attempt to guess values. These techniques may cause a tremendous
           amount of tests to be launched against the target. Use the reference number to specify the type, multiple may be used:

           1 - Test all files with all root directories

           2 - Guess for password file names

           3 - Enumerate user names via Apache (/~user type requests)

           4 - Enumerate user names via cgiwrap (/cgi-bin/cgiwrap/~user type requests)

           5 - Attempt to brute force sub-domain names, assume that the host name is the parent domain

           6 - Attempt to guess directory names from the supplied dictionary file
```

7. Suppose we know the username and password for a web forum, how do we set Nikto to do a credentialed check? Suppose the username is admin and the password is PrettyAwesomePassword1234

There is no entries that saying how to do this but im trying it with what I found in

```
-id
           ID and password to use for host Basic host authentication. Format is "id:password".
```

with command
```
set admin:PrettyAwesomePassword1234
```

as the aswer, and it worked

8. Let's scan our target machine, what web server do we discover and what version is it?

```
nikto -h TARGET_IP
```

Found

```
Apache/2.4.7
```

9.  This box is vulnerable to very poor directory control due to it's web server version, what directory is indexed that really shouldn't be?

```
OSVDB-3268: /config/: Directory indexing found.
```

10. Nikto scans can take a while to fully complete, which switch do we set in order to limit the scan to end at a certain time?

After several mental breakdown looking through manual and found nothing about time, and then the answer is on -H (same as -help(?))

and the answer is

```
-until
```

11. But wait, there's more! How do we list all of the plugins are available?

```
-list-plugins      List all available plugins, perform no testing
```

12. On the flip-side of the database, plugins represent another core component to Nikto. Which switch do we use to instruct Nikto to use plugin checks to find out of date software on the target host? Keep in mind that when testing this command we need to specify the host we intend to run this against. For submitting your answer, use only the base command with the out of date option. 

Checking
```
nikto -list-plugins
```

and found
```
Plugin: outdated
 Outdated - Checks to see whether the web server is the latest version.
 Written by Sullo, Copyright (C) 2008 Chris Sullo
```

So the answer is
```
-plugins outdated
```

13. Finally, what if we'd like to use our plugins to run a series of standard tests against the target host?

Same as the above, and found
```
Plugin: tests
 Nikto Tests - Test host with the standard Nikto tests
 Written by Sullo, Tautology, Copyright (C) 2008 Chris Sullo
 Options:
  tids: A range of testids that will only be run
  report: Report a status after the passed number of tests
  passfiles: Flag to indicate whether to check for common password files
  all: Flag to indicate whether to check all files with all directories
```

So the answer is
```
-plugins tests
```

## [Task 3] Zip Zap!

1. Let's start simple and launch zap. This can be done in a number of ways (Commands: owasp-zap, zaproxy) or through launching it in the Kali gui. 

2. Launch ZAP, what option to we set in order to specify what we are attacking?

Choosing both automated scan and manual scan will show you the same dialog page

Im choosing automated and the answer is
```
url to attack
```

3. Launch the attack against our target! Throughout the course of this attack you may notice this is very similar to Nikto. Similar to Nessus vs. OpenVAS, Nikto and ZAP and both offer different perspectives on a host and, as such, it's useful to know how to leverage both scanning tools in order to maximize your own visibility in a situation wherein 'noise' doesn't particularly matter.

4. ZAP will discover a file that typically contains pages which well-behaved web indexing engines will read in order to know which sections of a site to avoid. What is the name of this file? (Lucky for us, our scanner isn't what we would call 'well-behaved'!)

The only thing that stand out on scan result is 
```
robots.txt
```

and some how it the answer

5. One entry is included in the disallow section of this file, what is it?

They litteraly tell you what it is

Or click robots.txt and see what there

6. ZAP will find a directory that contains images for our application, what is the path for that directory? (This is what will follows the name/ip of the website)

```
/dvwa/images/
```

7. This website doesn't force a secure connection by default and ZAP isn't pleased with it. Which related cookie is ZAP upset about?

Found 2 
```
PHPSESSID

and

security
```

Neither of those are the anwer.

After new chapter of mental breakdown, checking reference and found the anwer lies in

```
Set-Cookie: security=impossible; httponly
```

8. Featured in various rooms on TryHackMe, Cross-Site Scripting is a vicious attack that is becoming ever more common on the open web. What Alert does ZAP produce to let us know that this site is vulnerable to XSS? Note, there are often a couple warnings produced for this, look for one more so directly related to the web client.

The time when im making this README, the room was 455 days old. So im not sure wether zap has update or what but for sure I cant find anything mention about XSS

So Im checking refrenece (again, yes im noob) and found the answer was

```
Web Browser XSS Protection Not Enabled
```

Again Im sorry

9. The ZAP proxy spider represents the component responsible for 'crawling' the site. What site is found to be out of scope?

Check Spider ---> URL

10. ZAP will use primarily two methods in order to scan a website, which of these two HTTP methods requests content?

```
GET
```

11. Which option attempts to submit content to the website?

```
POST
```
