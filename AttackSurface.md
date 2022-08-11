**Attack Surface defines the whole recon process of penetration testing and web hacking. This very much is controversial due to your scope,
but for the purpose of this document we will pretend everything under a domain is inscope and you will need to apply it if your scope is different**

**SUBDOMAIN ENUMERATION**<br>
<br>
Subfinder - (subfinder.py avaliable on github) python3 subfinder.py -d ENTERDOMAIN -o outfile.txt <br>
amass - amass enum -d DOMAIN >> outfile.txt <br>
assetfinder - assetfinder ENTERDOMAIN | grep ENTERDOMAIN >> outfile.txt <br>
sublist3r.py - sublist3r.py -d ENTERDOMAIN >> outfile.txt <br>
censys manual checking. Go to the legit domain website and check the SSL Cert, use the O or CN and serach via hosts or certs on censys, you can filter via ports <br>
<br>
What all this has done is built you a large file of subdomains owned by the company. <br>
<br>

**PORT SCANNING**<br>
<br> 
Now you have a large list, you'll want to see what is alive on the usual https 443 port. I use httprobe for this <br>
cat outfile.txt | httprobe -p https:443 | tee aliveoutfile.txt <br>
You may want to look for non standard ports, you can do this changing the "443" specified above. <br>
NMAP is also useful, I always do a scan across uncommon web ports <br>
nmap -iL outfile.txt -p 8000,8001,8002,8080,8443,5000,5001,88,9000,9001,9111,9011,9002,9090 | tee aliveports.txt
<br>

**FINDING URLS**<br>
I use several tools for this job and my wordlists come from the following link https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content <br>
or I use the locate command on kali linux if using CLI tools "locate Web-Content" <br>
**waybackurls
gau
gospider
wfuzz
dirsearch
ffuff
Feroxbuster
JavaScript Link Finder**
<br>

**Waybackurls** <br> is a tool that will scrape the wayback archive for URLS. We use this with GF patterns read here https://github.com/1ndianl33t/Gf-Patterns <br>
waybackurls DOMAIN | tee waybackurls.txt <br>
**OR** 
<br>
cat aliveoutfile.txt | waybackurls | tee waybackurls.txt
<br>
If you want to search for patterns you can use the following <br>
cat aliveoutfile.txt | waybackurls | gf xss |tee waybackurls.txt <br>
replace "xss" with the following patterns <br>
idor<br>
img-traversal<br>
interestingparams<br>
lfi<br>
rce<br>
redirect<br>
sqli<br>
ssrf<br>
ssti<br>
xss<br>
<br>
This will now give you output on URL endpoints that follow those patterns (does not mean you will have any of the vulns, it means you can now poke params for those.
<br>
The exact same is for GAU <br>
gau DOMAIN <br>
cat outfile.txt | gau | gf PATTERN | tee FILE.txt
<br>

**wfuzz** <br>
wfuzz -w wordlist/general/common.txt http://DOMAIN <br>
https://wfuzz.readthedocs.io/en/latest/user/basicusage.html 
<br>
**dirsearch**
dirsearch -d DOMAIN -w WORDLIST -r (for recursive) <br>
I like to use this on a bash loop if scanning a file containing subdomains so I will use the following bash script <br>
for i in $(cat aliveoutfile.txt)do; subfinder -d $i;done<br>
This one liner can be used on any script just replace "subfinder" with the tool name then the syntax<br>
**feroxbuster**
feroxbuster --url DOMAIN -w WORDLIST <br>
**JavaScript Link Finder** <br>
This is a Burp tool plugin, download this on the BAPP store and it will crawl for further endpoints in Javascript <br>
<br>
You can proxy results from these crawlers into BurpSuite using the -p https://127.0.0.1:8081 syntax (depends what IP your BS and port is active)
<br>




**paramater fuzzing and mining on burp**<br>

**403 bypassing on burpsuite**<br>



**BASH SCRIPTING**<br>



**QUICK WINS WORDLIST**<br>
/phpinfo.php
/info.php
/admin.php
/api/apidocs
/apidocs
/api
/api/v2
/api/v1
/v2
/package.json
/security.txt
/application.wadl
/api/apidocs
/swagger
/swagger-ui
/swagger-ui.html
/swagger/swagger-ui.html
/api/swagger-ui.html
/v1.x/swagger-ui.html
/swagger/index.html
/graphql
/graphiql
/register
/login
/.git
/.env
/.htaccess
/script
/console







