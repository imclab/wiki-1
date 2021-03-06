# SSH

SSH, or secure shell, is a way for you to log in and use the terminal of a computer over the internet. Not only is it useful for working remotely, it's required for working with the PR2, since the PR2 doesn't have a keyboard or a monitor.

## How to SSH into a computer

If you are on Linux or Mac OS X, open a terminal. If you are on Windows, download [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) or another SSH client of your choice.

Let's say you want to log into the lab machine roomba with the username husky.

If you're using a terminal, type:
```bash
ssh husky@roomba.cs.washington.edu
```

If you're using Windows or another SSH client, then you will need to specify the username (e.g., husky) and the hostname: (e.g., roomba.cs.washington.edu).

If you are on the CSE network (i.e., on a lab machine or on CSE-Local), you can omit the cs.washington.edu:
```bash
ssh husky@roomba
```

If the username of the computer you're on is the same as the username you'd like to log in with, you can omit the username as well:
```bash
ssh roomba
```

You now have a shell, as if you were physically sitting at that computer.

You can also log into a computer by its IP address, if it doesn't have a hostname that can be looked up:
```bash
ssh husky@128.208.5.151
```

## SSH keys

You can SSH into a computer without having to type in your password, by generating a public/private key pair. The security of SSH is based on [public-key cryptography](http://en.wikipedia.org/wiki/Public-key_cryptography). In this setting, you have a public key, which you share with the world, and a private key, which you keep to yourself.

### Creating SSH keys
To generate an SSH key, use the ssh-keygen program:
```
ssh-keygen -C "husky@cs.washington.edu"
```
What you pass to the -C option doesn't matter, as long as it helps explain who owns the key.

When it asks you to enter a location for the key, press enter to accept the default. Type in a password when it asks for one.

The keys should appear in ~/.ssh/. Your public key is id_rsa.pub, and your private key is id_rsa. You can share id_rsa.pub far and wide, but do not share id_rsa.

### Using SSH keys to log in without a password

Using your newly created SSH keys, you can log into a computer (say, roomba) without a password. Assuming you have an account on roomba, you can type:
```bash
ssh-copy-id husky@roomba
```

After this, you should be able to log in to roomba without a password.

### How to make your computer SSH-able

If you can't already SSH into your computer, you may need to install OpenSSH:
```bash
sudo apt-get install openssh-server
```

After that, you should be able to log in, using your IP address as the hostname.

If you're on the CSE network and don't already have a fancy hostname (e.g., you brought a personal laptop to school to use as a permanent work computer), do the following steps:

1. [Register your computer](https://norfolk.cs.washington.edu/htbin-post/lab/RegisterMAC.cgi) with the desired hostname
2. `sudo vim /etc/hostname` and replace whatever's there with your chosen hostname
3. `sudo vim /etc/hosts` and add an entry mapping 127.0.1.1 to your chosen hostname

## Github authentication
You can use SSH keys to avoid typing in your password when working with git repositories. For Github, go the [SSH Keys in the settings page](https://github.com/settings/ssh) and click "Add SSH key". Copy the contents of ~/.ssh/id_rsa.pub into the "Key" field.

## Copy files from another computer
You can use the secure copy command, scp, to copy files between computers. If you already have SSH keys set up between the two computers, it won't ask you for a password. For example, the following command will copy the .bashrc file from roomba to this computer:
```bash
scp husky@roomba:~/.bashrc .bashrc
```

Similarly, you can copy a file from your computer to a remote computer. Also, you can copy entire directories using the -r option:
```bash
scp -r ~/catkin_ws husky@c1.cs.washington.edu:~/catkin_ws
```

## Remote folder
You can mount a folder from a remote machine to your own computer using sshfs:
```bash
sudo apt-get install sshfs
mkdir c1_catkin_ws
ls c1_catkin_ws # It's empty
sshfs husky@c1:/home/husky/catkin_ws c1_catkin_ws
ls c1_catkin_ws # Stuff that was in catkin_ws on c1 is now on your computer too!
```

## SSH agent
You may notice that when you SSH into a machine, then you might have to type a password in to do operations that don't normally require a password from that machine. This is because there is a necessary program called ssh-agent which is started in most graphical sessions, but not when you log in remotely via SSH.

You can solve this by manually starting ssh-agent:
```bash
ssh-agent bash
ssh-add
```

After you type in your password, won't need to type in your password again as long as you are logged in.

## SSH tunneling
You can use SSH to "tunnel" from a port on your local computer to a port on a remote computer. This is useful for when you are working on a remote machine, and it launches something that runs on localhost.

For example, let's say you are SSH'ed into a lab computer, walle, and you're working on a web server. When you run the web server to test it, it runs on localhost:8000. If you can't reach the site by going to walle.cs.washington.edu:8000 in your browser, then you can tunnel by opening an SSH connection like so:
```bash
ssh husky@walle.cs.washington.edu -L 2000:walle.cs.washington.edu:8000
```

Now you should be able to access your website by going to localhost:2000 on your computer, which is roughly equivalent to visiting localhost:8000 on walle.

## X11 forwarding
If you are using a Linux computer, you are most likely using the X11 Window System. If you SSH into another computer using X11, then you can actually run graphical applications on a remote machine, and see the GUI appear on your computer. For example, this can be used to run Matlab on a powerful remote machine, while forwarding the GUI to your laptop. This could also be useful for opening up GUI applications on robots which don't have displays (e.g., the PR2).

To enable X11 forwarding, SSH with the `-X` option.:
```bash
ssh -X walle # Assumes walle is a Linux machine with X11.
firefox # Opens Firefox on walle, but shows the GUI on your machine, assuming you are also on Linux.
```
