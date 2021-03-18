SSRF: Server Side Request Forgery

In Simple terms SSRF is a type of vulnerability that enables an attacker to make a request to the Back End APIs or database by behalf of the server.

___SSRF AGAINST SERVER ITSELF___

For ex: A website has a product and it allows you to check whether the product is in stock or not.

So it makes a request like.

*****

GET /product?id=1234 HTTP 1.1

Host: Mozilla 5.0

Cookie: ABCD

.
.
.
.

Check_API:https://back-end/product/check_stock?id=1234


*****

The website makes a request to the back-end server which checks the product availability,if no propoer protection mechanism is used, an attacker can change the url to get access to sensitive information as the server will believe that it's a genuine request made by the webpage itself.

*****


GET /product?id=1234 HTTP 1.1

Host: Mozilla 5.0

Cookie: ABCD

.
.
.
.

Check_API:https://localhost/admin


*****

___SSRF AGAINST OTHER BACKEND___

Sometimes, the website keeps it's importnat files or pages on another server and make the call to it when needed, though it's hidden from public eye, it may become exposed, So using the previous example the payload could be crafted like.

*****

GET /product?id=1234 HTTP 1.1

Host: Mozilla 5.0

Cookie: ABCD

.
.
.
.

Check_API:https://192.168.1.0/admin

*****


