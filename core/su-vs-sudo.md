su-vs-sudo
==========



With su, you become another user — root by default, but potentially another user. If you say su -, your environment gets replaced with that user's login environment as well, so that what you see is indistinguishable from logging in as that user. There is no way the system can tell what you do while su'd to another user from actions by that user when they log in.

Things are very different with sudo:

* Commands you run through sudo execute as the target user — root by default, but changeable with -u — but it logs the commands you run through it, tagging them with your username so blame can be assigned afterward. :)

 * sudo is very flexible. You can limit the commands a given user or group of users are allowed to run, for example. With su, it's all or nothing.

 * This feature is typically used to define roles. For instance, you could define a "backups" group allowed to run dump and tar, each of which needs root access to properly back up the system disk.

 * I mention this here because it means you can give someone sudo privileges without giving them sudo -s or sudo bash abilities. They have only the permissions they need to do their job, whereas with su they have run of the entire system. You have to be careful with this, though: if you give someone the ability to say sudo vi, for example, they can shell out of vi and have effectively the same power as with sudo -s.

 * Because it takes the sudoer's password instead of the root password, sudo isolates permission between multiple sudoers.

    This solves an administrative problem with su, which is that when the root password changes, all those who had to know it to use su had to be told. sudo allows the sudoers' passwords to change independently. In fact, it is common to password-lock the root user's account on a system with sudo to force all sysadmin tasks to be done via sudo. In a large organization with many trusted sudoers, this means when one of the sysadmins leaves, you don't have to change the root password and distribute it to those admins who remain.

The main difference between sudo bash and sudo -s is that -s is shorter and lets you pass commands to execute in your user's default shell in a couple of ways:

   You can say sudo -s some-command which runs some-command under your shell. It's basically shorthand for sudo $SHELL -c some-command.

   You can instead pass the commands to the shell's standard input, like sudo -s < my-shell-script. You could use this with a heredoc to send several commands to a single sudo call, avoiding the need to type sudo repeatedly.

Both of those behaviors are optional. Far more commonly, you give -s alone, so it just runs your user's shell interactively. In that mode, it differs from sudo bash in that it might run a different shell than bash, since it looks first in the SHELL environment variable, and then if that is unset, at your user's login shell setting, typically in /etc/passwd.

The shell run by sudo -s inherits your current user environment. If what you actually want is a clean environment, like you get just after login, what you want instead is sudo -i, a relatively recent addition to sudo. Roughly speaking, sudo -i is to sudo -s as su - is to su: it resets all but a few key environment variables and sends you back to your user's home directory. If you don't also give it commands to run under that shell via standard input or sudo -i some-command, it will run that shell as an interactive login shell, so your user's shell startup scripts (e.g. .bash_profile) get run again.

All of this makes sudo -i considerably more secure than sudo -s. Why? Because if someone can modify your environment before sudo -s, they could cause unintended commands to be executed. The most obvious case is modifying SHELL, but it can also happen less directly, such as via PAGER if you say man foo while under sudo -s.

You might say, "If they can modify PAGER, they can modify PATH, and then they can just substitute an evil sudo program," but someone sufficiently paranoid can say /usr/bin/sudo /bin/bash to avoid that trap. You're probably not so paranoid that you also avoid the traps in all the other susceptible environment variables, though. Did you also remember to check EDITOR, for example, before running any VCS command? Thus sudo -i.

Because sudo -i also changes your working directory to your user's home directory, you might still want to use sudo -s for those situations where you know you want to remain in the same directory you were cd'd into when you ran sudo. It's still safer to sudo -i and cd back to where you were, though.

(c) <https://unix.stackexchange.com/questions/35338/su-vs-sudo-s-vs-sudo-i-vs-sudo-bash>