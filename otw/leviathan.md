**leviathan0:**
We can use `ls -a` to list the directories inside of the home. Here, we find a `.backup` folder, and we can find a `bookmarks.html file inside of it.`

The `bookmarks.html` file is very long and contains may html entries of different websites.

We can use grep to find a pattern in the file related to our desired password. 

Running `grep "leviathan1" bookmarks.html` will give us:

```
<DT><A HREF="http://leviathan.labs.overthewire.org/passwordus.html | This will be fixed later, the password for leviathan1 is 3QJ3TgzHDq" ADD_DATE="1155384634" LAST_CHARSET="ISO-8859-1" ID="rdf:#$2wIU71">password to leviathan1</A>
```

Looking at the entry, our password is ***