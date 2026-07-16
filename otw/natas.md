**natas0**

The hint on the webpage says "You can find the password for the next level on this page". Hitting F12 and inspecting the HTML, we can see in comments: `"The password for natas1 is ***`

Using this as our password, we can progress to the next level. 

**natas1**

The webpage says right-clicking is disabled, however we can still use F12 to access the HTML. In comments, it says: `"The password for natas2 is ***`

Using this as our password, we progress. 

**natas2**

The webpage says there is nothing on this page. Upon hitting F12 and inspecting the HTML, we see a pixel.png listed under the path `files/pixel.png`. To investigate, we input our url with the path of `/files` to see what files are on the server.
![[Pasted image 20260128134620.png]]
From here, we can open the users.txt and view the following text: 
```
# username:password
alice:BYNdCesZqW
bob:jw2ueICLvT
charlie:G5vCxkVV3m
natas3:3gqisGdR0pjm6tpkDKdIWO2hSvchLeYH
eve:zo4mJWyNj2
mallory:9urtcpzBmH
```

We can use the entry: `natas3:***` as our username and password for the next level. 

**natas3**

Inspecting the HTML, we find this message: `No more information leaks!! Not even Google will find it this time...`. Since it mentions that google can't find it, we can take it as a hint to inspect the `robots.txt` file. 

Following this path, we see this text in `robots.txt`:
```
User-agent: *
Disallow: /s3cr3t/
```

Seeing the Disallow entry, we can check out the `/s3cr3t` path and find the following text stored within the `s3cr3t` folder: 

`natas4:***`
`
We'll use this to progress to the next level.

**natas4**

The webpage displays: 

"Access disallowed. You are visiting from "http://natas4.natas.labs.overthewire.org/index.php" while authorized users should come only from "http://natas5.natas.labs.overthewire.org/". This message is presented after we refresh the webpage. 

Taking this as a hint, we can guess that it could have something to do with referer policy and permissions when it comes to who is visiting the website. Using Burpsuite to intercept a request, we'll inspect the HTTP request headers when we access the site.

We'll use Burpsuite as a proxy and intercept an HTTP request from the server. 

Inspecting the request received upon refreshing the webpage, we can see the referer header: 
```
GET /index.php HTTP/1.1
Host: natas4.natas.labs.overthewire.org
Cache-Control: max-age=0
Authorization: Basic bmF0YXM0OlFyeVpYYzJlMHphaFVMZEhydEh4enlZa2o1OWtVeExR
Accept-Language: en-US,en;q=0.9
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://natas4.natas.labs.overthewire.org/index.php
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
```

We can change the referer to be `http://natas5.natas.labs.overthewire.org` and forward the request back to the server.

Now, the webpage displays:
```
Access granted. The password for natas5 is ***
```

We'll use this to progress to the next level. 

**natas5**

