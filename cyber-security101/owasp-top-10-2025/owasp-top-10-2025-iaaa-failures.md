# 📌 OWASP Top 10 2025: IAAA Failures

## What is IAAA?

**IAAA** is a simple way to think about how users and their actions are verified on applications. Each item plays a crucial role and it isn't possible to skip a level. That means, if a previous item isn't being performed, you cannot perform the later times. The four items are:

**Identity** - the unique account (e.g., user ID/email) that represents a person or service.
**Authentication** - proving that identity (passwords, OTP, passkeys).
**Authorisation** - what that identity is allowed to do.
**Accountability** - recording and alerting on who did what, when, and from where.

The three categories of OWASP Top 10:2025 discussed in this room relates to failures in how IAAA was implemented. Weaknesses here can be incredibly detrimental, as it can allow threat actors to either access the data of other users or gain more privileges than they are suppose to have.

## A01: Broken Access Control

