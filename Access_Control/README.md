ACCESS CONTROl

Access control can be defined as the constraints that are applied on a user regarding what they can and what they cannot do.

They can be divided into 3 categories.

___VERTICAL ACCESS CONTROL___


This defines the mechanisms that restricts the access to sensitive functions that are not available to every type of user.
For ex: An admin can add or delete users but an normal user cannot.


___HORIZONTAL ACCESS CONTROL___


Horizontal Access Control defines the availability of the resources to the users depending on their authorization leve, For example: A normal user can't have access to the resources that can change the theme for a site whereas the site administrator may have access or a banking appliactions allows an user to depoist and withdraw funds from his account but cannot see another user account


___CONTEXT BASED ACCESS CONTROL___

Context dependent access controls restricts users interaction with how  a certain website may interact with it.

A product selling website might prevent users to modify  the contents of their shopping cart after they have made a payment.


___BROKEN ACCESS CONTROL___

These are often encountered during pentesting, Sometimes due to certain functionality a normal user may be able to gain access to the higher privileges. Lets look into how different access controls may be exploited.

VERTICAL ACCESS CONTROL

##UNPROTECTED FUNCTIONALITY

Websites hides the sensitive url from a normal user,If there is no protection mechanism tey may be exposed in the robots.txt files of the website which can allow a user to travel to that site, or easy guessable admin directories could easily be brute forced using tools such a gobuster or dirbuster.

To prevent users from guessing easily, websites make it harder to guess these which is also known as security through obscurity, but certain times these too may be exposed in certain JS functions or codes, Like shown below.

<script>
var isAdmin = false;
if (isAdmin) {
  ...
  var adminPanelTag = document.createElement('a');
  adminPanelTag.setAttribute('https://insecure-website.com/administrator-panel-yb556');
  adminPanelTag.innerText = 'Admin panel';
  ...
}
</script> 

The script adds the link to the user panel if they are admin but the code is easily visible to everyone.

##Parameter-based access control methods

While creating an account or querying something, websites generally tend to check for certain parameters which helps it indentify whether the user has enough privileges, So one could change these parameters to the ones having access to the sensitive information with the help of burpsuite

Ex: Suppose a website checks if the user has admin rights through the "roleid" parameter, So one could intercept the request from burp and then change the roleid to that of the admin to escalate priviliges


##Broken access control resulting from platform misconfiguration

 Some applications enforce access controls at the platform layer by restricting access to specific URLs and HTTP methods based on the user's role. For example an application might configure rules like the following:

DENY: POST, /admin/deleteUser, managers

This rule denies access to the POST method on the URL /admin/deleteUser, for users in the managers group. Various things can go wrong in this situation, leading to access control bypasses. 


Some application frameworks support various non-standard HTTP headers that can be used to override the URL in the original request, such as X-Original-URL and X-Rewrite-URL

If certain sites do this, it could be exploited to get admin rights

Ex: A request header can be changed to

POST / HTTP 1.1
X-ORIGINAL-URL: /admin


...


#BROKEN HORIZONTAL ACCESS CONTROL

It occurs when a user can gain access to details or services of another user.

Wesbites that don't employ proper protection mechanisms are very easy to exploit. For instance a user could change their userid number to another user's id to get the details and if the userid is not easily guessable they can be leaked in the source code of the comment history with that user.

These vulnerabilites could also be arised during multistep process, if even one of the steps allows the attacker to manipulate the request they could easily get access to the user's account