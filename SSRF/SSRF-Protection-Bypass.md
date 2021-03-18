To prevent users from accessing the sensitive information, Sites generally deploy certain protection mechanism, but we can also try to bypass them

___SSRF with blacklist-based input filters___

Some websites block certain words or IP addresses such as localhost, 127.0.0.1 but it can be circumvented but using 2130706433, 017700000001, or 127.1. to bypass this filter.

Or Registering your own domain name that resolves to 127.0.0.1. You can use spoofed.burpcollaborator.net for this purpose. 

___SSRF with whitelist-based input filters___

Sometimes the sites employs certain protective mechanism that only allows certain keywords that can be passed as parameter.

To bypass it, one could do the following.

    1.You can embed credentials in a URL before the hostname, using the @ character. For example: https://expected-host@evil-host.

    2.You can use the # character to indicate a URL fragment. For example: https://evil-host#expected-host.

    3.You can leverage the DNS naming hierarchy to place required input into a fully-qualified DNS name that you control. For example: https://expected-host.evil-host.

    4.You can URL-encode characters to confuse the URL-parsing code. This is particularly useful if the code that implements the filter handles URL-encoded characters differently than the code that performs the back-end HTTP request.

___SSRF BY OPEN REDIRECTION___

Sites strictly filter the requests passed through the URL, so it becomes nearlly impossible to bypass those restrictions but in that case it's possbile to bypass this restriction by redirecting the url to either our malicious site or some other backend server.


Suppose while checking  a product , one wished to see the next and for that the page sends a request with parameter such as 

"/product/nextProduct?currentProductId=6"

So an attacker could do something like

/product/nextProduct?currentProductId=6&http://evil-site.com

which will redirect the page to the
http://evil.com

SO the request could become someting like:


****
 POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://Site.com/product/nextProduct?currentProductId=6&path=http://192.168.0.1/admin


****
