# CSS Injection Attacks
## October 10, 2017

You may have heard of the well known SQL injection attack. Code injection is a type of vulnerability, and it affects far more than just SQL. Another type of injection attack is CSS injection. A lot of novice web developers my not know how an actual CSS injection attack might look when implemented. Thus, they aren’t well prepared to mitigate such a vulnerability.

Presuming you have a web page that is vulnerable to code injection, a web page that has suffered a CSS injection attack might resemble following:

```html
<html>
 <head><title>HTML/CSS Injection Example</title></head>
 <body>

 <!-- This code contains the HTML that the developer
 intended to be displayed -->
 <button class="good">Do Something Good</button>

<!-- This code block contains HTML and CSS that a
 malicious user injected into the page -->
 <style type="text/css">.good {display: none;}</style>
 <button class="evil">Do Something Evil</button>

 </body>
</html>
```