BLIND SSRF 

Blind SSRF occurs when an attacker can induce a request to the backend through the HTTP request but the response is not show in the frontend.


___Ways to detect___

Blind SSRF can be best detected by using out of band techniques(OAST), THis involves triggering HTTP request to a external server that an acttaker can control and noting down the connections.One could either configure there own server or use BurpCollaborator to create a server and point it to the attacker's machine


___Exploiting Blind SSRF___

Since the response is not shown in the frontend, The attacker could swipe the IP address space or send known payloads for very common vulnerabiliteis in the system thus trying to gain proper access to the system.