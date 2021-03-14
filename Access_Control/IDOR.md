IDOR-INDIRECT OBJECT REFERENCE 

It arises when user supplied input interacts with objects directly and can be modified to suit an attacker needs.

 IDOR vulnerabilities are most commonly associated with horizontal privilege escalation, but they can also arise in relation to vertical privilege escalati

 _____IDOR vulnerability with direct reference to database objects_____

 Suppose a website access the details of an user from it's database through the following request URL


 https://insecure.com/user/details?customer_id=4567


 An attacker can easily change the value of the customer id to get the information of other users.


 ______IDOR vulnerability with direct reference to static files______

 Sometimes the sites store data in static files which could be accessed by the users.Suppose a website stores the chat transcript into a static text file, and if a user wants to get access to that. The URL makes a request such as:


 https://insecure.com/user/chat?file_id=123.txt

An attacker can easily change the value of the file id to get access to chat transcripts of other users
