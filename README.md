# Creating an Offline YUM Repository with Apache Server for Cross-System Package Management

**Introduction:**

Managing software updates and installations in environments without internet access can be challenging. In this guide, we'll walk through the process of setting up an offline YUM repository using Apache on RHEL based systems. This repository can be accessed from any system within the local network, providing a convenient and efficient way to update and install packages.

**Step 1:** Install yum-utils and createrepo
```
yum install yum-utils createrepo
```
The `yum-utils` package provides essential tools for repository management, while `createrepo` is used to generate XML-based RPM metadata.

**Step 2:** Download Packages and Dependencies
```
yumdownloader --resolve --destdir=give_name package_name
```
Use `yumdownloader` to download RPM packages along with their dependencies. This is crucial for creating a comprehensive local repository.

**Step 3:** Create the Local Repository
```
cd <directory_path>
```
```
createrepo .
```
Navigate to the directory where the packages are downloaded and use `createrepo` to generate metadata files required by YUM for package management.

**Step 4:** Configure YUM Repository
Now that you have set up the local repository, you can use it on your system. 
```
sudo nano /etc/yum.repos.d/local.repo
```
Add the following content to the file:

```
[name]
name=Local Repository
baseurl=file:///home/<username>/<repository_folder>
enabled=1
gpgcheck=0
priority=1
```
- enabled=1 enables the repository; set to 0 to disable it.
- gpgcheck=0 disables GPG signature checking for simplicity.
- priority=1 prioritizes packages from the local repository.

GPG (GNU Privacy Guard) signature is a digital signature that helps ensure the integrity and authenticity of the packages in the repository.

**Step 5:** Update YUM Metadata Cache
```
sudo yum makecache
```
Use `yum makecache` to create or update the metadata cache, ensuring that YUM recognizes the newly configured local repository.

**Step 6:** Install and Configure Apache
Begin by installing the Apache web server on your system:
```
sudo yum install httpd
```
Start the Apache service
```
sudo service httpd start
```
Grant necessary permissions to the Apache user for the repository directory:
```
sudo chown -R apache:apache /path/to/your/folder
```
**Step 7:** Configure Apache HTTP Server
Edit the Apache configuration file:
```
sudo nano /etc/httpd/conf/httpd.conf
```
Ensure that the Document Root and Directory settings are correct. Adjust them if needed. For example:
```
DocumentRoot "/home/ec2-user"

<Directory "/home/ec2-user/packages">
    AllowOverride None
    Require all granted
</Directory>
```
Save the changes and restart the Apache server:
```
sudo service httpd restart
```
**Step 8:** Configure YUM Repository on other Systems
On the systems where you want to access the local repository, add the following to `/etc/yum.repos.d/local.repo`

```
[Local Repository]
name=Local Repository
baseurl=http://ipaddress
enabled=1
gpgcheck=0
priority=1
```
Replace ipaddress with the actual IP address of your system hosting the repository.


**Conclusion:**

By combining Apache and YUM, you've created a local offline repository accessible within your network. This setup facilitates seamless updates and installations on systems without direct internet access. Customize the configurations based on your specific network and security requirements, and enjoy the convenience of managing packages in an isolated environment.