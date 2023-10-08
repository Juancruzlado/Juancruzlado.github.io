# What makes PHP insecure? is it a generalization?

Hi, i'm a PHP Backend dev, 3 years experience, also interested in CyberSec.
Today we will be talking a little bit about PHP, how is it's security in particular and why does it get a bad wrap? 
 PHP is an open-source server-side scripting language that is used to create dynamic and functional web pages. Created in 1994 and having a 
heavy protagonism in the web1 days, it grew into a phenomenon few anticipated. It popularized, and as the globalized world grew, so did the internet, and so 
did www.com pages with html and php, and all kinds of insecure code in the early versions of the language. 
It all depends also on how the developer implements code to solve the problem at hand, without resorting to insecure code, nested in bad practices.
Let's see the following: 

```php
$password = md5(md5(md5(md5($_POST['password'])))) ; 
```
Wrong for manipulating unsanitized data in the super global $_POST variable, utilizing the wrong algorithm for the purpose of storing a password (use Argon2 instead, or research more alternatives)
As of PHP 5.5 we got safe, simple password hashing based on bcrypt, and as of PHP 7.2 we have libsodium. Now in PHP 8 there is  MurmurHash3 and xxHash as new additions, but those are not generally used for password storage.
Sensitive algorithms are internally coded to be constant time now to prevent timing attacks, too. PHP developers don't need to worry about this, as the internals team have handled it for us.

### Also Doesn't Help
The ammount of content (writeups, videos, talks in conferences, etc) of PHP programs getting hacked into, and used as an example of what not to do, is huge.
So if you're constantly bombarded with that rhetoric and you don't double check on it to see if it's true or not, that's when generalizations and misconceptions start to brew and develop.

Example:
https://www.youtube.com/watch?v=FCGvDqcetNY
https://www.youtube.com/watch?v=uWlaBmBrZe8
https://www.youtube.com/watch?v=j-wmhJ8u5Ws
https://www.youtube.com/watch?v=GE2HyC7Gwrs

So what can we do to shift that momentum in the development space?

We must start to inform devs on the proper way to implement PHP code, and use it and capitalize on its strengths without falling to its weaknesses.
Some considerations for PHP security in production include:

Code Quality and Best Practices:
Writing secure, maintainable, and well-structured code is crucial. Following best practices, such as using parameterized queries for database access (to prevent SQL injection), validating user input, and using appropriate error handling, can significantly improve security.

Cross-Site Scripting (XSS) and Cross-Site Request Forgery (CSRF) Prevention:
PHP applications need to implement measures to prevent XSS and CSRF attacks. This includes input validation, output encoding, using HTTP-only cookies, and implementing anti-CSRF tokens.

Authentication and Authorization:
Proper authentication and authorization mechanisms should be implemented to ensure that only authorized users have access to certain functionalities and data.

Secure File Handling:
PHP applications should handle file uploads and access securely to prevent unauthorized access or malicious file uploads.

Data Encryption and Protection:
Sensitive data, such as passwords or credit card information, should be properly encrypted before storage and transmitted securely using HTTPS.

Regular Updates:
Keeping PHP and related frameworks, libraries, and components up to date with the latest security patches is crucial to mitigate vulnerabilities.

Secure Configuration:
Properly configure PHP settings and server configurations to enhance security, for example, by disabling unnecessary features and functions that could be potential security risks.

Input Validation and Sanitization:
Ensure that user input is validated and sanitized to prevent various types of attacks like SQL injection, code injection, and more.

Session Security:
Handle sessions securely by using best practices, such as generating secure session IDs, setting session timeouts, and storing session data securely.

Security Testing and Auditing:
Regular security testing, code reviews, and vulnerability assessments should be conducted to identify and address any security weaknesses or vulnerabilities.

Overall, PHP can be used securely in production, but it requires adherence to best security practices, thorough testing, and a proactive approach to maintaining a secure development and deployment environment.

All of these general tips need to be investigated and looked into properly to be able to implement them into your systems.
For example in a lot of CTF i've solved (ie: https://hackthebox.com) i was able to hack into PHP webapps, in fault of bad sanitization of data, exploiting sql injections to have remote code exec and get potentially a reverse shell, escalate privileges
and get root access. Not only SQL Injections but risky html inputs, malicious file inclusion as a nested php code inside an jpg image, directory listing and exposing sensitive files from the website, etc. 

#3 Cross-Site Request Forgery (CSRF)
CSRF involves tricking a user's browser into making unintended requests on a trusted website where the victim is authenticated. Attackers exploit the trust relationship to perform unauthorized actions on behalf of the victim without their consent.

How to fix it?
To address the PHP vulnerability of Cross-Site Request Forgery (CSRF), it is important to include a unique CSRF token in all forms and AJAX requests

Verify the CSRF token on the server-side before processing the request.
Use techniques like SameSite cookies and anti-CSRF libraries to provide additional protection against CSRF attacks.
Here's an example of a CSRF vulnerability in PHP:

In this example, let's consider a typical GET request for a $5,000 bank transfer:

GET https://abank.com/transfer.do?account=RandPerson&amount=$5000 HTTP/1.1
An attacker can modify the request to transfer $5,000 to their own account. The malicious request would look like this:

GET https://abank.com/transfer.do?account=SomeAttacker&amount=$5000 HTTP/1.1
The attacker can then embed the request into a seemingly harmless hyperlink:

<a href="https://abank.com/transfer.do?account=SomeAttacker&amount=$5000">Click for more information</a>
The next step for the attacker is to distribute this hyperlink via email to a large number of bank clients. When recipients who are logged into their bank accounts click on the link, they unintentionally initiate the $5,000 transfer.

If the bank's website only uses POST requests, it's not possible to create malicious requests using the <a> href tag. However, the attack can still be carried out using a <form> tag.

Here's an example of such a form, which can even be self-submitting:

<body onload="document.forms[0].submit()">
<form id="csrf" action="https://abank.com/transfer.do" method="POST">
<input type="hidden" name="account" value="SomeAttacker"/>
<input type="hidden" name="amount" value="$5000"/>
</form>
</body>
Since the form lacks a submit button, it will be triggered without the user's knowledge and consent. Instead, a single line of JavaScript replaces the button:

document.getElementById('csrf').submit();

Use the language well. 

Sources:
https://www.atatus.com/blog/php-security-threats-and-vulnerabilities/
https://www.reddit.com/r/PHP/comments/qtoeex/is_php_inherently_insecure/?rdt=48289
https://argon2.online/
https://codesigningstore.com/what-is-the-best-hashing-algorithm#:~:text=To%20protect%20passwords%2C%20experts%20suggest,your%20best%20hashing%20algorithm%20choices.
