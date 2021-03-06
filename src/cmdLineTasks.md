---
title: Tasks for the cmd line
---
<script type="text/javascript">(function(w,s){var e=document.createElement("script");e.type="text/javascript";e.async=true;e.src="https://cdn.pagesense.io/js/webally/f2527eebee974243853bcd47b32631f4.js";var x=document.getElementsByTagName("script")[0];x.parentNode.insertBefore(e,x);})(window,"script");</script>

## Install dependencies

```sh
yum install gcc-c++
yum install gnutls-devel
yum install libuuid-devel
yum install cmake
yum install gnutls-utils
```

## Install from tarball

- Installing Taskserver from a tarball is a matter of downloading the tarball, extracting it, satisfying dependencies and building the server
- The next step is to obtain the code. This means getting the Task Server 1.1.0 (or newer) source tarball. You should check for the latest stable release here:

[http://taskwarrior.org/download](http://taskwarrior.org/download)

- You can download the tarball with curl, as an example of just one of many ways to download the tarball.

```sh
curl -LO https://taskwarrior.org/download/taskd-latest.tar.gz
```

## Build

- Expand the tarball, and build the Taskserver.

```sh
tar xzf taskd-latest.tar.gz
cd taskd-latest
cmake -DCMAKE_BUILD_TYPE=release .
...
$ make
...
```

- We will refer to the directory where you extracted the data to as SOURCEDIR (in the example above it is taskd-latest).

## Build Again

If you ever want to build the software again, do some cleanup.

```sh
cd taskd-latest
make clean
...
rm CMakeCache.txt
...
```

## Make Install

Now install Taskserver. This copies files into the right place, and installs man pages.

```sh
make install
```

## Verify Installation

- Run the taskd command to verify that the server is installed, and the location is in your $PATH.

- You should see something like this:

```sh
taskd

Usage: taskd -v|--version
```

## Installation from git-repo

- Installing Taskserver from git is a matter of cloning the git repository and building the server.
- The same dependencies as for installation from tarball apply. Please check the corresponding slides.

## Cloning the repo

```sh
git clone --recurse-submodules=yes \
      https://github.com/GothenburgBitFactory/taskserver.git \
      taskserver.git
```

## Use Stable

It is highly recommended that you build the stable version. This involves simply executing the next command.

```sh
cd taskserver.git
git checkout master
...
```

Only under special circumstances you should build the unstable development version.    

## Special Circumstances

The unstable development version is at no point guaranteed to work or even compile. The only time it does stabilize is right at the end of the development cycle, and in that case, you should wait until the release.

The stable version is always merged to the master branch, which is the default branch, so ordinarily nothing needs to be done. To build an unstable branch, first determine which branch by looking at the available branches:

## Choosing the right branch

```
cd taskserver.git
git branch -a
* master
remotes/origin/1.1.0
remotes/origin/1.1.1
remotes/origin/1.2.0
remotes/origin/HEAD -> origin/master
remotes/origin/master
```

The convention we use is that master represents the stable release. The numbered branches represent the latest development (1.2.0, the 'highest' branch number, ending in '.0') and a patch branch (1.1.1, ending in a non-zero number).

## Development Version

Patch branches are reserved for emergency releases, so in this example you would choose to build 1.2.0 as the latest development branch like this (please not that starting with version 1.2.0 we make use of submodules).

```sh
git checkout 1.2.0
# Branch 1.2.0 set up to track remote branch 1.2.0 from origin.
# Switched to a new branch '1.2.0'
```

## Sub-Modules

```sh
git submodule init
# Submodule 'src/libshared' (https://git.tasktools.org/scm/tm/libshared.git) registered for path 'src/libshared'

git submodule update
# Cloning into 'src/libshared'...

# remote: Counting objects: 2180, done.
# remote: Compressing objects: 100% (1379/1379), done.
# remote: Total 2180 (delta 1641), reused 1018 (delta 796)
# Receiving objects: 100% (2180/2180), 369.13 KiB | 554.00 KiB/s, done.
# Resolving deltas: 100% (1641/1641), done.
# Checking connectivity...
# done.
Submodule path 'src/libshared': checked out '2b0b70d90acb9a3ff3548befab9db8beb85a0c2d'
```

# Now Build from Git

```sh
cd taskserver.git
$ cmake -DCMAKE_BUILD_TYPE=release .
...

$ make
```
In this case the SOURCEDIR is taskserver.git.
    
## Test Build

Having built the server, now build and run the unit tests. Although this is an optional step, it is a good idea to know whether the build works on your platform.

```sh
$ cd test # from SOURCEDIR
$ make
...
$ ./run_all

Pass: 2920
Fail: 0
Skipped: 0
Runtime: 1 seconds

$ cd ..
```

## Interprete the resutls

This example shows that all 2,920 tests pass. If you see test failures, stop and report them.

Note that there are some unit tests that fail if you have not built the latest commit. Seeing 4 test failures may mean all is well. Seeing 30 failures does not.

## Make Install

Now install Taskserver. This copies files into the right place, and installs man pages.

```sh
$ sudo make install
...
```

## Verify your install

Run the taskd command to verify that the server is installed, and the location is in your $PATH. You should see something like this:

```sh
$ taskd

Usage: taskd -v|--version
...

```

## Server Setup

- TASKD-USER
- We assume that you will do all configuration with the taskd user you chose to run the server with.

## Data Location

Configuring the server is straightforward, but needs a little planning.

A location for the data must be chosen and created. The `TASKDDATA` environment variable will be used to indicate that location to all the taskd commands.

```sh
export TASKDDATA=/var/taskd
sudo mkdir -p $TASKDDATA
```

If the TASKDDATA variable is not set, then most taskd commands require the --data ... argument.

## Directory

- Everything the server does will be confined to that directory.
- There are two 'D's in `TASKDDATA`, and omitting one is a common mistake.

- The user that will run the server must have write permissions in that directory.

## Inititialization

Now we let the server initialize that directory:

```sh
taskd init
You must specify the 'server' variable, for example:
taskd config server localhost:53589

Created /var/taskd
```

- It is a good idea to copy the pki subdirectory from your `SOURCEDIR` to your `TASKDDATA` directory.
- If you installed from a package (manager) search for the pki directory, `find / -name pki -type d` (example `/usr/share/taskd/pki/` for Ubuntu).

## Keys & Certificates

Now we create certificates and keys. The command below will generate all the certs and keys for the server, but this uses self-signed certificates, and this is not recommended for production use. This is for personal use, and this may be acceptable for you, but if not, you will need to purchase a proper certificate and key, backed by a certificate authority.

## Assumptions

The scripts make assumptions that are guaranteed to be wrong for you. Specifically the generate.server script has a hard-coded CN entry that is not going to work. You need to edit the vars file, which you find in the pki subdirectory in your SOURCEDIR.

```sh
CN=localhost
```

You will need to modify this value to match your server.

Most probably the result of hostname `-f` is exactly what you need (`yourserver.example.com`).

## Common Name

The value of CN (Common Name) is important.

It is this value against which Taskwarrior validates the servername, so use a value similar to ack.example.com, don't expect that example to work for you. If you do not change this value, the only option for the client is to skip some or all certificate validation, which is a bad idea.

Go to your `SOURCEDIR`, which depends on which installation method you chose.

## Copy the certificates

Here is is assumed that you installed from the source tarball.

```sh
cd ~/taskd-1.1.0/pki
./generate
...

cp client.cert.pem $TASKDDATA
cp client.key.pem $TASKDDATA
cp server.cert.pem $TASKDDATA
cp server.key.pem $TASKDDATA
cp server.crl.pem $TASKDDATA
cp ca.cert.pem $TASKDDATA
```

## Configuring TaskServer

```sh
taskd config --force client.cert $TASKDDATA/client.cert.pem
taskd config --force client.key $TASKDDATA/client.key.pem
askd config --force server.cert $TASKDDATA/server.cert.pem
taskd config --force server.key $TASKDDATA/server.key.pem
taskd config --force server.crl $TASKDDATA/server.crl.pem
taskd config --force ca.cert $TASKDDATA/ca.cert.pem
```
## Explanation

- There are three classes of `key/cert` here. There is the CA (`Certificate Authority`) cert, which has cert signing capabilities and is used to sign and verify the other certs.

- There are the server `key/certs`, which are used to authenticate the server and encrypt.

- Finally there are `client key/certs`, which are not what you might expect. These are for API access, and not for your Taskwarrior client. Those are created later.

## Other Configuration

Now we configure some basic details for the server. The chosen port is 53589. Note that we allow Taskwarrior clients specifically.

```sh
cd $TASKDDATA/..
taskd config --force log $PWD/taskd.log
taskd config --force pid.file $PWD/taskd.pid
taskd config --force server localhost:53589
```

## Verify

Note that we have chosen localhost:53589, but this choice has consequences. The name localhost is not network visible, which limits the server to only serving clients on the same machine. Use your full machine name for proper network addressability.

You can look at all the configuration settings:

```
taskd config
```

You can view all the supported settings with:

```sh
$ man taskdrc
```

## Control Server

You can now to launch the server:

```sh
taskdctl start # analogue stop to stop the server

This command launched the server as a daemon process. This command requires the TASKDDATA variable. Your server is now running, and ready for syncing.

Check that your server is running by looking in the taskd.log file, or running this:

```sh
ps -leaf | grep taskd
```

## Interactive on Non-daemon Server

A daemon server is typically how you would want to run Taskserver, but there may be times when you need to run the server attached to a terminal. These two commands are identical:

```sh
taskdctl start
taskd server --data $TASKDDATA --daemon
```

By omitting the --daemon option, the server remains attached to the terminal. Then to stop the server you can enter `Ctrl-C`

## Interactive Server

The interactive mode is really only useful for debugging, in conjunction with TLS debug mode, like this:

```sh
$ taskd config debug.tls 3
$ taskd server --data $TASKDDATA
...
```

With a debug.tls setting that is non-zero, you see lots of security-related diagnostic output

## Systemd unit file

You can start Taskserver using a systemd-unitfile (called taskd.service) like the one on the next slide (please add the contents of `$TASKDDATA` not the variable itself). Running the Taskserver as root is not recommended, please add an appropriate user and group to run the daemon with (`$TASKDUSER` and `$TASKDGROUP`).

## TASKD.SERVICE

```sh
[Unit]
Description=Secure server providing multi-user, multi-client access to Taskwarrior data
Requires=network.target
After=network.target
Documentation=http://taskwarrior.org/docs/#taskd

[Service]
ExecStart=/usr/local/bin/taskd server --data $TASKDDATA
Type=simple
User=$TASKDUSER
Group=$TASKDGROUP
WorkingDirectory=$TASKDDATA
PrivateTmp=true
InaccessibleDirectories=/home /root /boot /opt /mnt /media
ReadOnlyDirectories=/etc /usr

[Install]
WantedBy=multi-user.target
```

## CONTROL WITH SYSTEMD

Afterwards prepare systemd to recognise the file. The following commands need to be run as root-user.

```sh
cp taskd.service /etc/systemd/system
systemctl daemon-reload
systemctl start taskd.service
systemctl status taskd.service

# In case everything is running fine, enable the script to start Taskserver on every boot.

systemctl enable taskd.service
```

## Client Setup

## Add Organization

A user account must be created, along with a key, cert and ID, before syncing may occur.

Before creating a user account, you may need to create an organization. An organization consists of a group of zero or more users. You can get away with just one organization, and in this example, we will create just one, named 'Public'.

## CREATE ORGANISATION

You can create as many organizations as you wish (even one per user), and the purpose is simply to group users together. Future features will utilize this.

```sh
taskd add org Public
```

- Created organization 'Public'
- Now the organization 'Public' exists, we can add users to that organization.

## CREATE USER

Now we add a new user, named 'First Last' as an example. You can use any name you wish, and if it contains spaces, quote the name as shown.

```sh
$ taskd add user 'Public' 'First Last'
New user key: cf31f287-ee9e-43a8-843e-e8bbd5de4294
Created user 'First Last' for organization 'Public'
```

Note that you will get a different 'New user key' than is shown here, and you will need to retain it, to be used later for client configuration. Note that the key is just a unique id, because your name alone is not necessarily unique.

## CREATE CERTIFICATE AND KEY
Go to your `SOURCEDIR`, which depends on which installation method you chose. Here it is assumed that you installed from the source tarball.

```sh
cd ~/taskd-1.1.0/pki
./generate.client first_last
```

This will generate a new key and cert, named `first_last.cert.pem` and `first_last.key.pem`. It is not important that 'first\_last' was used here, just that it is something unique, and valid for use in a file name. It has no bearing on security.

## LET'S ENCRYPT

Certificates coming from Let's encrypt have not been successfully used by anyone.

Please remember that Let's encrypt only generates servers, but we need a client certificate as well.

A working scenario would be highly appreciated.

## TASKWARRIOR CONFIGURATION

You have now created a new user account on the server, created a new client cert and key, and have details that need to be transferred to the user, to set up a sync client.

## INFORMATION NEEDED

- `ca.cert.pem` is the certificate authority, and the only way to validate self-signed certs like ours.
- first_last.cert.pem is the client certificate.
- `first_last.key.pem` is the client key.
- The new user key (yours will be different): `cf31f287-ee9e-43a8-843e-e8bbd5de4294`
- The organization, Public.
- The full and proper user name, First Last.

## SERVER ADDRESS AND PORT

The server address and port, `host.domain:53589` is needed as well.

In the server configuration we used localhost as an example. With localhost your server can not be reached from outside the machine your are running it on.

Whatever you actually used there, should be used here

## CERTIFICATES

If you have configured Taskserver and created a user account (or better yet, someone created an account for you) then you now have details needed in the configuration of your Taskwarrior client.

Now we feed this information to Taskwarrior.

## COPY ALL OF THEM

Copy the Cert, Key and CA to your `~/.task` directory. The reason we are copying the CA cert is because this is a self-signed cert, and we need the CA to validate against. Alternately we could force Taskwarrior to trust all certs, but that is not recommended.

```sh
cp first_last.cert.pem ~/.task
cp first_last.key.pem ~/.task
cp ca.cert.pem ~/.task
```

## CONFIGURE TASKSERVER IN TASKWARRIOR

Now set the server info:

```sh
$ task config taskd.server -- host.domain:53589
```

Finally we provide the credentials, which combine the organization, account name and user key:

```sh
$ task config taskd.credentials -- Public/First Last/cf31f287-ee9e-43a8-843e-e8bbd5de4294
```

Your Taskwarrior is now ready to sync.

## TRUST LEVEL

It is possible to configure Taskwarrior's trust level, which determines how the server certificate is treated.

You could specify taskd.trust=ignore hostname to skip certificate hostname validation. This is a bad idea. You can also specify taskd.trust=allow all to perform no validation. This is a worse idea.

The default value is taskd.trust=strict which performs the most stringent verification, and is more secure.

## NOTE ON TASKWARRIOR 2.3.0

For Taskwarrior 2.3.0 you can specify taskd.trust=yes in order to skip certificate validation. This is a bad idea. The default is taskd.trust=no, which does not trust the server certificate, which is more secure.

## Sync 

You will do this differently depending on whether this is the first sync per device, or one of the many subsequent syncs.

The first time you sync is special - the client sends all your tasks to the server. This is something you should only do once on only one device.

```sh
task sync init
```

Please confirm that you wish to upload all your pending tasks to the Task Server (yes/no) yes
Syncing with host.domain:53589

Sync successful.  2 changes uploaded.

## NOTE

You should get an indication that tasks were uploaded, in this case 2 of them.

Please note that older Taskwarrior versions - before 2.5.1 - only sync the pending tasks and not all tasks.

## GENERAL SYNC

After the first time sync, you switch and just use this command:

```sh
$ task sync
Syncing with host.domain:53589

Sync successful.  No changes.
```

## FEEDBACK

This will give you feedback about what happened. Please note that it is perfectly safe to run this command as often as you wish. Syncing is safe and does not consume great system resources.

## CONNECTIVITY

Note that if your client is a mobile device, a sync command may consume some of your data usage. Act accordingly.

But it does require network connectivity, and if there is no connectivity you will be notified. It is not a problem if a sync fails because of this, because the next sync that works will catch up with all the changes, and do the right merging. Taskwarrior and Taskserver were designed to work together, and tolerate intermittent connectivity.

## SYNC REMINDER

After you modify data locally, Taskwarrior will start notifying you that you need to sync, after commands, like this:

```sh
$ task project:foo list
No matches.
There are local changes.  Sync required.
```

This is just a reminder to sync.

## SYNC!

Respond with a sync, and the reminder goes away:

```sh
$ task sync
Syncing with <server>:<port>

Sync successful.  1 changes uploaded.
```

If you do not respond with a sync, then local changes accumulate unseen by other clients. When you do eventually sync, the data will be properly propagated, so it is a question of whether you need current data on the server. It is perfectly fine to allow weeks to go by without a sync.

## Getting Help

*TROUBLESHOOTING GUIDE*

Please note there is a troubleshooting guide as well.

You can find the recent version here.

## GETTING HELP

As a last resort, ask for help. But please make sure you have carefully reviewed your setup, and gone through the checks above before asking. No one wants to lead you through the steps above to discover that you didn't.

We'll ask you to provide the diagnostics output for both Taskwarrior and Taskserver, then we're going to go through the steps above, because this is our checklist also.

## GETTING HELP (1)

There are several ways of getting help:

- We have an FAQ covering a lot of questions.
- Email us at support@taskwarrior.org, then wait patiently for a volunteer to respond.
- Join us IRC in the #taskwarrior channel on Freenode.net, and get a quick response from the community, where, as you have anticipated, we will walk you through the checklist above.

## GETTING HELP (2)

There are several ways of getting help:

- Even though Twitter is no means of support, you can get in touch with @taskwarrior.
- We have a User Mailinglist which you can join anytime to discuss about Taskwarrior and techniques.
- The Developer Mailinglist is focussing on a more technical oriented audience.
