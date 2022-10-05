Try see if OAuth redirect_uri accepts localhost -- if it does use DNS binding to see if localhost can be set to a DNS format and redirect to a public server

Pre account takeover - register as a gmail account you own, then signup or login with gmail on the account. Can the user who registered still login with the password?

Intercept flow and if email address within flow appears try change to victims email/username

Check for state parameter, if it does not contain state, try force linking. Send request to victim that contains the URL request with a code (ensure you drop the request with the code so it does not get used). For an exploit server you would create an iframe and have a request pointing to vuln site with code URL.

Test for open redirect on redirect URL - can the token be stolen using this?

Is there a further open redirect on the site that can be used using directory traversal on the login URL to point to the open redirect vuln to steal token this way

Check for Reflected parameters and try XSS
