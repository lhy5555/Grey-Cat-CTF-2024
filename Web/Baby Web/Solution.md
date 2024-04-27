# The challenge

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-23%20200118.png)





# The Analysis
![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20123440.png)






Upon clicking `Admin` tab, it says that you are not authorized.


![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20123659.png)






# The solution
Zooming into the cookies being used by the site, we noticed a session cookie. 


![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20123847.png)






Let's decode the cookie and see what it says. As the challenge description suggests that it uses flask, we will need the help of this tool [here](https://github.com/noraj/flask-session-cookie-manager)


![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20124338.png)






It decodes to `{"is_admin":false}`. This means to get the flag, we will have to find a way to change the session cookie to be `{"is_admin":true}`. However, we now have a problem here, because flask session cookie requires a secret key to sign the cookie. Hence, we shall look at the source code to try to look for something interesting.


![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20124912.png)






Looks like we have found the FLASK secret key used for signing cookie, and the secret key is `baby-web`. Hence, we will now proceed to sign the session cookie with the value of `{"is_admin":true}` using that secret key.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20130018.png)





**ERROR! Let's do some troubleshooting here. Noticed that the decoded cookie is in bytes format. Now that we have the secret key, we shall try to decode again with this secret key.**

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20130215.png)





Notice that the formatting is different here. `{"is_admin": False)` instead of `{"is_admin":false}`. Hence, we shall follow the same format here to encode `{"is_admin": True)` using the same secret key.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20130426.png)






**Successful. Now let's overwrite the session cookie with this and refresh the page**

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20130548.png)






![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20130642.png)






**BOOM! We now have admin access to the page. However, the flag is still hidden. Let's look at the page source to see if there is hidden information.**

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20130838.png)





There seems to be hidden content within this page, which is the `Modal` section. 


**Method 1: Changing the CSS Class**
Notice that the visible content of the site is under the CSS Class `container` while the Modal section is under the CSS Class `modal fade`. Hence, we shall modify the CSS class of this section to be `container` as well so that we can see the hidden content.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20131607.png)





The hidden content has appeared. Clicking on the "Here is an even more secret button." reveals the flag.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20131802.png)






**Method 2: Navigating to the /flag directory**
Notice that the page source shows that there is another directory /flag in **line 61** of the page source. Hence, we shall navigate to the /flag directory.

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20132123.png)






The flag is `grey{0h_n0_mY_5up3r_53cr3t_4dm1n_fl4g}`
