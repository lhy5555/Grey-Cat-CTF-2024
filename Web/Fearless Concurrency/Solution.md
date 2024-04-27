# The Challenge

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-23%20200216.png)




**Note: This challenge has an unintended solution as there is a bug with the server. Scroll down to the reason for unintended solution for explanation**
# The Analysis

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20140311.png)
The website doesn't seem to have something interesting, even in the page source. Hence, let's look at the source code.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20140529.png)





So this code suggests that it expects a GET request to the root directory (/), and a POST request to /register, /query, and /flag directory.


Zooming into the /register, it requires a `users` parameter to be parsed into the POST request.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20140804.png)






# The solution
Hence, we will first craft the payload to POST to /register.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20141148.png)






Here, we are registering a user called `hello1`, and we got the UID for the user `hello1`, which is `13890296943370173281`. Next, we look at the `/query` 

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20141420.png)





This suggests that the /query takes in 2 parameters (the user_id and query_string).

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20141634.png)





This code here shows that it expects the POST data to be a JSON format. Hence, it will require a header `Content-Type: application/json` and the data to be POSTed in JSON format.


![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20142041.png)





![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20142144.png)





This means that whatever strings that are parsed into the query_string parameter in the POST request will be parsed into the SQL statement SELECT * FROM info WHERE body LIKE '`query_string`'. Which means, we might be able to do SQL injection here. Let's test it out

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20142512.png)






It looks like there is indeed a time delay here; hence, it is vulnerable to SQL Injection. We will now explore further on the /query. 


![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20142652.png)






This shows that upon POSTing to /query, a temporary table is created with the table name in this format `tbl_{hash_of_UID_and_the_string_"fearless_concurrency"}_{randomly_generated_table_id}.


**Beginner friendly tip**

If unable to manually calculate the *hash_of_UID_and_the_string_"fearless_concurrency"* manually, you can copy and paste part of the source code to a new RUST file with the known values. You may want to download the RUST interpreter [here](https://www.rust-lang.org/tools/install) The image below is an example.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20143340.png)





Where lines 8, 11, 14, 15 and 18 are a direct copy and paste from the original code where it calculates the *hash_of_UID_and_the_string_"fearless_concurrency"*.

We will now run this code with the `cargo run` command to get the hash (make sure you run the code at the location of your RUST project directory).

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20143738.png)





So the *hash_of_UID_and_the_string_"fearless_concurrency"* is `70a9958c979d25efd571977a18bc615852b4abee`. 

The code below shows that the temporary table stores the secret of each unique user.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20152938.png)





And to get the flag, we will need to parse the user_id and the secret to the POST request to /flag

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20153123.png)





**Reason for unintended solution**

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20152114.png)





The code here shows that this temporary table will get deleted once the /query has executed your query_string. However, **sometimes** this table doesn't get deleted on the server.

This image shows the **unintended** outcome of the query_string

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-21%20194657.png)





This image shows the **intended** outcome of the query_string

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20152609.png)





## The unintended solution
We will now proceed to do SQL injection to retrieve the full table name.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-21%20194657.png)




**Note: As I have limited screenshots when this bug occurs, I will only show the commands being used here based on the screenshot to retrieve the flag**


Next, we will retrieve the secret stored in the temporary table with the command `curl -X POST http://challs.nusgreyhats.org:33333/query -H "Content-Type: application/json" -d "{\"user_id\": 1356829178136985947, \"query_string\": \"6' UNION SELECT secret FROM tbl_6ebc3f70e049e80e724ca8d1146ce3127da96db6_1497578544#\"}"`


Lastly, we will use this secret here to do a POST request to /flag to get the flag with the command `curl -X POST http://challs.nusgreyhats.org:33333/flag -H "Content-Type: application/json" -d "{\"user_id\": 5057958167156069075, \"secret\": *the_secret_you_got*}"`


## The intended solution

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20152609.png)





Notice that the table name will change everytime. Hence to ensure that we are able to get the secret before the table gets deleted (making it unable to predict the table name), we will need to have a time delay, and use a **new command prompt or terminal** to retrieve the secret.

So on one terminal, we will execute a time delay command

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20154427.png)





And the other terminal, we will execute the command to get the table name

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20154532.png)





**OOPS!! It is affected by a time delay in the previous terminal. Hence, we will have to try to create another user, so that I can use the other user UID to retrieve the table name of the existing user.**

This will create another user called hello2, with the user_id of `14007658572505703413`

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20154821.png)




Next, we will modify our code and re-run to calculate the hash for this user_id.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20155103.png)





![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20155156.png)






Now we have the following details:

`hello1 : 13890296943370173281 : 70a9958c979d25efd571977a18bc615852b4abee`

`hello2 : 14007658572505703413 : b8cc3045fe6cdd34372408f4b78db2d7e5982290`


Next, we will use **hello1** user_id to do the time delay and **hello2** user_id to retrieve hello1 table name, and subsequently hello1 secret.

**Note: Make sure the time delay is long enough for you to retrieve the table name and use it to get the secret**

Using hello1 for time delay:

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20160352.png)





Using hello2 to retrieve table name for hello1:

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20160429.png)





Notice now that the table name remains, which means it will only get deleted after the SLEEP(600) (in this case) has ended. Which means we cannot use the same user_id to run two different command at the same time.

Finally, using hello2 to retrieve the secret for hello1 with the temporary table. **Make sure the time delay is still running before continuing**

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20160530.png)





So the secret for hello1 is 1281342252. Finally, we will proceed to POST this to /flag to get the flag. **You may now stop the SLEEP(600) if you want**

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FFearless%20Concurrency%2FScreenshot%202024-04-27%20160802.png)





The flag is `grey{ru57_c4n7_pr3v3n7_l061c_3rr0r5}`
