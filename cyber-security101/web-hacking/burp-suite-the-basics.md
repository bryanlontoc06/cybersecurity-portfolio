# 📌 Burp Suite: The Basics

Although Burp Suite Community offers a more limited feature set compared to the Professional edition, it still provides an impressive array of tools that are highly valuable for web application testing. Let's explore some of the key features:

**Proxy**: The Burp Proxy is the most renowned aspect of Burp Suite. It enables interception and modification of requests and responses while interacting with web applications.
**Repeater**: Another well-known feature. Repeater allows for capturing, modifying, and resending the same request multiple times. This functionality is particularly useful when crafting payloads through trial and error (e.g., in SQLi - Structured Query Language Injection) or testing the functionality of an endpoint for vulnerabilities.
**Intruder**: Despite rate limitations in Burp Suite Community, Intruder allows for spraying endpoints with requests. It is commonly utilized for brute-force attacks or fuzzing endpoints.
**Decoder**: Decoder offers a valuable service for data transformation. It can decode captured information or encode payloads before sending them to the target. While alternative services exist for this purpose, leveraging Decoder within Burp Suite can be highly efficient.
**Comparer**: As the name suggests, Comparer enables the comparison of two pieces of data at either the word or byte level. While not exclusive to Burp Suite, the ability to send potentially large data segments directly to a comparison tool with a single keyboard shortcut significantly accelerates the process.
**Sequencer**: Sequencer is typically employed when assessing the randomness of tokens, such as session cookie values or other supposedly randomly generated data. If the algorithm used for generating these values lacks secure randomness, it can expose avenues for devastating attacks.

**Simple analogy**

Imagine the website as a post office:

* **Proxy** → the front desk that intercepts the mail
* **Repeater** → resending the same mail over and over
* **Intruder** → a machine gun that sends many mails automatically
* **Decoder** → a translator for encrypted mail
* **Comparer** → a spot-the-difference tool
* **Sequencer** → a checker to see if the ticket numbers are truly random

## Introduction to Burp Proxy

The **Burp Proxy** is a fundamental and crucial tool within Burp Suite. It enables the capture of requests and responses between the user and the target web server. This intercepted traffic can be manipulated, sent to other tools for further processing, or explicitly allowed to continue to its destination.

**Foxy Proxy**:
https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-basic/

**Scoping**
Capturing and logging all of the traffic can quickly become overwhelming and inconvenient, especially when we only want to focus on specific web applications. This is where scoping comes in.

**Download the CA Certificate: With the Burp Proxy activated, navigate to http://burp/cert. This will download a file called cacert.der. Save this file somewhere on your machine.**

For Hands-on DEMOs, please refer to:
cybersecurity-portfolio/README.md
*click to watch*
### [Meterpreter Payload Exploitation Demo](https://www.youtube.com/watch?v=IEp0_TeC64Y)

