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

### Vulnerability #1: SQL Injection (SQLi)
I was able to get a response indicating that SQL injection is possible by replacing the id parameter in the salesperson page with malicious SQL. After sending a GET request to `https://35.226.188.43/blue/public/salesperson.php?id=%27%20OR%201=1%20#%20`, I received a result that says "Database query failed".

![](https://github.com/robeau/codepath-assignments/blob/master/assets/Screen%20Shot%202018-03-30%20at%209.27.29%20PM.png)

### Vulnerability #2: Session Hijacking/Fixation
I logged into the Blue site in Google Chrome and then visited the session changer tool to get a valid session ID. Then I went to the Blue site in Firefox, but didn't log in. Instead I tried visiting `staff/index.php`, which wasn't accessible. I then went into Burp and got the attempt from my HTTP history, and sent it to Repeater, where I modified the session ID, then resent the request. I was then able to see the logged in staff page.

![](https://github.com/robeau/codepath-assignments/blob/master/assets/Screen%20Shot%202018-03-30%20at%2010.33.13%20PM.png)


## Green

### Vulnerability #1: Username Enumeration
I attempted to log in on all three sites, first using a username that doesn't exist, then using a known username (jmonroe99, provided in the assignment page).
I noticed that on the Blue and Red sites, the login failure messages are identical in both cases. On the Green site however, a non-existent username show the error in normal font, while existing usernames show the error in bold.
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

### Vulnerability #2: Cross-Site Scripting (XSS)
I was able to use the contact form to send malicious html as feedback. After sending the form, log in and click feedback to see the javascript alert.

![](https://github.com/robeau/codepath-assignments/blob/master/assets/Screen%20Shot%202018-03-30%20at%209.50.39%20PM.png)


## Red

### Vulnerability #1: Insecure Direct Object Reference (IDOR)
The 'Find a Salesperson' page has links for each salesperson. We can see in the url that each person is identified by an id number. By incrementing the ID, we can find salespeople who weren't included in the main site.

![](https://github.com/robeau/codepath-assignments/blob/master/assets/Screen%20Shot%202018-03-30%20at%209.23.16%20PM.png)

The Blue and Green sites avoid this vulnerability by issuing a 302 Redirect on unlisted pages:

![](https://github.com/robeau/codepath-assignments/blob/master/assets/Screen%20Shot%202018-03-30%20at%209.19.41%20PM.png)

### Vulnerability #2: Cross-Site Request Forgery (CSRF)
To exploit the CSRF vulnerability on the Red site, I uploaded a form to my github page at https://robeau.github.io/csrftwo/, then visited the site while logged in as pperson. My form causes the first name of the user with id=1 to be changed, and redirects me to the user's page.

![](https://github.com/robeau/codepath-assignments/blob/master/assets/Screen%20Shot%202018-03-30%20at%2010.14.39%20PM.png)


## Notes

Describe any challenges encountered while doing the work
