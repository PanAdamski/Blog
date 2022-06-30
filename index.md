# Welcome 

Here I have taken away all the existing methods of privilege escalation in UNIX systems. <br>
These methods will be useful for CTFs, Pentester work and Certifications.<br>
I will try to present them, explain them and discuss them thoroughly.<br>

# sudo -l

Let's start with the simplest method I know `sudo -l`. <br>
A site that collects practically all the ill-configured binaries in linux [GTFOBins](https://gtfobins.github.io/).<br>
Generally, this page contains all the escalation possibilities in this way and I recommend using it even if our escalation method is a bit more difficult (more on that later).<br>
Let's start with a simple example. I will discuss it in detail.<br>
The simplest configuration that exists is shown in the screenshot below.<br><br>

<table><tr><td> 
<img src="https://user-images.githubusercontent.com/108422861/176632127-5e49e0ba-33dc-448b-9127-791be0883853.png"/>
</td></tr></table><br> <br>
With this configuration, the user can do everything with elevated privileges. In this case with the privileges of the <b>root</b> user. <br><br>
However, the most common situation you will encounter will be this:
<kbd> <img width="1229" alt="image" src="https://user-images.githubusercontent.com/108422861/176641545-cccd96ee-8505-486e-a0eb-8a1c14978afa.png"></kbd><br><br>

To see an effect like the one above we need to add in the `/etc/sudoers` file what is below.
```
adamski ALL=(bartek:bartek) NOPASSWD:/bin/sh
```
<br>
<details>
        <summary>CURIOSITY</summary>
  
DID YOU KNOW, you can edit this file using the commands: <br>
        
        `sudo nano /etc/sudoers` <br>
        `sudo visudo` <br>
        
        
 You can change `nano` to any text editor like `vi`, `vim`, `gedit`, `sumlime`,... 
 </details>   

### Let's discuss it step by step:
```
adamski ALL=(bartek:bartek) NOPASSWD:/bin/sh The first field indicates the username that the rule will apply to (adamski). 
adamski ALL=(bartek:bartek) NOPASSWD:/bin/sh The “ALL” indicates that this rule applies to all hosts.
adamski ALL=(bartek:bartek) NOPASSWD:/bin/sh This “bartek” indicates that the root user can run commands as all users. 
adamski ALL=(bartek:bartek) NOPASSWD:/bin/sh This “bartek” indicates that the root user can run commands as all groups. 
adamski ALL=(bartek:bartek) NOPASSWD:/bin/sh The **/bin/sh** indicates these rules apply to run **/bin/sh** command. 
```


## sudo -l "with the additional condition"




## sudo -l "with the additional condition" hijacking
aaa
## crontab
aaa

## SUID
aa

## simple function hijacking
aaa
## race condition
aaa
## Other services running like another user



