# KBID X - Session Prediction

## Running the app

```
$ sudo docker pull blabla1337/owasp-skf-lab:file-upload
```

```text
$ sudo docker run -ti -p 127.0.0.1:5000:5000 blabla1337/owasp-skf-lab:file-upload
```
Or simply:
```text
$ python SP.py
```

{% hint style="success" %}
 Now that the app is running let's go hacking!
{% endhint %}

![Docker Image and write-up thanks to TelecoLabs!](telecolabs.png)

Alberto Rafael Rodríguez Iglesias - www.telecolabs.com

## Reconnaissance

The session prediction attack focuses on predicting session ID values that permit an attacker to bypass the authentication schema of an application. By analyzing and understanding the session ID generation process, an attacker can predict a valid session ID value and get access to the application.

In the first step, the attacker needs to collect some valid session ID values that are used to identify authenticated users. Then, he must understand the structure of session ID, the information that is used to create it, and the encryption or hash algorithm used by application to protect it. Some bad implementations use sessions IDs composed by username or other predictable information, like timestamp or client IP address. In the worst case, this information is used in clear text or coded using some weak algorithm like base64 encoding.

In addition, the attacker can implement a brute force technique to generate and test different values of session ID until he successfully gets access to the application.


![](inicio.PNG)

When start the application we can see that we have upload functionality and this file will be stored on the server. First lets try to upload a valid image to see how the application behaves.

![](.gitbook/assets/screen-shot-2019-03-05-at-16.18.19.png)

If we inspect the request with an intercepting proxy we can see that the application is performing a POST request that results in a data mutation, storing our file on the server.





## Exploitation

Now lets try to upload a different file for example a XML file and see how the application will behave.

![](.gitbook/assets/screen-shot-2019-03-05-at-16.19.03.png)

As you can see this file is not allowed to be uploaded. Also when we upload files idealy we want to be able to access the files. Let's see if we can find the uploaded file back in the application.

The tool for example we can use for this is called Wfuzz and can be started with the following command:

```bash
wfuzz -c -z file,/usr/share/wfuzz/wordlist/general/common.txt --hc 404 http://localhost:5000/FUZZ
```

Unfortunately Wfuzz doesnt find any upload directories where our file is being stored. So lets have a look at the application what other folders are available and are accessible.

![](.gitbook/assets/screen-shot-2019-03-07-at-12.04.08.png)

The application uses different directories to store the stylesheets and images that are being used by the application. Lets see if we can use a path traversel injection to upload our files inside these folders.

![](.gitbook/assets/screen-shot-2019-03-05-at-16.23.42.png)

We have tried the path traversal directory injection and we got the message back from the application that the file was successfully uploaded. Now lets see if we can access our file.

```text
http://localhost:5000/static/img/Test.png
```

And there it is our file was being uploaded successfully in the static/img folder of the application and because this is accessible by the application we can access our file.

![](.gitbook/assets/screen-shot-2019-03-05-at-16.24.51.png)

Now try also to upload other files like \*.exe or \*.html and do a Cross Site Scripting attack. Maybe also try to upload a very big file and see if the application is also protected agains that type of attack.

## Additional sources

https://www.owasp.org/index.php/Session_Prediction
