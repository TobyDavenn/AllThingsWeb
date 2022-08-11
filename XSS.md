**Cross Site Scriping** <br>
<br>
**first glance methodology**
On a website that allows you to register an account, test every feature avaliable with the payload <!--<h1>test</h1>--> . Do this on every feature that allows input. <br>
Navigate the application and see if theres any places your H1 tag is interpreted. <br>
Via the page source and quickly filter for test and see what encoding is in place on your input. <br>
If tags are filtered but your input is placed within a value="test" can you add test" eventhandler and does the " get filtered? <br>
If your input is directly into a script tag, can you break out the context? E.g. if your input lands within a "var" can you close the var <br>
var srchstring = 'test' - enter test'-alert(1)-' and see what happens. You may need to use a ; after the first ' - test'; -alert(1)-' <br>
Burpsuite allows you to customise scans, create a scan and remove all scans except the scan titled "reflection". Run this on your burp project file to identify all reflected inputs on the web application.


<p align="center">
  <img src="https://pbs.twimg.com/media/Eir5smxXcAcJ4QX?format=jpg&name=large" width="800" height="600" title="hover text">
</p>
