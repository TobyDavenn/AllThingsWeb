**Attack Surface defines the whole recon process of penetration testing and web hacking. This very much is controversial due to your scope,
but for the purpose of this document we will pretend everything under a domain is inscope and you will need to apply it if your scope is different**
<br>
<br>

**SUBDOMAIN ENUMERATION**<br>
Subfinder - (subfinder.py avaliable on github) python3 subfinder.py -d ENTERDOMAIN -o outfile.txt <br>
amass - amass enum -d DOMAIN >> outfile.txt <br>
assetfinder - assetfinder ENTERDOMAIN | grep ENTERDOMAIN >> outfile.txt <br>
sublist3r.py - sublist3r.py -d ENTERDOMAIN >> outfile.txt <br>
censys manual checking. Go to the legit domain website and check the SSL Cert, use the O or CN and serach via hosts or certs on censys, you can filter via ports <br>
<br>
What all this has done is built you a large file of subdomains owned by the company. <br>
<br>

**PORT SCANNING**<br>
 Now you have a large list, you'll want to see what is alive on the usual https 443 port. I use httprobe for this <br>
cat outfile.txt | httprobe -p https:443 | tee aliveoutfile.txt <br>
You may want to look for non standard ports, you can do this changing the "443" specified above. <br>
NMAP is also useful, I always do a scan across uncommon web ports <br>
nmap -iL outfile.txt -p 8000,8001,8002,8080,8443,5000,5001,88,9000,9001,9111,9011,9002,9090 | tee aliveports.txt
<br>

**FINDING URLS**<br>
I use several tools for this job and my wordlists come from the following link https://github.com/danielmiessler/SecLists/tree/master/Discovery/Web-Content <br>
or I use the locate command on kali linux if using CLI tools "locate Web-Content" <br>
<br>
**waybackurls <br>
gau <br>
gospider <br>
wfuzz <br>
dirsearch <br>
ffuff <br>
Feroxbuster <br>
JavaScript Link Finder** 
<br>

**Waybackurls** <br> is a tool that will scrape the wayback archive for URLS. We use this with GF patterns read here https://github.com/1ndianl33t/Gf-Patterns <br>
waybackurls DOMAIN | tee waybackurls.txt <br>
<br>
**OR** 
<br>
<br>
cat aliveoutfile.txt | waybackurls | tee waybackurls.txt
<br>
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
output these files using >> urls.txt after each command
wfuzz -w wordlist/general/common.txt http://DOMAIN <br>
https://wfuzz.readthedocs.io/en/latest/user/basicusage.html <br>
<br>
**dirsearch**
dirsearch -d DOMAIN -w WORDLIST --deep-recursive (for recursive) <br>
I like to use this on a bash loop if scanning a file containing subdomains so I will use the following bash script <br>
for i in $(cat aliveoutfile.txt)do; subfinder -d $i;done<br>
This one liner can be used on any script just replace "subfinder" with the tool name then the syntax<br>
<br>
**feroxbuster**
feroxbuster --url DOMAIN -w WORDLIST <br>
<br>
**JavaScript Link Finder** <br>
This is a Burp tool plugin, download this on the BAPP store and it will crawl for further endpoints in Javascript <br>
<br>
You can proxy results from these crawlers into BurpSuite using the -p https://127.0.0.1:8081 syntax (depends what IP your BS and port is active)<br>
dirsearch uses the --proxy= syntax
<br>

Now that you have all URL's, it is advised to run paraminer on the output file you have created for all all subdomains, will need a bash one liner <br>
for i in $(cat aliveoutfile.txt)do; paramspider.py -d $i >> paramspider.txt <br>
<br>
<br>
Further Param discovery - arjun -i aliveoutfile.txt -oT arjun.txt <br>
Move contents of arjun.txt into waybackurls.txt
<br>

**BASH TIME**
Now you have all your files, you want to externally check for vulns <br>
First I check for Reflected XSS using qsreplace and airixss <br>
cat waybackurls.txt | gf xss | qsreplace '<test>' | airixss -p "<test>" <br>
This will check all urls grepped under the xss catagory for reflection of unfiltered <>tags <br>
<br>
Open Redirect - cat waybackurls.txt | gf redirect | qsreplace "https://evil.com" | httpx -status-code -location -fc 404,401 <br>
<br>
SSRF One Liner - Open Burpsuite and go to collab client, copy the value. <br>
cat waybackurls.txt | qsreplace BURPCOLLAB | airixss -p BURPCOLLAB <br>
<br> 
Now you'll want to run Nuclei on your hoste file <br>
for i in $(cat aliveoutfile.txt)do nuclei -u $i;done <br>
<br>
  
**QUICK WINS WORDLIST**<br>
/phpinfo.php<br>
/info.php<br>
/admin.php<br>
/api/apidocs<br>
/apidocs<br>
/api<br>
/api/v2<br>
/api/v1<br>
/v2<br>
/package.json<br>
/security.txt<br>
/application.wadl<br>
/api/apidocs<br>
/swagger<br>
/swagger-ui<br>
/swagger-ui.html<br>
/swagger/swagger-ui.html<br>
/api/swagger-ui.html<br>
/v1.x/swagger-ui.html<br>
/swagger/index.html<br>
/graphql<br>
/graphiql<br>
/register<br>
/login<br>
/.git<br>
/.env<br>
/.htaccess<br>
/script<br>
/console<br>







