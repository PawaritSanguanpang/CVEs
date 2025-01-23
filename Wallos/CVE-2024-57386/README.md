# Wallos v.2.41.0 - Stored Cross-site Scripting (XSS)
We discovered that Wallos v2.41.0 is vulnerable to Stored Cross-Site Scripting (XSS) due to improper sanitization of user input, which allows an attacker to inject malicious JavaScript into a persistent profile picture field.
<p align="center">
  <img src="https://github.com/user-attachments/assets/8413b8c9-b472-4d2b-865e-361bc5e48432">
</p>

# What is Stored Cross-Site Scripting (XSS)?

Stored Cross-Site Scripting (XSS) is a vulnerability where an attacker injects malicious JavaScript into a web application’s input fields, which is then stored on the server and executed when other users access the affected content.

# Proof of Concept (POC)

The following are step to reproduce:

First, we log in as normal users.

<p align="center">
  <img src="https://github.com/user-attachments/assets/7d72b967-b2f0-48e4-b4f4-a305a43cac4f">
</p>

Next, we click on the profile picture → Profile.

<p align="center">
  <img src="https://github.com/user-attachments/assets/4596c819-b524-4945-9752-400053c16bd7">
</p>

Next, we click on the save button.

<p align="center">
  <img src="https://github.com/user-attachments/assets/04621493-939a-4f72-864a-f8f1438583b8">
</p>

Next, we used Burp Suite to intercept HTTP requests, and we found the avatar parameter has a value as the path of the profile picture.

<p align="center">
  <img src="https://github.com/user-attachments/assets/63039cf4-e37f-4918-a59c-e8b2b2bc671d">
</p>

Next, we right-click on the profile picture in the User Details section and click the Inspect button.

<p align="center">
  <img src="https://github.com/user-attachments/assets/9a2602eb-a381-4c26-847b-30980e5f3e7b">
</p>

Next, we found the source of the IMG tag is the same as the value of the avatar parameter.

<p align="center">
  <img src="https://github.com/user-attachments/assets/e7e9e7d9-ff6d-4d26-9725-26ab81c3ac49">
</p>

Next, we append "> to close the <img> tag and add <h1>Hello World!</h1> to attempt injecting an HTML tag. If the injection is successful, the page will render 'Hello World!' as a header.

<p align="center">
  <img src="https://github.com/user-attachments/assets/a9276697-6df7-4320-bf91-1fa2cdf1615f">
</p>

Next, we refresh the profile page and we found the page render 'Hello World!' as a header.

<p align="center">
  <img src="https://github.com/user-attachments/assets/564cd775-fa6b-4316-b5fb-eadb65e07b20">
</p>

Next, we attempt to inject JavaScript to trigger an alert with the cookie value.

<p align="center">
  <img src="https://github.com/user-attachments/assets/15a9596c-1a7a-418f-9745-8fda3feec483">
</p>

Finally, when we refreshed the profile page, we found that the JavaScript successfully executed, displaying an alert with the cookie value.

<p align="center">
  <img src="https://github.com/user-attachments/assets/b26858b4-2fc3-4a50-9212-5cafc28359db">
</p>

# Recommendation

- **Sanitize User Input**:
    - Ensure that all user inputs are properly sanitized. This means removing or encoding potentially dangerous characters (like `<`, `>`, `"`, `'`, etc.) that could be interpreted as HTML or JavaScript.
- **Use Output Encoding**:
    - When displaying user-generated content, encode any data that could be interpreted as HTML or JavaScript. For example, replace `<` with `&lt;`, `>` with `&gt;`, and so on.
- **Validate and Filter Input**:
    - Validate and filter inputs based on their expected format (e.g., email addresses, numbers, etc.), and ensure that no HTML or JavaScript is allowed unless specifically required.
- **HTTPOnly and Secure Cookies**:
    - Use the **HTTPOnly** flag on cookies to prevent JavaScript from accessing session data or cookies directly.
