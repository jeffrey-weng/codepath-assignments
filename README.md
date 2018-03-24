# codepath-assignments

## WordPress Pentesting

### 1. Reflected XSS in URL for Wordpress TwentyFifteen theme

#### Steps to replicate:
- Install WordPress version 4.2 and make sure that the theme is set to TwentyFifteen which, by default, uses an icon font package called "Genericons".
- Enter your WordPress site's Genericons example url, and include an anchor tag that with an XSS exploit. ```http://wpdistillery.vm/wp-content/themes/twentyfifteen/genericons/example.html#1<img src=x onerror= alert('pwned')>```

Result:
![](https://github.com/robeau/codepath-assignments/blob/master/assets/Screen%20Shot%202018-03-17%20at%201.28.07%20PM.png)

#### Vulnerabilities:
This is an example of a **reflected XSS vulnerability**. The **CVE identifier** is `2015-3429`.

#### Vulnerable source code

The specific code that allows this vulnerability is found in /wp-content/themes/twentyfifteen/genericons/example.html, which looks for an glyph id in the url and then inserts it directly into the page's html. There are no checks to make sure the inputted data is actually a legitimate glyph id before inserting the input into the page.

From example.html:

```javascript
// pick random icon if no permalink, otherwise go to permalink
	if ( window.location.hash ) {
		permalink = "genericon-" + window.location.hash.split('#')[1];
		attr = jQuery( '.' + permalink ).attr( 'alt' );
		cssclass = jQuery( '.' + permalink ).attr('class');
		displayGlyph( attr, cssclass );
	} else {
		pickRandomIcon();
	}
  
  function displayGlyph( attr, cssclass ) {

	// set permalink
	var permalink = cssclass.split(' genericon-')[1];
	window.location.hash = permalink;

	// css copy string
	csstext = "content: \'\\" + attr + "';";

	// html copy string
	htmltext = '<span class="' + cssclass + '"></span>';

	// glyph copy string
	glyphtemp = "&#x" + attr + ";";
	jQuery('#temp').html( glyphtemp );
	glyphtext = jQuery('#temp').text();

	// final output
	output = '<div class="' + cssclass + '"></div>'
	+ '<div class="info">'
		+ '<strong>&larr; ' + cssclass.split( ' ' )[1] + '</strong>'
		+ '<a href="javascript:copyToClipboard(csstext, \'css\')">Copy CSS</a>'
		+ '<a href="javascript:copyToClipboard(htmltext, \'html\')">Copy HTML</a>'
		+ '<a href="javascript:copyToClipboard(glyphtext)">Copy Glyph</a>'
	+ '</div>';

	jQuery( '#glyph' ).html( output );

}
```
**Affected Versions:**
This vulnerability affects versions of Genericons prior to 3.3.1, which is used by default in WordPress versions prior to 4.2.2.

### 2. Stored XSS in WordPress comments

#### Steps to replicate:
- Install WordPress version 4.0.
- Create a new post, or go to any existing post.
- Add a comment to the post, with the following body: ```<button onclick="alert('pwned')"></button>```
- Visit the post that has your comment, and click the green button. You should see that the javascript gets executed, resulting in an alert.

Result:
![](https://github.com/robeau/codepath-assignments/blob/master/assets/Screen%20Shot%202018-03-23%20at%2010.30.38%20PM.png)

#### Vulnerabilities:
This is an example of a **stored XSS vulnerability**. The **CVE identifier** is `2015-3438`.

#### Vulnerable source code

The specific code that allows this vulnerability is found in /src/wp-comments-post.php, which handles WordPress comments. There is no validation to make sure the comment doesn't contain malicious HTML before adding it to the database.

**Affected Versions:**
This vulnerability affects versions of WordPress core prior to 4.2.1. I tested it on version 4.0.

### 3. Open Redirect Vulnerability in login URL

#### Steps to replicate:
- Install WordPress version 4.2.4.
- Create a new subscriber, then log out.
- Append ?redirect_to=<some url> to the login page url. ```http://wpdistillery.vm/wp-login.php?redirect_to=http%3A%2F%2Fwpdistillery.vm%2F%3Fpage_id%3D5```
- Log in, and notice that you're immediately reidrected to the page from the previous step.

Result:
Login page with altered url
![](https://github.com/robeau/codepath-assignments/blob/master/assets/Screen%20Shot%202018-03-23%20at%2011.11.39%20PM.png)

After logging in:
![](https://github.com/robeau/codepath-assignments/blob/master/assets/Screen%20Shot%202018-03-23%20at%2011.11.48%20PM.png)

#### Vulnerabilities:
This is an example of an **Open Redirection Vulnerability**. The **CVE identifier** is `2014-2229`.

#### Vulnerable source code

The specific code that allows this vulnerability is found in src/wp-includes/pluggable.php, which validates http redirects. You can see the code changes related to this specific case here: https://core.trac.wordpress.org/changeset/36444

**Affected Versions:**
This vulnerability affects versions of WordPress core prior to 4.2.7. I tested it on version 4.2.4.

