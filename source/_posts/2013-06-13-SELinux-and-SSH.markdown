---
date: 2013-06-13 13:30  
title: SELinux and SSH
tags: ssh, selinux
layout: post
comments: true
---
I needed to set up Jenkins so that it could run an agent on another Linux box. To do this the default way, you need to allow Jenkins to access the remote box via SSH. So, I set up the Jenkins user and the SSH keys on the remote machine, but trying to do an ssh from the Jenkins user on one machine to the other still didn't work. After doing ``sudo journalctl -f`` on the remote machine, I saw the following:

    Jun 13 12:41:50 m2m-linux setroubleshoot[701]: SELinux is preventing /usr/sbin/sshd from read access on the file authorized_keys. For complete SELinux messages. run sealert -l ca7b7aea-64cf-46e5-8bfa-6650ad23f55b

Running the suggested ``sealert -l ca7b7aea-64cf-46e5-8bfa-6650ad23f55b`` gave me the following (edited for brevity):

    SELinux is preventing /usr/sbin/sshd from read access on the file authorized_keys.

    *****  Plugin catchall_labels (83.8 confidence) suggests  ****

    If you want to allow sshd to have read access on the authorized_keys file
    Then you need to change the label on authorized_keys
    Do
    # semanage fcontext -a -t FILE_TYPE 'authorized_keys'
    where FILE_TYPE is one of the following: NetworkManager_etc_rw_t, NetworkManager_etc_t,...
    Then execute:
    restorecon -v 'authorized_keys'


    *****  Plugin catchall (17.1 confidence) suggests  *************

    If you believe that sshd should be allowed read access on the authorized_keys file by default.
    Then you should report this as a bug.
    You can generate a local policy module to allow this access.
    Do
    allow this access for now by executing:
    # grep sshd /var/log/audit/audit.log | audit2allow -M mypol
    # semodule -i mypol.pp

Not really helpful, so I dug around the web and found [this post on ServerFault](http://serverfault.com/questions/50573/selinux-preventing-passwordless-ssh-login). It turns out that the problem is that I created the Jenkins user's home directory by hand (I had to, it was ``/var/jenkins``, which ``adduser`` wouldn't create), so SELinux didn't know it was a home directory.

To fix this, I added the following lines to the end of ``/etc/selinux/targeted/contexts/files/file_contexts.homedirs``:

    /var/jenkins/.+ unconfined_u:object_r:user_home_t:s0
    /var/jenkins/\.ssh(/.*)?      system_u:object_r:ssh_home_t:s0

and then did ``sudo restorecon -R -v /var/jenkins``, and that fixed the problem.
