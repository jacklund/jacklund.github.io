---
date: 2013-05-06 20:46  
title: Getting Jenkins on Windows Working with Git  
tags: jenkins, windows, git  
layout: post
comments: true
---
We're transitioning, as a company, from Subversion to Git, which is a Good Thing, but has its complications. One of them was getting [Jenkins](http://jenkins-ci.org/), which we use for [continuous integration](http://en.wikipedia.org/wiki/Continuous_integration), to work with our Git repositories.

I had started out by thinking that I would have to create a user in our repository provider (we're using [BitBucket](http://bitbucket.org)) which would have read permisisons to our repositories, a "jenkins" user. Turns out its not the case. All you need to do for read-only access is to generate SSH keys that you register with the repository, and which Jenkins uses to clone the repository.

On Linux, this would be trivial. On Windows, of course, very little is trivial.

So, following [this guide](http://computercamp.cdwilson.us/jenkins-git-clone-via-ssh-on-windows-7-x64)(with some modifications) I managed it this way:

1. Install [Git](http://git-scm.com/downloads)
1. Generate the new SSH keys on my local machine using ``ssh-keygen``. Make sure to specify a file **other** than the default (unless you like the idea of overwriting any existing keys you might have).
1. Install the public key in the repository. In BitBucket, you do this under the account settings for the user or group who owns the repositories. Do it at the user/group level, not at the repository level (unless you want separate keys for each repository). Just cut and paste the contents of the public key to the web page, and click "Add"
1. Install [PsTools](http://technet.microsoft.com/en-us/sysinternals/bb896649.aspx) (_Note_: the link given in the guide mentioned above didn't work for me). Extract the zip file wherever you want (I put mine in the root dir)
1. Run the following in a command shell _running as administrator_: ``\PsTools\PsExec.exe -i -s cmd.exe`` (_Note:_ the guide says use a normal shell, but that will fail the first time, because it tries to instal something that needs Admin privileges). To do this, find the command shell icon in the menu, right-click, and select _Run as Administrator..._.
1. Do ``echo %USERPROFILE%`` in the shell to find out where the system user (and, hence, Jenkins) runs from as its home directory. Mine turned out to be ``C:\Windows\system32\config\systemprofile``. YMMV.
1. Make a folder named ``.ssh`` in that home directory
1. Copy the keys (private and public) to the Windows machine in the ``.ssh`` folder
1. Run ``"C:\Program Files(x86)\Git\bin\ssh.exe" -T git@bitbucket.org`` (_Note:_ the location of git on your install may be different than mine). When you get the usual "unknown host" warning, type "yes" to get the server key in your known_hosts file
1. Finally, test git by doing a ``git ls-remote git@bitbucket.org:repo.git HEAD``
