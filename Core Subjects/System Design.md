### Design a URL Shortener

*functional requirements: 
1. given a long URL, create a short URL
2. given a short URL, redirect to long URL
*non functional requirements: 
3. very low latency
4. very high availability

**API Design**
REST API
1. POST: /create-url
	  - params: long-url
	  - status code: 201 created
2. GET: /{short-url}
	- status code: 301 permanent redirect
**Schema**
- long-url : string
- short-url : string
- created-at : timestamp
#### key questions to ask the interviewer:

1. *how long should the url be*:
	![[Pasted image 20260926221806.png]]

so we need to use 7 characters
#### HLD

POST
![[Pasted image 20260926222708.png]]
GET
![[Pasted image 20260926222750.png]]
**Additional talking points**
1. Analytics
- ﻿﻿Counts for each URL to determine which short URLS to cache
- ﻿﻿IP address to store location information to determine where to locate caches etc.
2. Rate limiting
-  Prevent DDoS attacks by malicious users
3. Security considerations
 - Add random suffix to the short url to prevent hackers predicting