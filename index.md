# Welcome (I'm in the process of writing this project. It is not finished)

Here I have taken away all the existing methods of privilege escalation in UNIX systems. <br>
These methods will be useful for CTFs, Pentester work and Certifications.<br>
I will try to present them, explain them and discuss them thoroughly.<br>

# Testing Legend

1. <a id="Methods"> The methods I use for escalation in most cases </a>
2. <a href="#sudo-l"> Sudo -l </a>


## Escalation methods I will use (of course there are exceptions)

```
chmod u+s /bin/bash
cp /root/.ssh/id_rsa /tmp/id_rsa
cp /etc/shadow /tmp/shadow
usermod -aG sudo <username>
```

1. Assigning the SUID bit to the `/bin/bash` file. By executing `/bin/bash -p` we execute the command with the SUID bit. <br>
2. copy /root/.ssh/id_rsa to /tmp/id_rsa <br>
3. copy /etc/shadow to /tmp/shadow <br>
4. add user <username> to the **sudo** group <br>
<br>
<a id="sudo-l"></a>        
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
        <summary>DID YOU KNOW</summary>
  
You can edit this file using the commands: <br>
        
```
sudo nano /etc/sudoers
sudo visudo 
```

<br> 
 You can change `nano` to any text editor like `vi`, `vim`, `gedit`, `sumlime`. 
</details>   
<br>
### Let's discuss it step by step:
        
```
   .------------------------- The first field indicates the username that the rule will apply to (adamski).     
   |
   |     .------------------------- The “ALL” indicates that this rule applies to all hosts.
   |     |
   |     |      .-------------------- This “bartek” indicates that the root user can run commands as all users.
   |     |      |
   |     |      |     .--------------- This “bartek” indicates that the root user can run commands as all groups. 
   |     |      |     |
   |     |      |     |         .---------- By default, sudo requires that a user authenticate him or herself before running a command. This behavior can be modified via the NOPASSWD 
   |     |      |     |         |        
   |     |      |     |         |        .----- The **/bin/sh** indicates these rules apply to run **/bin/sh** command.
   |     |      |     |         |        |
   |     |      |     |         |        |        
adamski ALL=(bartek:bartek) NOPASSWD:/bin/sh 
        
```
        
        
 

### Simple example of sudo -l
        
<img width="1224" alt="image" src="https://user-images.githubusercontent.com/108422861/176664752-3da09ab2-eec1-4297-b910-38ca241a7fb3.png"><br><br>
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
 ![image](https://user-images.githubusercontent.com/108422861/176670970-d4e39747-ba42-487b-9bdc-a275ffdca168.png) <br><br>
     
Here we literally do copy+paste <br>

<img width="1227" alt="image" src="https://user-images.githubusercontent.com/108422861/176671501-1400f5e3-16f4-434a-9acc-d95ef26a68bc.png"><br>
        
It works :)  <br><br>
    

## sudo -l "with the additional condition"
        
In this case, we will look at the case when, in addition to the binary file itself, we have an additional condition. <br>
<img width="961" alt="image" src="https://user-images.githubusercontent.com/108422861/176696469-ff45b900-09bc-4c09-86a2-b0e326150b7e.png"> <br>
We see that we can, as the root user, run the perl script in location `/opt/backup.pl`. <br><br>

We check what is in this file and what permissions we have. <br>
<img width="445" alt="image" src="https://user-images.githubusercontent.com/108422861/176696885-f0b48dec-6286-454f-a314-68f0c3a5f4da.png"> <br>
We can see that the script makes a copy of the `/etc/shadow` file to `/dev/null`. <br>
File permissions:
        ```
        -rw-r--rw- 1 root root 53 Jun 30 09:56 /opt/backup.pl
        ```
<br>
This means that everyone has write permissions on this file so our task is simple. We need to write any content to the file <br>
We start by executing a simple command to make sure everything works. <br>
![image](https://user-images.githubusercontent.com/108422861/176699837-e80c8532-df98-4379-aa94-191a7dd5eaca.png) <br> <br>
Then we can raise our eligibility. My favorite method is shown in the image below. I like them a lot because they are short and quick. <br>
<img width="468" alt="image" src="https://user-images.githubusercontent.com/108422861/176701626-b52c6c41-39e3-430f-a474-7a970d769929.png"> <br>
Now let me explain what I did: <br>
        1. I saved the contents of my malicious code to a file. <br>
        2. I showed that the code in the file has changed and showed the permissions of the `/bin/bash` binaries. <br>
        3. I ran the script with elevated privileges and I showed that it worked. <br>
        4. I used `/bin/bash -p` because that's how I ran it with the SUID bit. <br>
        <br>
        

## sudo -l "with the additional condition" hijacking
        
There may be an infinite number of these examples, but I would like to show you only three. I hope that everyone after seeing them will be able to understand the logical idea of this method. <br>

Te simplest of the three: <br>
1. We are executing a script that we cannot control, but it is executing a script over which we have control. <br>        
This doesn't quite fit the rest of the cases, but I wasn't sure where to put it (it kind of fits the case above). <br> <br>        
        
<img width="954" alt="image" src="https://user-images.githubusercontent.com/108422861/176918300-3d0768a1-71a1-4ddb-b887-f4fb9b9202a8.png"> <br>
We can see that our script /opt/backup.pl runs another script in the directory /home/adamski/copy.sh (which in this case is our home directory) <br> <br>
First, we check the permissions and content of the file. <br>
 <img width="547" alt="image" src="https://user-images.githubusercontent.com/108422861/176917119-2655e61d-80e3-4b3c-a5f9-cdf02f10e085.png"> <br>
We can see that it will be easy as we have full control over this file (we own it and it owns our group). <br><br>
Here we have several options for you to choose from. We can do as before with the perl script, but we can also delete the file and add another one with the same name :D <br><br>
<img width="713" alt="image" src="https://user-images.githubusercontent.com/108422861/176918655-897e1662-db05-4be6-b622-bc87b8ae06ae.png"><br><br>
1. I remove the file + show that it is not there <br>
2. I create the counters and show the permissions of the file that I will modify. <br>
3. I add my malicious code to a bash script that I control. <br>
4. I execute the script and show that I got root. <br>
  
        
## crontab
aaa

## SUID
aa

## simple function hijacking
aaa
## race condition
aaa
## Other services running like another user

## Known/Popular kernel exploits


