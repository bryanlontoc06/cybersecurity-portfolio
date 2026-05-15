# 📌 Burp Suite: Intruder

## Introduction to Attack Types

The **Positions** tab of Burp Suite Intruder has a dropdown menu for selecting the attack type. Intruder offers four attack types, each serving a specific purpose. Let's explore each of them:

1. Sniper: The Sniper attack type is the default and most commonly used option. It cycles through the payloads, inserting one payload at a time into each position defined in the request. Sniper attacks iterate through all the payloads in a linear fashion, allowing for precise and focused testing.

2. Battering ram: The Battering ram attack type differs from Sniper in that it sends all payloads simultaneously, each payload inserted into its respective position. This attack type is useful when testing for race conditions or when payloads need to be sent concurrently.

3. Pitchfork: The Pitchfork attack type enables the simultaneous testing of multiple positions with different payloads. It allows the tester to define multiple payload sets, each associated with a specific position in the request. Pitchfork attacks are effective when there are distinct parameters that need separate testing.

4. Cluster bomb: The Cluster bomb attack type combines the Sniper and Pitchfork approaches. It performs a Sniper-like attack on each position but simultaneously tests all payloads from each set. This attack type is useful when multiple positions have different payloads, and we want to test them all together.

Each attack type has its advantages and is suitable for different testing scenarios. Understanding their differences helps us select the appropriate attack type based on the testing objectives.

## Sniper

The Sniper attack type is the default and most commonly used attack type in Burp Suite Intruder. It is particularly effective for single-position attacks, such as password brute-force or fuzzing for API endpoints. In a Sniper attack, we provide a set of payloads, which can be a wordlist or a range of numbers, and Intruder inserts each payload into each defined position in the request.

Assuming we have a wordlist with three words: burp, suite, and intruder, Intruder would generate six requests:

|Request Number|	Request Body|
|--|--|
|1|	username=burp&password=Expl01ted|
|2|	username=suite&password=Expl01ted|
|3|	username=intruder&password=Expl01ted|
|4|	username=pentester&password=burp|
|5|	username=pentester&password=suite|
|6|	username=pentester&password=intruder|

## Battering Ram

The Battering ram attack type in Burp Suite Intruder differs from Sniper in that it places the same payload in every position simultaneously, rather than substituting each payload into each position in turn.

Using the Battering Ram attack type with the same wordlist from before (burp, suite, and intruder), Intruder would generate three requests:

|Request Number|	Request Body|
|--|--|
|1|	username=burp&password=burp|
|2|	username=suite&password=suite|
|3|	username=intruder&password=intruder|

## Pitchfork

The Pitchfork attack type in Burp Suite Intruder is similar to having multiple Sniper attacks running simultaneously. While Sniper uses one payload set to test all positions simultaneously, Pitchfork utilises one payload set per position (up to a maximum of 20) and iterates through them all simultaneously.

To better understand Pitchfork, let us revisit our brute-force example, but this time with two wordlists:

The first wordlist contains usernames: joel, harriet, and alex.
The second wordlist contains passwords: J03l, Emma1815, and Sk1ll.

We can use these two lists to perform a Pitchfork attack on the login form. Each request made during the attack would look like this:

|Request Number|	Request Body|
|--|--|
|1|	username=joel&password=J03l|
|2|	username=harriet&password=Emma1815|
|3|	username=alex&password=Sk1ll|

## Cluster Bomb

The Cluster bomb attack type in Burp Suite Intruder allows us to choose multiple payload sets, one per position (up to a maximum of 20). Unlike Pitchfork, where all payload sets are tested simultaneously, Cluster bomb iterates through each payload set individually, ensuring that every possible combination of payloads is tested.

To illustrate the Cluster bomb attack type, let's use the same wordlists as before:

Usernames: joel, harriet, and alex.
Passwords: J03l, Emma1815, and Sk1ll.

In this example, let's assume that we don't know which password belongs to which user. We have three users and three passwords, but the mappings are unknown. In this case, we can use a Cluster bomb attack to try every combination of values. The request table for our username and password positions would look like this:

|Request Number|	Request Body|
|--|--|
|1|	username=joel&password=J03l|
|2|	username=harriet&password=J03l|
|3|	username=alex&password=J03l|
|4|	username=joel&password=Emma1815|
|5|	username=harriet&password=Emma1815|
|6|	username=alex&password=Emma1815|
|7|	username=joel&password=Sk1ll|
|8|	username=harriet&password=Sk1ll|
|9|	username=alex&password=Sk1ll|