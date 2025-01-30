# UCSC Remote Computing
### Alex D. Hill
#### 25 SEP 2024

#### Setting up with Prism

First, you must complete an online course called [2022 Information Security, Insider Threats, Privacy Awareness, Records Management and Emergency Preparedness Refresher](https://irtsectraining.nih.gov/public.aspx) and save your completion certificate.

You then need to sign two documents, the [UCSC VPN Policy](https://giwiki.gi.ucsc.edu/images/c/cb/GI_VPN_Policy.pdf) and the [NIH Genomic Data Sharing Policy](https://giwiki.gi.ucsc.edu/images/1/1b/NIH_GDS_Policy.pdf).

Finally fill out the form found [here](https://app.smartsheet.com/b/form/a76dbd90ba0240ab9ea9d39b390586ce) to request access to the UCSC VPN.

They will schedule a meeting with you once your request is approved, but before the meeting you must install an App called [TunnelBlick](https://tunnelblick.net/downloads.html), which is the schools VPN of choice.

#### Connecting to the VPN

Once the GI managers have your VPN and server credentials saved, you should have access to the servers.

To connect to the VPN, open TunnelBlick and click the `Connect` button.

Then open the terminal and type `ssh username@mustard`. `ssh` stands for secure shell and is a command that allows you to connect to a remote computer. `username` is your UCSC username and `mustard` is the name of the server you are connecting to. Valid UCSC servers are `mustard`, `emerald`, and `razzmatazz`.

You will be prompted for your password, which is the prism password you set up in your meeting, bet sure to save it.

#### Saving your password

I log onto the servers everyday about 10 times, so I cannot be bothered to type my password every time. We use a special encrypted file called an ""ssh key" to store our login credentials for the servers.

To create a new ssh key, open your terminal and type in exactly `ssh-keygen -t rsa -b 4096 -f ~/.ssh/prism`

You will be prompted to create a passphrase for the key, but we don't want to type in passwords, so hit enter two times.

Next we want to make sure that we always use this passkey for mustard, so type exactly `echo "Host mustard\n    IdentityFile ~/.ssh/prism\n    ForwardAgent yes" >> ~/.ssh/config`

Now that we have an encrypted key and we are ready to use it for mustard, we need to share it to the servers. To do this, type `ssh-copy-id -i ~/.ssh/prism username@mustard`. You will be prompted for your prism password for the last time ever.

#### Configuring the server

Once on the server, we need to edit a file called `~/.bashrc` to make sure that our environment manager and other command line operations are all set up correctly. `.bashrc` is a special bash script stored in your home directory that is run every time you open a new terminal. 

##### Adding mamba configuration

To add the mamba configuration to your `.bashrc` file, run
```
echo '__conda_setup="$(\'/private/groups/kimlab/environments/bin/conda\' \'shell.bash\' \'hook\' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/private/groups/kimlab/environments/etc/profile.d/conda.sh" ]; then
        . "/private/groups/kimlab/environments/etc/profile.d/conda.sh"
    else
        export PATH="/private/groups/kimlab/environments/bin:$PATH"
    fi
fi
unset __conda_setup

if [ -f "/private/groups/kimlab/environments/etc/profile.d/mamba.sh" ]; then
    . "/private/groups/kimlab/environments/etc/profile.d/mamba.sh"
fi
mamba activate base'
```

This will give you access to the lab's installation of mamba, so we can share environments for reproducibility.


##### Screens

When using a remote computer the terminal that you activate to log on disappears when you leave. This means if you are actively running some tool or script, it will be killed if your computer falls asleep or the connection is lost. To avoid this, we use a program called `screen` which creates saved terminal sessions that you can enter and exit freely without them terminating.

To launch a new screen we use the command `screen -S <name>` which will create a new terminal session called '\<name>'.

When you are in a screen session, there are certain commands you can use to navigate:
  - Ctrl-A then d will *d*etach you from the saved terminal and dump you back into the main terminal
  - Ctrl-A then esc will allow you to scroll within the terminal session

If you have many saved terminal open, you can view all their names with the command `screen -ls` to *l*i*s*t the screen sessions.

When you see the screen you want to enter, you can reconnect back to your saved terminal with the command `screen -R <name>` to *r*eattach to the screen.

Lastly, you should remember that saved terminals **DO NOT** die when you close the remote connection, meaning you have to kill them yourself. One way is to reattach to the saved terminal you want to kill and running `exit`. The other is to run `screen -XS <name> quit` from the primary terminal session to kill the saved terminal with name '\<name>'.

##### Directories

Our labs directory is `/private/groups/kimlab` and we encourage individuals to make their own directory within the kimlab dir. Inside your folder your work can be organized however you feel is most efficient for you, but we would also like you to keep README files scattered to help others read through and parse your work directories.

All of the labs sequencing data should be stored in `/private/groups/kimlab/data` and reflected in the directory's README file. Anyone who wants data in another place should **SYMLINK** the data to the new location.

