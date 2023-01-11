
Account collision

if front end limits characters, see if it is javascript front end, intercept with burp and try unicode characters for a known username
e.g. admin exists
try 
unicode characters to admin

try space issues 
if admin@admin.a exists try
admin@admin.a  a  (via burp)

try use a different @ character

Try to use CLRF charatcers ater a valid email should email verification not be required, see if this then allows you to log in as the original email

victim has registered account e.g. tony@tony.com
go to https://qaz.wtf/u/convert.cgi?text=o and unicode one of the chars
create email with same but replaced one char with unicode
go to - https://www.punycoder.com/ add unicode char and encode - can now register email address with output to test email
