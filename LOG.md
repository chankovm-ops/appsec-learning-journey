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
