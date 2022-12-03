#### Manually Creating a Base Repository

```
[root@ip-172-31-84-51 ~]# mkdir test
[root@ip-172-31-84-51 ~]# cd test
```
#### In this directory, we’ll put some .rpm packages. In general, we can also copy several packages from an RHEL DVD. We now need a tool called createrepo for creating a repository. The createrepo command scans a directory containing rpm packages.

```
[root@ip-172-31-84-51 test]# dnf install createrepo
```

#### After installing createrepo, we need to pass our test directory as an argument to the createrepo command:

```
[root@ip-172-31-84-51 ~]# createrepo test
Directory walk started
Directory walk done - 1 packages
Temporary output repo path: test/.repodata/
Preparing sqlite DBs
Pool started (with 5 workers)
Pool finished
```

#### Subsequently, after running the above command, we get a new directory called repodata inside of our demo directory.
```
[root@ip-172-31-84-51 ~]# cd test/
[root@ip-172-31-84-51 test]# ls -al
total 260
drwxr-xr-x. 3 root root     73 Dec  3 05:19 .
dr-xr-x---. 5 root root    130 Dec  3 05:15 ..
-rw-r--r--. 1 root root 260264 Nov 25 13:52 bind-pkcs11-utils-9.11.36-7.el8.aarch64.rpm
drwxr-xr-x. 2 root root   4096 Dec  3 05:19 repodata
```



#### Now let’s create a repo file in the /etc/yum.repos.d/ directory with the .repo extension:

```
$ cd /etc/yum.repos.d/
$ sudo vi test.repo


[root@ip-172-31-84-51 yum.repos.d]# cat test.repo 
[testrepo]
name=My Test Repo
baseurl=file:///root/test/
enabled=1
gpgcheck=0
```

#### Checking Repolist :
```
[root@ip-172-31-84-51 yum.repos.d]# dnf repolist
Updating Subscription Management repositories.
Unable to read consumer identity

This system is not registered with an entitlement server. You can use subscription-manager to register.

repo id                      repo name
epel                         Extra Packages for Enterprise Linux 8 - x86_64
rhel-9-appstream-rhui-rpms   Red Hat Enterprise Linux 9 for x86_64 - AppStream from RHUI (RPMs)
rhel-9-baseos-rhui-rpms      Red Hat Enterprise Linux 9 for x86_64 - BaseOS from RHUI (RPMs)
rhui-client-config-server-9  Red Hat Enterprise Linux 9 Client Configuration
testrepo                     My Test Repo
```


#### The first three lines below the repository ID section are essential. These are the minimum ones required for creating a repo file. The line [testrepo] is a unique identifier that identifies a repo. The second line represents the name of our repository. Here the name of this repo file is My Test Repo.The baseurl line shows the location of the repository. In some cases, like the EPEL repo  we see a metalink entry and baseurl as commented. This is because metalinks save a user from using malicious mirrors.

#### The enabled entry has a value of either 0 or 1. In general, enabled=1 means the repo is enabled/active, and 0 here means it’s not enabled/inactive. Besides these, we have a few more options to configure this file. For instance, we can configure the GPG block. A GPG can be used as a means to sign or verify packages. This way, we can detect and keep off unreliable packages. Just like the case of enabled, gpgcheck=1  means that it’s enabled, and 0 implies it’s disabled.


#### Installing  a Package Using Custom Repository 

```
yum --disablerepo="*" --enablerepo="testrepo" install package name
