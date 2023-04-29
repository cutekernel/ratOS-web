# ratOS-web

More details coming soon.

ratOS is a very secure unhackable Operating System [accessible](https://labs.hackxpert.com/CommandInjection/) from the internet. 
Kudos to XSSRat for putting this application together.

## Understanding the Web Application and its funtionalities:

This step is often overlooked because of its simplicity of many biaises and assumptions one may have about a particular application. However, I believe that it is fundamental in order to properly test the security of any system, application, etc.

When you first access the website you are presented with a cli.
![image](https://user-images.githubusercontent.com/74272629/235278237-4a445c49-5bd7-4465-84cc-f57fb7d3e23e.png)

Use the help command to see which commands this terminal supports.
![image](https://user-images.githubusercontent.com/74272629/235278248-9d6ff8be-5edc-4487-b2c2-37f5d827b7a5.png)

It is common practice to know which user you are logged in as. You can do so by unning the whoami command. 
![image](https://user-images.githubusercontent.com/74272629/235278244-cc40113f-f669-4329-bc2c-a505134a0b73.png)

Subsequent to this discovery, I performed further testing by attempting to execute the remaining commands to gain a comprehensive understanding of their respective functionalities. Through this testing, I observed that certain commands did not execute as expected, while others did.

It is worth noting that in the case of the history command, I found to be operational and revealed sensitive information, namely the username and password of the root user.
![image](https://user-images.githubusercontent.com/74272629/235278403-b0a5c06b-bb04-4b28-848a-66dcd89d16e2.png)

I used these credentials to authenticate as root. Once you do, you will be presented with a "Login Successful". In addition to an error message, which we will go back to in a minute.
![image](https://user-images.githubusercontent.com/74272629/235278474-6f4c76ef-6bf7-47e0-963d-3e00cb2c8529.png)

You can verify that you are root
![image](https://user-images.githubusercontent.com/74272629/235278599-789ebbae-370c-45ac-b1fb-b1aaf6ce93b7.png)
 By the way there is another easy why to access the root user by simply using the su command -- su root, as shown below.
 
![image](https://user-images.githubusercontent.com/74272629/235278638-c1f7f4f2-dff7-44c6-bf4e-a92d56c90acd.png)

After some trial and error, I determined that only the following commands function: whoami, su, help, history and scream.

## Functionality Bugs
I identified a critical flaw in the input validation process of ratOS, which allows for the circumvention of user input controls using special characters like '|' and URL-encoded characters. I proceeded to perform a simple test that revealed the vulnerability, as demonstrated below. By feeding the terminal with two commands, it was possible to execute only the last command, leaving the system susceptible to exploitation.
![image](https://user-images.githubusercontent.com/74272629/235278889-354a6b7c-a9c8-45bc-b22c-a64fa2dc8f0b.png)


## Error Messages

So far I have encountered two error messages:

Using the login command:
![image](https://user-images.githubusercontent.com/74272629/235278979-eb4c8b4f-40f9-4c03-98a7-faef12f10992.png)

Using the su command without providing a username:
![image](https://user-images.githubusercontent.com/74272629/235278972-85ef5acd-2a90-4764-a9bd-224241595224.png)


## Digging up a tad more

Using Burp Suite as a Web Proxy, I discovered that the command is passed in the HTTP Body as `command=`
![image](https://user-images.githubusercontent.com/74272629/235279071-59ddbfbe-7426-41f6-bb98-53c197dac8ba.png)

I leverage that to fuzz this parameter and find a suitable XSS payload. I took into consideration the fact that the user's history command would record and display the payload. By executing arbitrary Javascript code, I was able to store the payload in the user's history. My only limitation was that this exploit only had a local impact on the user.

## Leveraging CSRF with RXSS

Since the web application does not have an anti-csrf token, I leveraged this as an attack vector.
![image](https://user-images.githubusercontent.com/74272629/235279782-abed9273-dc04-4f23-af9f-973761830e98.png)

I initiated an HTTP server with a straightforward HTTP form. Once the victim clicked on the link, the form would submit itself, effectively compromising the ratOS session established between the victim and the ratOS server from the beginning. This malicious activity is further demonstrated in the accompanying GIF.

![a19e2df2dfcf24a80a6fc52f775387b4](https://i.gyazo.com/e144d8b47f0481c60d741bc9ddc255f4.gif)

## How does that relate to OWASP Top 10





