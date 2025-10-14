

## Linux Fundamentals Lab: The Core Concepts

Welcome to your first Linux administration lab\! The goal of this exercise is to familiarize you with the essential concepts that govern how a Linux system operates. We'll learn by doing, so open up a terminal on your VM and get ready to type some commands. 🧑‍💻

### 1\. The Boot Process and `systemd`

Every time your Linux machine starts, it goes through a sequence of steps to become usable. While it's complex, we can simplify it:

1.  **BIOS/UEFI:** Your computer's firmware wakes up the hardware.
2.  **Bootloader (GRUB):** It loads the Linux **Kernel** into memory.
3.  **Kernel:** The core of the OS. It starts the very first process.
4.  **`systemd` (PID 1):** This first process, `systemd`, is the "init" (initialization) system. Its job is to start all the other services and processes needed for the system to run, like networking, login screens, and applications.

`systemd` is the modern manager for services in Linux. Let's see it in action.

**Hands-On:**

  * Apache is a popular web server. Let's check its status. (We'll use `caddy` for Debian/Ubuntu, use `httpd` for CentOS/RHEL).

    ```bash
    # Check if the service is running
    systemctl status caddy

    # Start the service (you'll need sudo)
    sudo systemctl start caddy

    # Stop the service
    sudo systemctl stop caddy

    # Enable the service to start automatically on boot
    sudo systemctl enable caddy

    # Disable it from starting on boot
    sudo systemctl disable caddy

    # Look at the logs for a specific service
    journalctl -u caddy
    ```

-----

### 2\. User and Group Management

Linux is a multi-user system. Everything is owned by a **user** and a **group**. This is how Linux controls who can access what.

  * `/etc/passwd`: A public file that lists all user accounts.
  * `/etc/shadow`: A secure file that contains the encrypted passwords for users. Only `root` can read it\!

**Hands-On:**

  * Let's explore your own identity and create a new user.

    ```bash
    # Find out who you are currently logged in as
    whoami

    # Get detailed information about your user and group membership
    id

    # List only the groups you are in
    groups

    # Look at the contents of the passwd file (it's safe!)
    cat /etc/passwd

    # Try to look at the shadow file (it will fail!)
    cat /etc/shadow

    # Now try with sudo (it will work)
    sudo cat /etc/shadow

    # Let's create a new user named 'alex'
    sudo useradd -m alex

    # Set a password for alex (it will prompt you to enter one)
    sudo passwd alex
    ```

-----

### 3\. File Permissions (The `rwx` System)

Every file and directory in Linux has permissions for three types of identities: the **User** (owner), the **Group**, and **Other** (everyone else).

The permissions are:

  * **r (Read):** View the contents of a file or list contents of a directory.
  * **w (Write):** Change a file or create/delete files in a directory.
  * **x (Execute):** Run a file (if it's a script/program) or enter a directory (`cd`).

**Hands-On:**

  * Let's create a file and play with its permissions.

    ```bash
    # Create a test directory and enter it
    mkdir mytest && cd mytest

    # Create an empty file
    touch my_file.txt

    # List the file with its permissions
    ls -l my_file.txt
    # Output looks like: -rw-rw-r-- 1 student student 0 Oct 14 16:30 my_file.txt
    #  ^  --- --- ---
    #  |   U   G   O  (User, Group, Other)

    # Remove write permission for the Group
    chmod g-w my_file.txt
    ls -l my_file.txt # See the change?

    # Add execute permission for everyone (User, Group, and Other)
    chmod a+x my_file.txt
    ls -l my_file.txt # See the 'x's appear?

    # You can also use numbers (r=4, w=2, x=1)
    # To set rwx for User, and r-- for Group/Other (744)
    chmod 744 my_file.txt
    ls -l my_file.txt

    # Change the owner of the file to the 'alex' user we created
    sudo chown alex my_file.txt
    ls -l my_file.txt # Notice the owner has changed
    ```

-----

### 4\. Special Permissions (SUID, SGID, Sticky Bit)

Beyond `rwx`, there are three special permissions for advanced cases.

  * **SetUID (SUID):** When set on an executable file, the file runs with the **permissions of the file owner**, not the user who ran it. This is used for tasks that need temporary elevated privileges. The `passwd` command is a great example—it lets you change your own password in the protected `/etc/shadow` file by running temporarily as `root`.
  * **SetGID (SGID):** Similar to SUID, but the file runs with the permissions of the file's **group**. When used on a *directory*, any new file created inside it will inherit the directory's group. This is great for collaborative folders.
  * **Sticky Bit:** When set on a directory, it allows anyone to create files in it, but only the file's owner (or root) can delete that file. The `/tmp` directory is the classic example.

**Hands-On:**

```bash
# SUID: Notice the 's' in the user execute permission for the passwd command
ls -l /usr/bin/passwd

# SGID: Let's create a collaborative directory
sudo mkdir /shared_folder
sudo groupadd team_a
sudo chown root:team_a /shared_folder
# Now set the SGID bit
sudo chmod g+s /shared_folder

# Check the permissions - notice the 's' for the group
ls -ld /shared_folder

# Sticky Bit: Check the permissions on /tmp - notice the 't' at the end
ls -ld /tmp
```

-----

### 5\. Processes and Signals

A **process** is any program that is currently running. Each process has a unique Process ID (**PID**). You can send messages, called **signals**, to processes to tell them to do things like stop, reload, or terminate.

Key Signals:

  * `SIGTERM` (15): The polite request. "Please shut down cleanly." This is the default for the `kill` command.
  * `SIGKILL` (9): The forceful command. "Shut down now\!" The process cannot ignore this.
  * `SIGHUP` (1): The "hang up" signal. Often used to tell a service to reload its configuration file.

**Hands-On:**

  * Let's start a simple process and manage it.

    ```bash
    # This command will sleep for 300 seconds. The '&' runs it in the background.
    sleep 300 &
    # The shell will print its PID. Let's say it's 1234.

    # See all your running processes
    ps aux

    # Filter to find just our sleep process
    ps aux | grep sleep

    # Find the PID using pgrep
    pgrep sleep

    # Now, let's stop it politely using its PID (replace 1234 with the actual PID)
    kill 1234

    # The process should be gone. Let's start another one.
    sleep 300 &

    # This time, let's use the process name to kill it (more convenient!)
    pkill sleep

    # killall is similar but can be more aggressive
    # killall sleep
    ```

-----

**Lab Complete\!** You've now experimented with the basics of Linux services, users, permissions, and processes. Keep these concepts in mind as you move on to the next challenge, where you'll need them to fix a broken system\! 🎉
