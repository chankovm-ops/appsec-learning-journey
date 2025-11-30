# Daily Progress Log

## Day 1: [29.11.2025]
**Focus:** Anatomy of a Request/Response
**Resources:** MDN Web Docs & MonsterLessons Academy
**Learnings:**
* **Method:** I saw a `GET` request. This asks the server for data.
* **Status Code:** The server returned `200`, which means "OK".
* **Headers:** I noticed the `User-Agent` header, which tells the server I am using [Your Browser Name].
* **Insight:** I realized that "Cookies" are just lines of text sent in the Header. If I can change this text in Burp Suite later, I might be able to trick the server.

## Day 2: [29.11.2025]
**Focus:** Burp Suite Installation & Proxy Chaining
**Resource:** Elevate Cyber Video
**Learnings:**
* **Proxying:** Configured FoxyProxy to route traffic to `127.0.0.1:8080`.
* **The Handshake:** Learned that Burp acts as a "Man-in-the-Middle". I had to install Burp's CA Certificate so my browser wouldn't reject the connection as "Insecure."
* **First Intercept:** Successfully paused a request to Google.
* **QA Insight:** This is different from the "Network Tab" because I can actually *modify* the data before the server sees it. The Network tab is read-only; Burp is read-write.

## Day 3: Burp Repeater [29.11.2025]
**Focus:** Modifying HTTP Requests in Flight
**Resource:** PortSwigger Documentation
**Learnings:**
* **Repeater:** Learned that I can "replay" a request as many times as I want without using the browser.
* **The Experiment:** I searched for "cat", intercepted the request, changed the parameter `q=cat` to `q=dog` in Repeater, and the server responded with search results for dogs.
* **QA Insight:** In manual QA, if I want to test 50 different inputs, I have to type them into the UI 50 times. With Repeater, I can just change the text and hit "Send" 50 times in seconds. This is much faster for testing edge cases.

## Day 4: GET vs POST [30.11.2025]
**Focus:** Request Methods & Data Placement
**Resource:** HTTP Methods (MDN) / TestPHP Vulnweb
**Learnings:**
* **GET:** I observed that GET requests put parameters in the URL (Query String).
* **POST:** I observed that POST requests hide parameters in the Body (bottom of the request).
* **The "Envelope":** I intercepted a login attempt on `testphp.vulnweb.com`. Even though the password was dots `••••` on the screen, Burp showed me clear text `pass=password123` in the body.
* **QA Insight:** I need to check my company's application. If any sensitive data (tokens, PII) appears in the URL bar, it's a security defect because URLs are logged in browser history.