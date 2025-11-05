-----

## Lab Challenge: The Bash File Sorter

**Objective:** Learn to use `for`, `while`, `until`, `break`, and `continue` by writing a script that *simulates* sorting files in a directory.

**Scenario:** Your `Downloads` folder is a mess\! It's full of different file types. Your task is to write a script that iterates through a list of filenames and decides where they should go.

**Setup:**
Open a new script file (e.g., `sorter.sh`) and add this code at the top. This is the "messy folder" we'll practice on.

```bash
#!/bin/bash

# This variable is our "messy folder"
# We use quotes to handle filenames with spaces
FILES_TO_SORT=(
    "report-final.txt"
    "vacation-photo.jpg"
    "presentation.pptx"
    "important-data.zip"
    "music-track.mp3"
    "urgent-memo.txt"
    "project-archive.zip"
    "STOP.here"
    "selfie.png"
    "old-essay.docx"
)

echo "Starting the file sorter script..."
echo "---"

# --- YOUR TASKS GO BELOW ---
```

-----

### Task 1: List All Files (`for` loop)

**Goal:** Learn to use a `for` loop to iterate over a list of items.

Your first task is to simply list every file in the `$FILES_TO_SORT` array.

  * Write a **`for` loop** that goes through each item in the `FILES_TO_SORT` array.
      * *Hint:* The syntax for an array is `for item in "${ARRAY_NAME[@]}"`
  * Inside the loop, `echo` a message like: `Found file: report-final.txt`

**Expected Output:**

```
Found file: report-final.txt
Found file: vacation-photo.jpg
... (and so on for all files)
```

-----

### Task 2: Sort the Files (`if / else`)

**Goal:** Add conditional logic (`if`) inside your loop to make decisions.

Now, let's actually sort them. Modify your `for` loop from Task 1.

  * Inside the loop, check the filename for its extension.
  * Use `if`, `elif` (else if), and `else` to print what you *would* do.
      * If the file ends in `.txt` or `.docx` or `.pptx`, print: `Sorting [filename] into the 'Documents' folder.`
      * If the file ends in `.jpg` or `.png`, print: `Sorting [filename] into the 'Images' folder.`
      * If the file ends in `.mp3`, print: `Sorting [filename] into the 'Music' folder.`
      * For anything else, print: `Not sure where to put [filename].`
  * *Hint:* You can check for a file extension like this: `if [[ "$file" == *".txt" ]]`

**Expected Output:**

```
Sorting report-final.txt into the 'Documents' folder.
Sorting vacation-photo.jpg into the 'Images' folder.
... (and so on)
```

-----

### Task 3: Skip Certain Files (`continue`)

**Goal:** Learn to use `continue` to skip an iteration.

Your manager says you should **not** sort `.zip` files. They need to be handled manually.

  * Inside your `for` loop (before the `if/elif` block), add a new check.
  * If the file ends in `.zip`, print a message like: `Skipping archive file: important-data.zip`
  * After printing, use the **`continue`** keyword to immediately stop processing that file and move to the next one.

**Expected Output:**
Your script should now print `Skipping...` for the `.zip` files and *not* print the "Not sure where to put..." message for them.

-----

### Task 4: Stop on a Magic File (`break`)

**Goal:** Learn to use `break` to exit a loop completely.

To prevent accidents, you've added a "magic file" named `STOP.here`. If your script sees this file, it should stop all sorting immediately.

  * Inside your `for` loop (right at the beginning), check if the filename is exactly `STOP.here`.
  * If it is, print a message like: `STOP.here file found! Halting all operations.`
  * Use the **`break`** keyword to exit the `for` loop completely.

**Expected Output:**
Your script will now run as before, but it will stop as soon as it hits `STOP.here` and will not process `selfie.png` or `old-essay.docx`.

-----

### Task 5: Wait for Confirmation (`until` loop)

**Goal:** Learn to use an `until` loop to wait for a specific condition.

This script is powerful. Before running the `for` loop, you should ask the user for confirmation.

  * **Before** your `for` loop (after the `echo "---"` line), add this.
  * Write an **`until` loop** that asks the user a question.
  * Inside the loop, use `read -p "Are you sure you want to sort files? (yes/no): " answer`
  * The loop should continue **until** the `$answer` variable is equal to "yes".
  * If the user types anything else (like "no" or "maybe"), the loop should repeat and ask again.

**Expected Output:**
Your script will now wait for you to type "yes" before it even starts the `for` loop.

-----

### Bonus Task 6: Simulate a Slow Process (`while` loop)

**Goal:** Learn to use a `while` loop as a simple counter.

Let's pretend "starting the scanner" is a slow process that takes 3 seconds.

  * **Before** the `until` loop, add this new section.
  * Create a variable `counter` and set it to 1.
  * Write a **`while` loop** that runs as long as `$counter` is less than or equal to 3 (`-le 3`).
  * Inside the loop:
      * Print `Scanner starting... $counter / 3`
      * Increment the counter: `((counter++))`
      * Wait for 1 second: `sleep 1`
  * After the loop, print `Scanner ready.`

**Expected Output:**
Your script will now pause for 3 seconds, printing `Scanner starting...`, *before* it asks you for confirmation.
