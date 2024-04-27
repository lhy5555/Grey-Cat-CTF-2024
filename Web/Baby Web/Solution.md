# The challenge

![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-23%20200118.png)





# The Analysis
![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20123440.png)






Upon clicking `Admin` tab, it says that you are not authorized.


![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20123659.png)






Zooming into the cookies being used by the site, we noticed a session cookie. 


![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20123847.png)






Let's decode the cookie and see what it says. As the challenge description suggests that it uses flask, we will need the help of this tool [here](https://github.com/noraj/flask-session-cookie-manager)


![image](.%2F..%2F..%2F..%2FChallenge%20Image%2FWeb%2FBaby%20Web%2FScreenshot%202024-04-27%20124338.png)






It decodes to `{"is_admin":false}`. This means to get the flag, we will have to find a way to change the session cookie to be `{"is_admin":true}`. However, we now have a problem here, because flask session cookie requires a secret key to sign the cookie. Hence, we shall look at the source code to try to look for something interesting
