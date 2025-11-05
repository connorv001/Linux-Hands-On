## Lab Challenge: The Bash Vending Machine

**Objective:** Use `while`, `case`, `if`, and `break` to build a simple, interactive vending machine menu.

**Scenario:** You are building the software for a new command-line vending machine. Your script needs to:

1.  Show a list of items to the user.
2.  Keep running until the user decides to quit.
3.  Handle whatever input the user gives, whether it's a valid item or not.

**Setup:**
Open a new script (e.g., `vending.sh`) and start with this. This time, we'll use a `while true` loop, which is an infinite loop that we must *manually* `break` out of.

```bash
#!/bin/bash

echo "============================"
echo " Welcome to the BASH Vending!"
echo "============================"

# This loop will run forever until we 'break' out of it.
while true
do
    echo "" # Print a blank line for spacing
    echo "--- Menu ---"
    echo "1: Soda ($1)"
    echo "2: Chips ($2)"
    echo "3: Candy ($1)"
    echo "q: Quit"
    
    # Ask the user for their choice
    read -p "Please enter your selection: " choice

    # --- YOUR TASKS GO BELOW ---

done

echo "============================"
echo " Thanks for using the machine!"
echo "============================"
```

-----

### Task 1: Handle the User's Choice (`case` statement)

**Goal:** Use a `case` statement to do something based on the user's input.

  * Inside the `while` loop (where the "YOUR TASKS GO BELOW" comment is), create a **`case` statement** that checks the value of the `$choice` variable.
  * Add an "arm" for each valid menu option (`1`, `2`, `3`, and `q`).
  * For now, just `echo` a message for each one.
  * Add a "default" arm (`*`) to catch any a user enters.

### Hint

Your `case` statement structure will look like this:

```bash
case "$choice" in
    1)
        echo "Dispensing Soda..."
        ;;
    2)
        echo "Dispensing Chips..."
        ;;
    3)
        echo "Dispensing Candy..."
        ;;
    q)
        echo "Quitting..."
        ;;
    *)
        echo "Invalid selection. Please try again."
        ;;
esac
```

-----

### Task 2: Actually Quit the Loop (`break`)

**Goal:** Learn to exit an infinite loop using `break`.

Your script from Task 1 will show the "Quitting..." message, but then it will just show the menu again\! This is because the `while true` loop is still running.

  * In your `case` statement, find the `q)` arm.
  * Right *after* the `echo "Quitting..."` line, add the **`break`** keyword.
  * This will tell Bash to "break out" of the `while` loop and continue to the end of the script.

**Expected Output:**
When you run the script and press `q`, it should print "Quitting..." and then "Thanks for using the machine\!" before it exits.

-----

### Task 3: Wait for Confirmation (`until` loop)

**Goal:** Use an `until` loop to validate a dangerous action.

What if the user didn't mean to quit? Let's add a confirmation step.

  * Go back to your `q)` arm in the `case` statement.
  * **Remove** the `break` command (for now).
  * We will ask the user for confirmation. We must keep asking **until** they type `yes` or `no`.
  * Inside the `q)` arm, create an **`until` loop** that asks for confirmation.

### Hint

Here is the logic you can add right inside the `q)` arm:

```bash
q)
    echo "Quitting..."
    
    # Reset confirm_answer each time
    confirm_answer=""
    
    until [[ "$confirm_answer" == "yes" || "$confirm_answer" == "no" ]]
    do
        read -p "Are you sure you want to quit? (yes/no): " confirm_answer
    done
    
    # Now, check the answer
    if [[ "$confirm_answer" == "yes" ]]; then
        echo "Goodbye!"
        break # This is where the break moves to!
    else
        echo "OK, not quitting. Returning to menu."
        # No 'break' here, so the loop continues
    fi
    ;;
```

**Run your script and test this\!**

  * Press `q`.
  * Press `maybe` (it should ask again).
  * Press `no` (it should return to the main menu).
  * Press `q` again.
  * Press `yes` (it should quit for real).

-----

### Bonus Task: Keep Track of Money (`if` and variables)

**Goal:** Use variables and `if` statements to add more logic.

Let's add a "balance" to the machine.

1.  **Before** the `while true` loop, create a new variable: `balance=5`
2.  Change your menu `echo`s to show the balance: `echo "q: Quit (Your balance: $balance)"`
3.  Inside your `case` statement, modify the arms for `1`, `2`, and `3`.
4.  Use an **`if` statement** to check if the user has enough balance *before* "dispensing" the item.
      * Soda costs $1
      * Chips cost $2
      * Candy costs $1
5.  If they have enough balance, subtract the cost (`((balance = balance - 1))`) and print "Dispensing..."
6.  If they don't have enough, print "Insufficient funds\!"

### Hint (for Task 1: Soda)

```bash
1)
    if [[ $balance -ge 1 ]]; then
        echo "Dispensing Soda... ($1)"
        ((balance = balance - 1)) # Subtract $1
    else
        echo "!! Insufficient funds! Please add money. !!"
    fi
    ;;
```

Now you have a fully interactive menu that uses nested loops, conditionals, and a `case` statement\!
