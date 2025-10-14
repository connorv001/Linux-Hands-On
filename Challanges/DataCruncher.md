

## The DataCruncher Rescue Mission: A Field Guide

Welcome, System Administrator. The `datacruncher` service is down, and it's your job to fix it. This guide will help you navigate the broken system, ask the right questions, and use the right tools to identify and solve each problem.

Your primary tool for a service-related issue is `systemctl` and for logs is `journalctl`. Use them frequently.

### Phase 1: The Service Won't Start

Your first objective is to get the service to attempt to start without immediately erroring out.

**Your Investigation Checklist:**

1.  **Check the Status:** What is the very first command you should run to check the state of any service?
    
    Bash
    
    ```
    sudo systemctl status datacruncher.service
    
    ```
    
    -   Pay close attention to keywords like `loaded`, `active`, `inactive`, `dead`, or `failed`.
        
    -   Read the last few lines of the output. They often contain the direct error message.
        
2.  **Read the Logs:** The status command gives you a summary. The journal provides the details.
    
    Bash
    
    ```
    # Look at the specific logs for this service
    journalctl -u datacruncher.service
    
    # Tip: Use the -f flag to follow the logs in real-time as you try to start the service
    journalctl -fu datacruncher.service
    
    ```
    
    -   Look for errors like `command not found`, `Permission denied`, or `status=203/EXEC`. These are clues.
        
3.  **Inspect the Blueprint (`.service` file):** A `systemd` service file tells the system _what_ to run and _how_ to run it. If the logs point to an execution error, your next step is to verify the service file itself.
    
    -   Service files are located in `/etc/systemd/system/`. View the contents of `datacruncher.service`.
        
    -   Find the `ExecStart=` line. Does the file path it points to _actually exist_? Use `ls -l` to verify the path.
        
4.  **Check Executability:** In Linux, a file needs special permission to be run as a program.
    
    -   Once you've confirmed the path in `ExecStart=` is correct, check the permissions on that script file with `ls -l /path/to/the/script`.
        
    -   Does the owner (`cruncher_user`) have the **execute (`x`)** permission? If not, the system is correctly denying the request to run it. You'll need to add this permission.
        

**Goal for this Phase:** Successfully run `sudo systemctl start datacruncher.service` without it failing immediately. The service might still stop after a few seconds, and that's okay. We'll fix that next.

----------

### Phase 2: The Service Starts, but Immediately Fails

If the service now starts but then quickly enters a failed state, it means the script is running but hitting a problem. This is progress! Now we debug the application's own errors.

**Your Investigation Checklist:**

1.  **Check the Logs (Again):** The error messages in `journalctl -u datacruncher.service` will be different now. The script itself might be printing an error message like "cannot create file" or "Permission denied."
    
2.  **Think Like the User:** The service file specifies that the script runs as the user `cruncher_user`. This user has very limited permissions. The script tries to write a log file to `/var/log/datacruncher/`.
    
    -   Who owns that directory? Use `ls -ld /var/log/datacruncher/` to find out.
        
    -   Does the `cruncher_user` have **write (`w`)** permission for that directory? If the directory is owned by `root`, the answer is no.
        
    -   How do you change the ownership of a file or directory to the correct user and group?
        

**Goal for this Phase:** The service starts and _stays_ running. You should see `active (running)` in the `systemctl status` output, and you should see new lines appearing in `/var/log/datacruncher/app.log`.

----------

### Phase 3: The Unstoppable Process

The service is running, but now Alex reports he can't stop it. A service that can't be stopped is a rogue process.

**Your Investigation Checklist:**

1.  **Attempt a Graceful Stop:** Try to stop the service the normal way.
    
    Bash
    
    ```
    sudo systemctl stop datacruncher.service
    
    ```
    
    -   Does the command hang and eventually time out? This is a big clue.
        
2.  **Find the Process:** Even if `systemctl` failed, the process is likely still running. You need to find its Process ID (PID).
    
    Bash
    
    ```
    # Search for the process by name
    ps aux | grep cruncher.sh
    # Or use a more direct tool
    pgrep cruncher.sh
    
    ```
    
3.  **Send the Signals:** You can communicate with a process using signals.
    
    -   The standard `kill <PID>` command sends **SIGTERM (15)**, which is a polite request to shut down. Try it. Did the process go away?
        
    -   If a process ignores SIGTERM, you must use a more forceful signal that cannot be ignored: **SIGKILL (9)**. How do you send this specific signal using the `kill` command?
        

**Goal for this Phase:** Understand why the process ignores the normal stop signal and successfully terminate it using the correct, more forceful signal.

----------

### Phase 4: Advanced Permissions and Final Touches

The service is stable. Now, let's implement the final security and operational requirements.

**Your Investigation Checklist:**

1.  **The SUID Challenge:** Alex needs a utility at `/opt/datacruncher/read_secret` to read a root-only file at `/etc/datacruncher_secret`. You cannot change the permissions on the secret file itself.
    
    -   Try to run the utility as the application user: `sudo -u cruncher_user /opt/datacruncher/read_secret`. Observe the "Permission denied" error.
        
    -   **Hint:** How can you make an executable run with the permissions of its **owner** (`root`) rather than the user running it? Look for a "special permission" bit that does this.
        
2.  **The SGID Challenge:** All new files in `/var/data/collaborative_data` must automatically get `cruncher_group` as their group.
    
    -   Test the current behavior: `sudo -u cruncher_user touch /var/data/collaborative_data/test.txt`. Check the new file's group with `ls -l`. It will be wrong.
        
    -   **Hint:** How can you make a **directory** pass its own group down to all new files created inside it? This requires another "special permission" bit set on the directory.
        
3.  **The Enable Challenge:** The service works, but it won't start after a reboot.
    
    -   Check if the service is enabled: `systemctl is-enabled datacruncher.service`.
        
    -   Try to enable it: `sudo systemctl enable datacruncher.service`. Read the error message carefully. It will tell you something is missing from the `.service` file.
        
    -   **Hint:** A service file needs an `[Install]` section to tell `systemctl` how to hook it into the boot process. Research what this section looks like. After you edit the file, you must tell `systemd` to re-read it before you can enable it. What is that command?
        

**Goal for this Phase:** Complete all three tasks. The `read_secret` utility works, new files in the collaborative directory have the correct group, and the service is successfully enabled to start on boot.

Good luck, Admin. The system is in your hands. 🛠️
