<h1> Identify a suitable endpoint for cache posioning </h1><br>
This needs to be cacheable and must indicate in some way whether you received a cached response or a response directly from the server. This feedback could take various forms, such as:<br>
<br>
An HTTP header that explicitly tells you whether you got a cache hit <br>
Observable changes to dynamic content <br>
Distinct response times <br>
<br>
If you can identify that a specific third-party cache is being used, you can also consult the corresponding documentation. <br> 
This may contain information about how the default cache key is constructed. <br>
You might even stumble across some handy tips and tricks, such as features that allow you to see the cache key directly. <br>
For example, Akamai-based websites may support the header Pragma: akamai-x-get-cache-key, which you can use to display the cache key in the response headers <br>
<br>
<h1>Probe key handling</h1><br>
You should specifically look at any transformation that is taking place. Is anything being excluded from a keyed component when it is added to the cache key? <br>
Common examples are excluding specific query parameters, or even the entire query string, and removing the port from the Host header.
<br>
Add a random Port to the request and see if when the cache is hit if the port reflects or changes any output.
