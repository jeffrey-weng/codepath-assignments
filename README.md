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
- **Which attacks were easiest to execute? Which were the most difficult?**
The IDOR attack was definitely the easiest, both when finding the vulnerable page and executing the attack. Once I saw that the salespeople were listed by incrementing IDs, I was able to just try the next number until something interesting came up.
The most difficult was CSRF -- fortunately the assignment details were very descriptive.

- **What is a good rule of thumb which would prevent accidentally username enumeration vulnerabilities like the one created here?**
Don't use incrementing numbers when assigning user IDs - instead use a unique uuid. Also, any page that you don't want public should be access restricted. Never trust the user to not try to visit pages, even if they're 'unlisted'.

- **Since you should be somewhat familiar with the CMS and how it was coded, can you think of another resource which could be made vulnerable to an Insecure Direct Object Reference? What code could be removed which would expose it?**
I guess the list of users could be vulnerable if they were publicly accessible the same way that `public/salesperson.php` is.

- **Many SQL Injections use OR as part of the injected code. (For example: ' OR 1=1 --'.) Could AND work just as well in place of OR? (For example: ' AND 1=1 --'.) Why or why not?**
The reason that OR is used is because it only requires one condition to be true, and we can guarantee a true condition with 1=1. You can only use AND if you know for sure that the first argument is true.

- **A stored XSS attack requires patience because it could be stored for months before being triggered. Because of this, what important ingredient would an attacker most likely include in a stored XSS attack script?**
It would be important that the payload be able to sit for a long time without being noticed, so one important ingredient might be to make sure that the attack is hidden well. Another one might be some type of alert to let the attacker know when the attack has been triggered, so they don't miss the window between the attack being triggered and being patched.

- **Imagine that one of your classmates is an authorized admin for the site's CMS and you are not. How would you get them to visit the self-submitting, hidden form page you created in Objective #5 (CSRF)?**
I could send them a link to my form, but tell them it's something else, like an article about cybersecurity or something. I could even edit the metadata on my page to make it show a nice thumbnail and headline in messenger/slack/etc so it looks legit.

- **Compare session hijacking and session fixation. Which attack do you think is easier for an attacker to execute? Why? One of them is much easier to defend against than the other. Which one and why?**
Session fixation seems much easier, because you don't need to find out the user's session ID, you just have to lure them into using yours. It also seems like the easiest to protect against, since you can simply make the session IDs expire.
