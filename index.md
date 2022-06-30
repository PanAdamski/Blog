# Welcome 

Here I have taken away all the existing methods of privilege escalation in UNIX systems. <br>
These methods will be useful for CTFs, Pentester work and Certifications.<br>
I will try to present them, explain them and discuss them thoroughly.<br>


## Escalation methods I will use (of course there are exceptions)

```
chmod u+e /bin/bash
cp /root/.ssh/id_rsa /tmp/id_rsa
cp /etc/shadow /tmp/shadow
usermod -aG sudo <username>
```

1. Assigning the SUID bit to the `/bin/bash` file. By executing `/bin/bash -p` we execute the command with the SUID bit. <br>
2. copy /root/.ssh/id_rsa to /tmp/id_rsa <br>
3. copy /etc/shadow to /tmp/shadow <br>
4. add user <username> to the **sudo** group <br>
<br>
        
# sudo -l

Let's start with the simplest method I know `sudo -l`. <br>
A site that collects practically all the ill-configured binaries in linux [GTFOBins](https://gtfobins.github.io/).<br>
Generally, this page contains all the escalation possibilities in this way and I recommend using it even if our escalation method is a bit more difficult (more on that later).<br>
Let's start with a simple example. I will discuss it in detail.<br>
The simplest configuration that exists is shown in the screenshot below.<br><br>

<kbd>
<img src="https://user-images.githubusercontent.com/108422861/176632127-5e49e0ba-33dc-448b-9127-791be0883853.png">
</kbd><br> <br>
With this configuration, the user can do everything with elevated privileges. In this case with the privileges of the <b>root</b> user. <br><br>
However, the most common situation you will encounter will be this:
<kbd> <img width="1229" alt="image" src="https://user-images.githubusercontent.com/108422861/176641545-cccd96ee-8505-486e-a0eb-8a1c14978afa.png"></kbd><br><br>
If we do not specify `-u`/`-g` switch the command will always want to run as **root** so in this case we failed, because in this configuration we can execute the command as user **bartek** <br>
<img width="1225" alt="image" src="https://user-images.githubusercontent.com/108422861/176662778-bfdab3b8-1ec8-451f-b0e1-0ec295ccbeb7.png"> <br>
<br>
To see an effect like the one above we need to add in the `/etc/sudoers` file what is below.
```
adamski ALL=(bartek:bartek) NOPASSWD:/bin/sh
```
<br>
<details>
        <summary>CURIOSITY (expand)</summary>
  
DID YOU KNOW, you can edit this file using the commands: <br>
        ```
        sudo nano /etc/sudoers
        sudo visudo 
       ```
        
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

### Simple example of sudo -l
<img width="1224" alt="image" src="https://user-images.githubusercontent.com/108422861/176664752-3da09ab2-eec1-4297-b910-38ca241a7fb3.png"> <br>
User **adamski** can execute the `/usr/bin/nano` command as user **bartek**. <br>
Go to our [GTFOBins](https://gtfobins.github.io/) page and type in the command of interest ([nano](https://gtfobins.github.io/gtfobins/nano/#sudo)). <br> <br>
Moving to the sudo tab, we see what we need to do to get the shell <br>
![image](https://user-images.githubusercontent.com/108422861/176664116-82f33529-6ec1-44f2-b668-c9b17906756c.png)<br><br>

Our command differs slightly so we have to adjust it <br>
<img width="1221" alt="image" src="https://user-images.githubusercontent.com/108422861/176663552-ce443b7c-ac0e-4d44-9028-42b96f192864.png"><br>

we do what the instructions say i.e. press `ctrl+r`, `ctrl+x` and type `reset; sh 1>&0 2>&0` `/press enter/` <br>
<img width="1230" alt="image" src="https://user-images.githubusercontent.com/108422861/176666141-cb555007-9e0d-449c-b9c7-1f885ee85c5d.png"> <br>

And we see that we have a shell as user bartek (I recommend pressing the spacebar several times, because the shell obtained from nano is very ugly and we have to "click through" before we see anything) <br>
<img width="1176" alt="image" src="https://user-images.githubusercontent.com/108422861/176666227-97ffc082-497b-4d98-ba79-e9b4336962bc.png"><br>

        There are also much simpler examples like **python**. <br>
 ![image](https://user-images.githubusercontent.com/108422861/176670970-d4e39747-ba42-487b-9bdc-a275ffdca168.png) <br>

        <br>
        Here we literally do copy+paste <br>
        <img width="1227" alt="image" src="https://user-images.githubusercontent.com/108422861/176671501-1400f5e3-16f4-434a-9acc-d95ef26a68bc.png"><br>
        
It works :)  <br><br>
        
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



