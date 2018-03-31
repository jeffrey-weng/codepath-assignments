# Project 8 - Pentesting Live Targets

Time spent: 4 hours spent in total

> Objective: Identify vulnerabilities in three different versions of the Globitek website: blue, green, and red.

The six possible exploits are:
* Username Enumeration
* Insecure Direct Object Reference (IDOR)
* SQL Injection (SQLi)
* Cross-Site Scripting (XSS)
* Cross-Site Request Forgery (CSRF)
* Session Hijacking/Fixation

Each version of the site has been given two of the six vulnerabilities. (In other words, all six of the exploits should be assignable to one of the sites.)

## Blue

Vulnerability #1: __________________

Vulnerability #2: __________________


## Green

### Vulnerability #1: Username Enumeration
Attempt to log in on all three sites, first using a username that doesn't exist, then using a known username (jmonroe99, provided in the assignment page).
Notice that on the Blue and Red sites, the login failure messages are identical in both cases. On the Green site however, a non-existent username show the error in normal font, while existing usernames show the error in bold.
The mistake the developer made was using the wrong class name. For non-existent users, the class is 'failed', while for existing users it is 'failure'.

non-existent user
```html
<span class="failed">Log in was unsuccessful.</span>
```

user exists
```html
<span class="failure">Log in was unsuccessful.</span>
```

![](https://github.com/robeau/codepath-assignments/blob/master/assets/Screen%20Shot%202018-03-30%20at%208.50.26%20PM.png)

Vulnerability #2: __________________


## Red

Vulnerability #1: __________________

Vulnerability #2: __________________


## Notes

Describe any challenges encountered while doing the work
