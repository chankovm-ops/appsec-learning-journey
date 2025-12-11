# Daily Progress Log

## Lesson 1: [29.11.2025]
**Focus:** Anatomy of a Request/Response

**Resources:** MDN Web Docs & MonsterLessons Academy
* [MDN HTTP Overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Overview)
* [HTTP Request and Response Format - You Must Know It](https://www.youtube.com/watch?v=NqKytCEaqMw)

**Learnings:**
* **Method:** I saw a `GET` request. This asks the server for data.
* **Status Code:** The server returned `200`, which means "OK".
* **Headers:** I noticed the `User-Agent` header, which tells the server I am using [Your Browser Name].
* **Insight:** I realized that "Cookies" are just lines of text sent in the Header. If I can change this text in Burp Suite later, I might be able to trick the server.

## Lesson 2: [29.11.2025]
**Focus:** Burp Suite Installation & Proxy Chaining

**Resource:** Elevate Cyber Video
* [How to use Burp Suite for Beginners](https://www.youtube.com/watch?v=GxvFqgCBW-8)

**Learnings:**
* **Proxying:** Configured FoxyProxy to route traffic to `127.0.0.1:8080`.
* **The Handshake:** Learned that Burp acts as a "Man-in-the-Middle". I had to install Burp's CA Certificate so my browser wouldn't reject the connection as "Insecure."
* **First Intercept:** Successfully paused a request to Google.
* **QA Insight:** This is different from the "Network Tab" because I can actually *modify* the data before the server sees it. The Network tab is read-only; Burp is read-write.

## Lesson 3: Burp Repeater [29.11.2025]
**Focus:** Modifying HTTP Requests in Flight

**Resource:** PortSwigger Documentation
* [Burp for Beginners: How to Use Repeater](https://www.youtube.com/watch?v=LfuU5u6C2Sk)

**Learnings:**
* **Repeater:** Learned that I can "replay" a request as many times as I want without using the browser.
* **The Experiment:** I searched for "cat", intercepted the request, changed the parameter `q=cat` to `q=dog` in Repeater, and the server responded with search results for dogs.
* **QA Insight:** In manual QA, if I want to test 50 different inputs, I have to type them into the UI 50 times. With Repeater, I can just change the text and hit "Send" 50 times in seconds. This is much faster for testing edge cases.

## Lesson 4: GET vs POST [30.11.2025]
**Focus:** Request Methods & Data Placement

**Resource:** HTTP Methods (MDN) / TestPHP Vulnweb
* [HTTP GET vs POST explained for beginners](https://www.youtube.com/watch?v=tkfVQK6UxDI)


**Learnings:**
* **GET:** I observed that GET requests put parameters in the URL (Query String).
* **POST:** I observed that POST requests hide parameters in the Body (bottom of the request).
* **The "Envelope":** I intercepted a login attempt on `testphp.vulnweb.com`. Even though the password was dots `••••` on the screen, Burp showed me clear text `pass=password123` in the body.
* **QA Insight:** I need to check my company's application. If any sensitive data (tokens, PII) appears in the URL bar, it's a security defect because URLs are logged in browser history.

## Lesson 5: Cookies & Sessions [30.11.2025]
**Focus:** Session Management & State

**Resource:** MDN HTTP Cookies
* [What is a cookie?](https://www.youtube.com/watch?v=I01XMRo2ESg)

**Learnings:**
* **Statelessness:** Learned that HTTP doesn't remember me. The "Cookie" is the only thing keeping me logged in.
* **The Experiment:** I logged into `testphp.vulnweb.com`, found the session cookie in the DevTools "Application" tab, and deleted it. Refreshing the page immediately logged me out.
* **QA Insight:** This explains why "Clear Cache & Cookies" fixes so many bugs—it forces a fresh handshake with the server.
* **Security Insight:** If a hacker can copy my cookie text and paste it into their browser, they *are* me. They don't need my password. This is why "Secure" and "HttpOnly" flags on cookies are critical (I'll learn those later).

## Lesson 6: First Logic Hack [30.11.2025]
**Focus:** Consolidating HTTP, Cookies, and Repeater

**Resource:** PortSwigger Lab: User role controlled by request parameter
* [Business Logic Vulnerabilities | Complete Guide](https://www.youtube.com/watch?v=HLS2-kGHupU)

**Learnings:**
* **The Vulnerability:** The application trusted the cookie sent by the user without verifying it on the server.
* **The Exploit:** I intercepted the request where the cookie said `admin=false`. I changed it to `true` in Repeater.
* **Result:** The server believed me and served the Admin Panel.
* **QA Insight:** This is a classic "Client-Side Trust" bug. As a QA, I should always ask: "Can I change this value in the API call?" If the backend accepts it without checking, it's a bug.

## Lesson 7: SQL Injection (Retrieval) [01.12.2025]
**Focus:** Breaking the WHERE clause

**Resource:** Computerphile / PortSwigger
* [What is SQL injection (SQLi)?](https://portswigger.net/web-security/sql-injection)
* [Running an SQL Injection Attack - Computerphile](https://www.youtube.com/watch?v=ciNHn38EyRc)

**Learnings:**
* **The Theory:** SQLi works by breaking out of the data context using a single quote `'` and appending new logic.
* **The Payload:** Used `' OR 1=1--`.
    * `'` closed the category string.
    * `OR 1=1` is always true, so the database returned *everything*.
    * `--` commented out the rest of the legitimate query so it wouldn't crash.
* **QA Insight:** I should test search bars with a single quote `'`. If the page crashes or returns a database error, it's likely vulnerable.

## Lesson 8: SQLi Login Bypass [06.12.2025]
**Focus:** Commenting out password checks

**Resource:** PortSwigger Lab
* [Subverting application logic](https://portswigger.net/web-security/sql-injection#subverting-application-logic)
* [SQL Injection Login Bypass - PortSwigger Web Security Academy Series](https://www.youtube.com/watch?v=VO1dIxidYTo)

**Learnings:**
* **The Comment:** In SQL, `--` (dash dash space) means "comment."
* **The Hack:** I logged in as `administrator` by typing `administrator' --`.
* **Result:** The application ignored the password field entirely because I commented it out in the query.
* **QA Insight:** Every login form I test from now on gets a `' --` test.

## Lesson 9: Burp Intruder [06.12.2025]
**Focus:** Automation & Brute Force

**Resource:** YouTube Guides
* [Burp for Beginners: How to Use Intruder](https://youtu.be/mibKttwhbRk?si=KRLHAGhlg1pbeFu5)

**Learnings:**
* **Intruder:** Used for "Fuzzing" (sending many inputs to one place).
* **Sniper Attack:** The default mode. Replaces one placeholder with a list of words.
* **Analysis:** Learning to read the results is key. I look for anomalies in "Response Length" or "Status Code" to find the successful login.

## Lesson 10: SQLi UNION Attacks [11.12.2025]
**Focus:** Determining Column Count

**Resource:** Rana Khalil / PortSwigger
* [SQL Injection - Lab #3 SQLi UNION attack determining the number of columns returned by the query](https://youtu.be/umXGHbEyW5I?si=kmoWkKY76K60VAWb)
* [SQL injection UNION attacks](https://portswigger.net/web-security/sql-injection/union-attacks)

**Learnings:**
* **The Rule:** UNION statements only work if the number of columns matches the original query.
* **The Technique:** I used `' ORDER BY X--` to guess the number of columns. When I incremented X until the page crashed (Internal Server Error), I knew the limit.
* **QA Insight:** If I see a search filter, I should try sorting by non-existent columns. If it throws a 500 error, I know the database is exposed.

