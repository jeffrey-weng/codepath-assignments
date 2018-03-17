# codepath-assignments

## WordPress Pentesting

### 1. XSS in URL for Wordpress TwentyFifteen theme

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
