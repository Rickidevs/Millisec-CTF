Hi, this is a write up for an interesting room from TryHackMe called ‘Millisec’.

**Exploitation**
First, we will start by scanning the given IP.We can see that 3 ports are open.


Now, let’s scan these ports more deeply.


And we see that anonymous access is enabled on the FTP port on port 21.


I accessed the admin panel and searched for ways to get in, but I couldn’t succeed. Maybe this is a rabbit hole.


I returned to the homepage and tried to register, then I noticed that the user ID is visible in the URL, and I tried to change it.


I set the ID value to 0, and an image appeared in front of me.


Let’s take a look at the metadata of the image.


I went to the directory found from the metadata, and there is a page used to execute commands there.


Maybe there is a command injection vulnerability here. To check this, I wrote a command that could bring me a reverse shell.



**Privilege Escalation**
First, I ran the sudo -l command on the system and saw that I could run the /home/millisec/simba file as the millisec user without a password. Then, I used cp /bin/bash /home/millisec/simba to copy the /bin/bash shell to the simba file. After that, I ran /home/millisec/simba as the millisec user with the command sudo -u millisec /home/millisec/simba.


After switching to the millisec user, I ran the sudo -l command and saw that I could run /usr/bin/perl /root/*.pl as the root user. Then, I executed the following commands:touch perl.pl && echo 'print do { local $/; open my $f, "<", "/root/root.txt" or die; <$f> };' > perl.pl && chmod +x perl.pl

This created a file named perl.pl, which was designed to read the contents of the root.txt file. I then gave execution permissions to this file. Finally, I ran the following command:sudo -u root /usr/bin/perl /root/../home/millisec/perl.pl

This allowed me to successfully read the contents of the root.txt file.

Upon analyzing the last command, we can observe that the file path needs to start with /root and end with .pl. However, by adding .., we managed to execute the file we created under the millisec user's home directory as the root user.


I hope you had fun solving this CTF!
