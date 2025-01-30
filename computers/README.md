# Computing
### Alex D. Hill
#### 25 SEP 2024

#### Terminals and Bash

The terminal is a text-command based interface to your computer. On the terminal you run commands for navigation, file manipulation, and program execution. When in your terminal you are executing commands from a specific directory. When you first log into the computer, this defaults to a `home directory`. On a mac this will be `/Users/username/` and on a linux system it is usually be `/home/username/`. The directory where your terminal is currently operating is your *working directory*.

#### File Paths

The `path` to a file is the location it is stored within the computer. It tells you all of the folders (we call them *directories* from here on out) that you need to navigate through to get to the file. On a macOC or linux system, the path is separated by `/` characters.

For example, the path to a file called `my_file.txt` in a directory called `my_folder` in your home directory would be `/home/username/my_folder/my_file.txt`.

This is an example of an absolute path, which ignores your working directory. Absolute paths for a specific file will not change unless the file is movesd. Any paths which start with `/` will be run as absolute paths.

A `relative path` means that you are adding the specified path onto your current working directory. If you are in directory `/home/username/` and you want to access the file `my_file.txt` inside `my_folder`, you could use the relative path `my_folder/myfile.txt`. Because the path does not start with a `/`, we add it onto the working directory, giving us `/home/username/ + my_folder/my_file.txt = /home/username/my_folder/my_file.txt`. Because relative paths are based on your working directory, the relative path to a file can change depending on where you are running the command from.

Here is an example to clarify:

You are running your terminal from the home directory `/home/username`. In your home folder there is a directory called `my_folder` and in that directory there is a file called `my_file.txt`.

The absolute path to that file is `/home/username/my_folder/my_file.txt`.
The relative path is `my_folder/my_file.txt`.

However, you don't have to launch all commands from the home directory, in order to work inside of a folder you can `cd` into it. 

##### Change Directory (*cd*)
```
cd <path/to/dir>
```

Using `cd` will change your working directory to the specified path. Lets say I am in my home folder, which contains a new directory called "my_project", which is where I am doing all my work. I can change into that directory with the following command: `cd my_project`. You could equivalently use `cd /home/username/my_project` to get to the same place.

There are special directories accessible from cd:
 - `.` is the current directory. Running `cd .` will keep you in your current directory.
 - `..` is the parent directory. Running `cd ..` will move you up one directory in the file tree. Using the "my_project" example, if you run `cd ..` from inside the "my_project" directory, you will be moved back into your home directory.
 - `~` is your home directory. Running `cd ~` will move you back to your home directory from anywhere on the computer.

With all the moving around in the computer it is important to keep track of what your working directory is.

##### Print Working Directory (*pwd*)

You can see what directory you are in by using the `pwd` command. This command stands for *print working directory*.

##### List Files (*ls*)

One of the most frequent commands you will use is `ls`, which lists all the files and directories a directory. 

Running `ls` will list all files in the working directory, however you can also list files in a different directory by specifying the path to that directory. For example, `ls /home/username/my_folder` will list all files in the directory `my_folder`.

`ls` can also use the special directories `.`, `..`, and `~` that `cd` can use.

This command also has `flags` or arguments (`args`) that can be used to modify the output.

For example, `ls -l` will list the files in the directory in a `l`ong format, which includes information like the file permissions, the number of links, the owner, the group, the size, the date, and the name of the file.

`ls -a` will list all files in the directory, including hidden files. Hidden files are files that start with a `.` character.

##### Make Directory (*mkdir*)

If you want to create a new directory to store more directories and files, you use `mkdir`. 

```
mkdir <new_dir_name>
```

args:
 - `-p` This flag will create all directories necessary to create the new directory. For example if you run `mkdir new_folder/second_folder` without creating the directory `new_folder` first, you will get an error. However, if you run `mkdir -p new_folder/second_folder` the `new_folder` directory will be created and then the `second_folder` directory will be created inside of it.

##### Remove Directory (*rmdir*)

Sometimes you need to remove directories that you no longer need. You can use `rmdir` to remove a directory.

```
rmdir <dir_name>
```

args:
 - `-f` This flag will force the deletion of the directory, **even if it is not empty**. By default `rmdir` will not delete a directory that has files in it.

##### Copy Files (*cp*)

You can copy files from one directory to another using the `cp` command.

```
cp <source_file> <destination_file>
```

args:
 - `-r` By default `cp` only works to copy individual files, not directories. This flag will copy directories and their contents.

##### Move Files (*mv*)

You can move files from one directory to another using the `mv` command. Moving files or directories is also the same as renaming them.

```
mv <source_file> <destination_file>
```

args:
 - `-r` By default `mv` only works to move individual files, not directories. This flag will move directories and their contents.
 - `-f` This flag will force the move, even if the destination file already exists. This will delete any existing files or directories with the same name as the destination file.

##### Create Files (*touch*)

You can create a new file using the `touch` command.

```
touch <new_file>
```

##### Remove Files (*rm*)

You can remove files using the `rm` command.

```
rm <file>
```

args:
 - `-r` By default `rm` only works to remove individual files, not directories. This flag will remove directories and their contents.
 - `-f` This flag will force the removal of the file, even if it is write-protected.

#### BASH

Everything you run in the terminal is technically using a programming language called `bash`. The operations above are all special programs that have been written to be run by bash. These programs are all stored in special `bin` directories on your computer.

All the bins containing programs are saved in a bash variable called `PATH`. You can see the contents of the `PATH` variable by running `echo $PATH`.

`echo` is a program that will just print its arguments to the terminal, and the `$` is a special character used to access variables in bash.

Running `echo` with `$PATH` will then print the contents of the `PATH` variable. Running `echo PATH` will just print the word `PATH`.

#### Environments

It may seem reasonable to just install every program you could ever want to use on your computer, but this introduces problems. What if you want to install the newest version of program `X`, but program `Y` requires you to use a super old version of `X`??

We use `environments` to manage the programs we have installed, in separate spaces.
We can create one environment for program `X` and another for program `Y`, and then switch between them as needed.

##### MAMBA/CONDA

A very common environment manager is `conda`, which we use to install packages, tools, libraries and anything we need into neatly named environments. 

`mamba` is just a faster version of `conda`, so we use it instead.

##### Create Environment (`conda create -n <env_name>`)

This command will create a new environment with the name `<env_name>`. Once an environment is created, you can begin installing packages into it.

#### Install Package (`mamba install <package_name>`)

This command will install the package with the name `<package_name>` into the current environment. You can also specify a version of the package to install by adding `=<version>` to the end of the package name.

args:
  - `-n <env_name>` This flag will install the package into the environment with the name `<env_name>`. If you do not explicitly specify an environment, the package will be installed into the current environment.
  - `-c <channel>` Conda uses special package repositories called "channels" to save all their packages. Sometimes a package you want to install will not be in the default channel, so you can specify different channels to install from on top of the default channel. You can use the `-c` flag multiple times to specify multiple channels.

##### Activate Environment (`conda activate <env_name>`)

This command will activate the environment with the name `<env_name>`. Once an environment is activated, any packages you installed into that environment are accessible as bash commands!

##### Deactivate Environment (`conda deactivate`)

This command will deactivate the current environment. Once an environment is deactivated, you will no longer have access to the packages you installed into that environment.

##### List Environments (`conda env list`)

This command will list all the environments you have created on your computer.

#### Notes
  - Most programs have their own unique flags and arguments, but the majority ship with a flag `-h` or `--help`. This flag will print out the help documentation for the program, which will list all the flags and arguments you can use with the program, as well as proper way to call the command.