

# 👩‍🎓  Corrupt Log Investigator

### The Scenario

You are an SRE on call. A critical application has been unstable, and the log file (`system.log`) is a *mess*. It's filled with normal log lines, but also random "junk" lines that are corrupting the log parser.

Your manager doesn't need the file fixed right now. They just need a **quick report** to understand the scope of the problem.

**Your Mission:** Write a Bash script named `log_analyzer.sh` that reads `system.log` and generates a summary report of all *valid* log entries.

### Step 1: Create the "Corrupt" Log File

Copy and paste this entire block into your terminal. This will create the `system.log` file in your current directory.

```bash
cat << 'EOF' > system.log
INFO:User 'admin' logged in.
WARN:Disk space running low on /dev/sda1 (90% full).
This line is pure junk. No one knows where it came from.
ERROR:Service 'payment-gateway' failed to respond.
INFO:User 'guest' accessed /index.
DEBUG:Query string: 'SELECT * FROM users'
ERROR:NullPointerException at com.example.app.Main:42
Just a random comment.
INFO:User 'admin' updated settings.
WARN:High CPU usage detected (95%).
???:Unknown error code 123.
CRITICAL:Database connection lost!
INFO:User 'guest' logged out.
ERROR:Service 'auth-service' failed to respond.
EOF
```

### Step 2: Write Your Analyzer Script

Create a new file: `touch log_analyzer.sh` and `chmod +x log_analyzer.sh`.

Now, open `log_analyzer.sh` and start building your script. Here is the logical guide:

1.  **Initialize Counters:** At the top of your script, create a variable for each log level you want to count. Set them all to 0.

      * `INFO_COUNT=0`
      * `WARN_COUNT=0`
      * `ERROR_COUNT=0`
      * `OTHER_COUNT=0` (for things like `DEBUG`, `CRITICAL`, or the `???` one)

2.  **Read the File:** Use a `while read line` loop to read the `system.log` file line by line.

      * **Hint:** The structure is `while read line; do ... done < system.log`

3.  **Filter the Junk:** Inside your loop, you must first check if the line is valid. A valid line has a colon (`:`) in it.

      * Use an `if [[ "$line" == *":"* ]]; then ... fi` block to process *only* the valid lines.

4.  **Extract the Log Level:** Inside the `if` block, you need to get the part *before* the colon.

      * **Hint:** `echo "$line" | cut -d':' -f1` will "cut" the line at the colon and give you the first part. Store this in a variable (e.g., `level`).

5.  **Count the Levels:** This is the perfect job for a `case` statement. Use a `case` on your `$level` variable to check what it is and increment the correct counter.

      * **Hint:**
        ```bash
        case "$level" in
            INFO)
                ((INFO_COUNT++))
                ;;
            WARN)
                ((WARN_COUNT++))
                ;;
            ERROR)
                ((ERROR_COUNT++))
                ;;
            *)  # This is the "default" case
                ((OTHER_COUNT++))
                ;;
        esac
        ```

6.  **Print the Final Report:** *After* your `while` loop has finished, print a clean report.

      * **Hint:** Use `echo` to print the final values of your counter variables.

### Expected Output

If your script is correct, running `./log_analyzer.sh` should produce this output:

```
--- Log Analysis Report ---
INFO:     4
WARNING:  2
ERROR:    3
OTHER:    3
---------------------------
```
