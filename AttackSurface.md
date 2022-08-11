**Attack Surface defines the whole recon process of penetration testing and web hacking. This very much is controversial due to your scope,
but for the purpose of this document we will pretend everything under a domain is inscope and you will need to apply it if your scope is different**

**SUBDOMAIN ENUMERATION**<br>
<br>
Subfinder - (subfinder.py avaliable on github) python3 subfinder.py -d ENTERDOMAIN -o outfile.txt <br>
amass - amass enum -d DOMAIN >> outfile.txt
assetfinder - assetfinder ENTERDOMAIN | grep ENTERDOMAIN >> outfile.txt <br>
sublist3r.py - sublist3r.py -d ENTERDOMAIN >> outfile.txt <br>
censys manual checking. Go to the legit domain website and check the SSL Cert, use the O or CN and serach via hosts or certs on censys, you can filter via ports <br>
<br>
What all this has done is built you a large file of subdomains owned by the company.

**PORT SCANNING**<br>
<br> Now you have a large list, you'll want to see what is alive on the usual https 443 port. I use httprobe for this <br>
cat outfile.txt | httprobe -p https:443 | tee aliveoutfile.txt <br>
You may want to look for non standard ports, you can do this changing the "443" specified above. <br>
NMAP is also useful, I always do a scan across uncommon web ports <br>
nmap -iL outfile.txt -p 8000,8001,8002,8080,8443,5000,5001,88,9000,9001,9111,9011,9002,9090 | tee aliveports.txt

**FINDING URLS**<br>
hawkfinder
waybackurls
gau
Wordlists locating kali linux
gospider
wfuzz
dirsearch
ffuff
add seclist link here
JavaScript Link Finder


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







