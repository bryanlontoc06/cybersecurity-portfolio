# 📌 Broken Authentication

## Types of Authentication Bypass

1. Username Enumeration
2. Credential Brute Force
3. Logic Flaws
4. Cookie Manipulation

## Username Enumeration

**Username enumeration** is a reconnaissance technique used to determine which usernames exist on a target web application. The output is a list of real accounts that can be fed into credential attacks such as brute force, password spraying, and targeted phishing. Before any meaningful attack on user accounts can be mounted, the attacker needs to know which accounts exist, and an application that discloses this information removes the first step the attacker would otherwise have to complete.

### Enumerating With ffuf

ffuf is a fast web fuzzer written in Go. It substitutes each entry from a wordlist into a marker inside an HTTP request and reports the responses that match a given condition. The tool is pre-installed on the AttackBox and can also be downloaded from [https://github.com/ffuf/ffuf](https://github.com/ffuf/ffuf).

`ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "username=FUZZ&email=x&password=x&cpassword=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.48.154.134/customers/signup -mr "username already exists"`

OR

`ffuf -w /usr/share/wordlists/SecLists/Usernames/Names/names.txt -X POST -d "email=specialadmin@support.thm&password=x" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.48.154.134 -fs 2678`

The -w argument selects the wordlist of candidate usernames. The -X POST argument sets the HTTP method, as required by the signup form. The -d argument defines the request body, with the token FUZZ acting as a placeholder that ffuf replaces with each word from the wordlist in turn. The -H argument sets the Content-Type header so the server treats the body as URL-encoded form data. The -u argument sets the target URL. Finally, the -mr argument (match regex) restricts the output to responses whose body contains the string username already exists.

Running the command produces a short list of matches, each corresponding to a username registered on the application. Save the matching usernames into a file called valid_usernames.txt, one per line, for use in the next task. The file will be passed directly into ffuf as a wordlist, so the contents must contain only usernames with no status codes, timing columns, or extra whitespace.

### Brute Forcing a Login Form

### Success Conditions
Every brute-force tool needs a way to distinguish a successful login from a failed one. The Acme IT Support login form at http://10.48.149.214/customers/login returns HTTP 200 with the login page re-rendered when the submitted credentials are invalid, and a 302 redirect to the customer dashboard when the credentials are valid. The change in status code is the signal that a login has succeeded.

Other applications signal success differently. Some return a new response body, some set an additional cookie, and some redirect to a specific URL. In each case, the attacker's tool needs to be configured to match the signal that corresponds to success for that particular application.

### Running the Attack With ffuf
ffuf supports multiple wordlists by assigning each one a unique marker in place of the default FUZZ. This allows the username and password positions in the same request to be varied independently.

From the directory containing valid_usernames.txt, run the following command:

`ffuf -w valid_usernames.txt:W1,/usr/share/wordlists/SecLists/Passwords/Common-Credentials/10-million-password-list-top-100.txt:W2 -X POST -d "username=W1&password=W2" -H "Content-Type: application/x-www-form-urlencoded" -u http://10.48.149.214/customers/login -fc 200`

The -w argument now takes two wordlists separated by a comma, with W1 bound to the username list from Task 3 and W2 bound to the top one hundred passwords from SecLists. The body template username=W1&password=W2 places each marker where it belongs in the POST body, so every username is paired with every password in the list. The -fc 200 argument (filter code) discards every response that returned HTTP 200, leaving only the single successful login visible in the output.

## Logic Flaws


## Cookie Manipulation


-----

`nmap -sC -sV -p- -v <target_ip>`

`gobuster dir -u http://10.48.187.39 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt,bak,zip,js`