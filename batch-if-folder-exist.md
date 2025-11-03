Hello\! I'd be happy to show you the **Batch syntax** to check if a folder (or directory) exists.

You can use the `IF EXIST` command in a Batch script for this purpose.

## 📂 Checking for Folder Existence

The basic syntax uses the `IF EXIST` command followed by the folder path. You then specify what action to take if the condition is **true** (the folder exists) and optionally what to do if the condition is **false** (the folder does not exist).

### 1\. Simple Check (Exit if it exists)

This script will check for the folder and display a message indicating its status.

```batch
@echo off

SET "target_folder=C:\Users\%USERNAME%\Desktop\MyTestFolder"

IF EXIST "%target_folder%" (
    ECHO The folder "%target_folder%" exists.
) ELSE (
    ECHO The folder "%target_folder%" does NOT exist.
)

PAUSE
```

-----

### 2\. Action Example (Create the folder if it doesn't exist)

A common use case is to create the folder if it's missing.

```batch
@echo off

SET "target_folder=C:\NewProjectData"

IF EXIST "%target_folder%" (
    ECHO The folder "%target_folder%" already exists. Skipping creation.
) ELSE (
    ECHO The folder "%target_folder%" does not exist. Creating it now...
    MD "%target_folder%"
    ECHO Folder created successfully!
)

PAUSE
```

### Key Elements Explained:

  * **`@echo off`**: This is usually put at the start of a Batch file to hide the commands being executed, showing only the output.
  * **`SET "target_folder=..."`**: Defines a variable to hold the path for clarity. The quotes ensure paths with spaces are handled correctly.
  * **`IF EXIST "%target_folder%"`**: This is the core check. It tests if the file or directory specified in the path exists.
  * **`(...)`**: The parentheses group commands to be executed if the condition (the folder existing) is **true**.
  * **`ELSE (...)`**: This block is executed if the condition is **false** (the folder does *not* exist).
  * **`MD "%target_folder%"`**: The command to **M**ake **D**irectory.

-----

Would you like me to show you how to use this check to **copy files** into a folder only if it exists?

That's a great follow-up question\! To **negate** the check (meaning you want to check if a folder **does NOT exist**), you simply use the `NOT` keyword directly before the `EXIST` condition.

## 🚫 Negating the Folder Check

The syntax becomes: `IF NOT EXIST [path]`.

### 1\. Simple Negated Check (Action if it does NOT exist)

This is the standard way to check for non-existence, often used before creating a new directory.

```batch
@echo off

SET "target_folder=C:\Users\%USERNAME%\Desktop\MyNewFolder"

IF NOT EXIST "%target_folder%" (
    ECHO The folder "%target_folder%" does NOT exist.
    ECHO Proceeding to create the folder now...
    MD "%target_folder%"
) ELSE (
    ECHO The folder "%target_folder%" already exists. No action needed.
)

PAUSE
```

-----

### 2\. Negated Check Explained

| Part | Meaning |
| :--- | :--- |
| `IF` | Starts the conditional statement. |
| `NOT` | **Negates** the condition that follows. |
| `EXIST` | Checks for the existence of the file or folder. |
| `"%target_folder%"` | The path being checked. |
| `(...)` | The block of commands to execute if the **folder does NOT exist**. |

This structure is much cleaner than using `IF EXIST` and then putting your actions in the `ELSE` block, especially when the main goal is to handle the non-existence case.

-----

I previously offered to show you how to **copy files** into a folder only if it exists. Now that you know how to negate, would you like to see how to use the negated check to **create a folder** and *then* copy files into it?
