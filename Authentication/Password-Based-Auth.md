___PASSWORD BASED AUTHENTICATION VULNERABILITIES____


Websites opt a password-based login system, so if the attacker is able to guess the login creds of the victim,they could easily compromise the account.

The most common way is to Brute force either username or password or both.

### BRUTE FORCING USERNAME OR PASSWORD ###

Usually business have the email in the form of name@company_name.com which makes it easier for an attacker to refine his attacks, Also "admin" or "administrator" is also pretty commonnly used as an easily guessable username.

Brute Forcing passwords is a bit difficult as one needs to test against thousands to lakhs of passwords before they can get an actual hit, knowing the password lenth or certain characters makes it easy to guess but high entropy passwords are very hard to crack and morevoer the sites nowadays make sure the password enetered is not weak.



##USERNAME ENUMERATION ##


Getting to know the username could actually prove to be beneficial for an attacker, One should keep an eye out for certain changes during guessing of the id/password.There can be certain changes such as

1.Change in status code: Usually the HTTP status codes remains the same, but sometimes it may differ from the code that occurs during a real name and wrong password.SO one could look out for it.

2.Change in Error: USually the error code returns "Wrong username or password" but on poorly configured login pages if the username is correct it could give an error "Wrong password" so one could look out for the difference in error codes.

3.Chane in Response time: If all the requests are handled with similar response time, and if it deviates from that means, something is going behind the scene. As if the username is correct the website could take additional time to check if the password is also correct or not


To prevent bruteforcing the website can implement certain attempts to stop the attacker from accessing the account,but it can also be bypassed then.


#IP-BLOCKING


Websites tend to block a certain IP for a certain amount of time if there are too many failed login attempts, to bypass this the attacker could include a valid ID and Paswsord in the wordlist to reset the number of max attempts for that IP


#ACCOUNT LOCKING

#ACCOUNT LOCKING


TO protect against bruteforcing some wesbites locks the account after certain number of tries, to bypass this, one could establish a small number of users and a list of most probable password for any of the users like 2-3 and then using burp intruder could check.
This also fails against account stuffing, sometimes users tend to provide the same id and password for various sites and during a data breach the information gets leaked, which could be bruteforced.

#HTTP basic authentication

Sometimes the HTTP send a auth token consisting og user name and password cancatenated through base64 thus making it easier to decode and moreover one could easily bruteforce it as they are not generally supplied with brute force protection

