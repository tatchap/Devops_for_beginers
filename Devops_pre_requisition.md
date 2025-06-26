# Working Your Way Through the CLI

How many directories and files are present in /home/thor/test_dir directory?

To count files: 
```
find /home/thor/test_dir -type f | wc -ldf
```
To count directories:
```
find /home/thor/test_dir -type d | wc -l
```

Which text file is not present under /home/thor/test_dir/ directory?

```
cd /home/thor/test_dir/
```
Create an empty file inside /home/thor/ directory.

```
touch empty_file.txt
```

Create file with content inside /home/thor/ directory.

```
echo "This is the file content." > /home/thor/contents_file.txt
```
then ```cat contents_file.txt``` for verifications

Create an empty directory inside /home/thor/ directory.
```
mkdir empty_dir
```
Create directory hierarchy inside /home/thor/ directory.

```
mkdir -p /home/thor/asia/india/bangalore
```

Copy target file to target directory


target file name : /home/thor/asia/bangalore.txt

target directory : /home/thor/asia/india/bangalore

```
cp /home/thor/asia/bangalore.txt /home/thor/asia/india/bangalore/
```

Copy source directory to target location
source directory name : /home/thor/asia/india/bangalore
target location: /home/thor/

```
cp -r /home/thor/asia/india/bangalore /home/thor/
```

Remove target file from target directory
target file name : bangalore.txt
target directory : /home/thor/asia/

```
rm /home/thor/asia/bangalore.txt
```

Remove target directory and its contents.
target directory : /home/thor/asia/india/bangalore

```
rm -r /home/thor/asia/india/bangalore
```

If you also want to skip confirmation prompts (use with caution), add the -f flag:
```
rm -rf /home/thor/asia/india/bangalore
```

# Package Management

What is full form of RPM in linux environment

```
Red Hat Package Manage
```

Which of the following rpm is not installed on host01?

```
rpm -q package-name
```

Install ftp with rpm.
We have downloaded rpm file inside /opt/ directory.

```
sudo rpm -ivh /opt/ftp*.rpm
```

Remove ftp with rpm.
You can get exact package name by rpm -qa | grep ftp
```
sudo rpm -e ftp-0.17-89.el9.x86_64
```

Install maven with yum
```
sudo yum install maven
```
Which version of maven is installed with yum on host01 server in previous step? Ignore epoch version number
```
mvn -version
```

Remove maven with yum as we will install lower version in next step
```
sudo yum remove maven
```

Install maven with yum but version should be maven-1:3.6.3-19.el9.noarch

```
sudo yum install maven-1:3.6.3-19.el9.noarch
```

# More Linux Commands

Which user is current user on host01 server?
```
whoami
```

What is id of thor user?
```
id thor
```
Which command you will use to switch to ansible user?
```
su ansible
```
Which command you will use to login to other server with IP 172.16.238.3 and thor user?
```
ssh thor@172.16.238.3
```
Which of the following file is present under /root directory?
Note: Normal user don't have permission to view files under /root directory so you will have to gain higher privileges to see files under /root.
```
sudo ls -l /root
```

Which command can't be used to download file over internet?
```
ping or cat
```

Download target file to host01
target file name : dummy.pdf
target file URL: https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf
target directory: /home/thor
```
wget -P /home/thor https://www.w3.org/WAI/ER/tests/xhtml/testfiles/resources/pdf/dummy.pdf
```

Where can you find OS information of linux servers?
```
cat /etc/os-release
```

Which OS is running on host01 server?
```
hostnamectl (altenative to the previous question)
```

Which version of CentOS is running on host01 server?
```
cat /etc/centos-release
```

# Services

Which of the following is not valid systemctl command? (systemctl is a command-line utility used to control and manage systemd services and the system state on modern Linux distributions)
```
systemctl httpd stop
```

What is the status of httpd service?
We have pre-installed httpd (apache package) which is used for hosting web server.
NOTE: Don't forget to make use of the sudo command.
```
sudo systemctl status httpd
```

Lets start httpd service so that our host01 can act as web server.
We have pre-installed httpd (apache package) which is used for hosting web server.
NOTE: Don't forget to make use of the sudo command.
```
sudo systemctl start httpd
```

Now we have to enable httpd service so that it starts automatically when system boots up and we dont need to manually start the service.
```
sudo systemctl enable httpd
```

Now we decided to use dedicated python flask app instead of apache so please stop httpd service.
```
sudo systemctl stop httpd
```

Now we have to disable httpd service so that it doesn't start automatically when system boots up.
```
sudo systemctl disable httpd
```

We have added a new python flask based service called app. In which systemd unit file is this service configured?
```
systemctl status app (The output often shows the full path of the unit file being used)
```
What is the status of app service?
```
sudo systemctl status app
```
Look at the service configuration file (systemd unit file) and identify which script is run before the app service starts.
```
sudo systemctl cat app
```

Now lets start app service so that our host01 can act as Flask server as we intended.
```
sudo systemctl start app
```
Now we have to enable app service so that it starts automatically when system boots up and we dont need to manually start the service.
```
sudo systemctl enable app
```

# DNS

At basic level what is DNS used for ?
```
translate human-friendly domain names into IP addresses
```
On a Linux based system, which of the following file can be used to point domains/hostnames to IPs locally?
```
/etc/hosts
```
On a Linux based system, which of the following file contains information about dns server i.e nameserver?
```
/etc/resolv.conf
```
On host01, if we have an entry for app01 in /etc/hosts file like 172.168.238.12 app01 and the DNS server which is used by host01 has 172.168.239.10 as app01's IP then which IP host01 will pick for app01 as per priority.
```
172.168.238.12
```
On host01, point www.google.com to 127.0.0.1 IP address locally.
  Open /etc/hosts with a text editor using sudo:
```
sudo nano /etc/hosts
```
  Then add : 127.0.0.1    www.google.com

On host01, add Google public DNS i.e 8.8.8.8 as a nameserver.
```
sudo nano /etc/resolv.conf
```
  Then add this line : nameserver 8.8.8.8

In www.google.com, which is the top level domain?
```
.com
```
Which of the following is a sub-domain example?
```
maps.google.com
```
On host01 we want to resolve name news to news.yahoo.com automatically without hard coding its entry in /etc/hosts file. Add the required changes on host01.
```
echo "address=/news/98.137.11.163" | sudo tee /etc/dnsmasq.d/news-alias.conf
```

# Java introduction 

Which java version is installed on host01 server?
```
java --version
```
Which tool is used for documenting your app with JDK?
```
javadoc
```
Which tool is used for compiling your app with JDK?
```
javac
```
Which tool is used for debugging your app with JDK?
```
jdb
```
Which component was not part of JDK before version 9 of Java but became part of it from version 9 onwards?
```
jre
```
Help us install Java 20 inside the /opt directory on the app01 server. Use the bob user and the password caleston123 to log in to the app01 server.
```
ssh bob@app01
```
  Download Java 20 (JDK) tarball: 
```cd /tmp
curl -L -O https://github.com/adoptium/temurin20-binaries/releases/download/jdk-20.0.2%2B9/OpenJDK20U-jdk_x64_linux_hotspot_20.0.2_9.tar.gz
```
  Extract it into /opt: 
```
sudo mkdir -p /opt/java
sudo tar -xzf OpenJDK20U-jdk_x64_linux_hotspot_20.0.2_9.tar.gz -C /opt/java
```

















