# CHALLENGE DESCRIPTION

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FMISC%2FHashcat%20the%20Ripper%2FScreenshot%202024-04-23%20194422.png)

# ANALYSIS

***The File***

It's a zip file

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FMISC%2FHashcat%20the%20Ripper%2FScreenshot%202024-04-23%204305.png)



***Unzipping the file***

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FMISC%2FHashcat%20the%20Ripper%2FScreenshot%202024-04-23%204606.png)



Oops! It's password-protected.
However, as the challenge name suggests, Hashcat may work for the password cracking of this zip file.

Let us see how does HASHCAT work:

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FMISC%2FHashcat%20the%20Ripper%2FScreenshot%202024-04-23%205405.png)



It looks like we need the password hash here. Hence, we'll use the built-in John the Ripper function called ZIP2JOHN.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FMISC%2FHashcat%20the%20Ripper%2FScreenshot%202024-04-23%205627.png)



This will extract the password hash of the zip file and store it in the hash.txt file, and the result is

**$pkzip$1*1*2*0*7b*75*69101057*0*42*8*7b*6604*fcdbabf65d930d160c148a8dd2620c7611fee1ad550a68b70537ba045dbbf5d975e93d6537ac1f1cd2f903d1855ded458c9a0d3528d23cb8d33f1cd49b4e981a90de7a0e64b415f847b7e1550683ff3084d0349d7433b70eca29657d4f1dcad5468241498800c649ef6445fedecae3ae57df8b605d30bf3ecb01a6*$/pkzip$**
