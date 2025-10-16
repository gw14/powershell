The most common and built-in way to run commands asynchronously in PowerShell is by using **Jobs**. Jobs allow you to run scripts or cmdlets in the background without blocking your current session.

The primary cmdlets for managing asynchronous commands (jobs) are:

### 1\. `Start-Job`

This cmdlet runs a command or script block in a separate process on the local computer as a **BackgroundJob**. This provides good isolation but is generally slower and uses more resources than thread-based alternatives.

  * **Syntax:**
    ```powershell
    Start-Job -ScriptBlock { # Your asynchronous code here }
    ```
  * **Example:**
    ```powershell
    $job = Start-Job -ScriptBlock {
        Write-Host "Job starting..."
        Start-Sleep -Seconds 10 # Simulate a long-running command
        Get-Process | Select-Object -First 5
        Write-Host "Job finished."
    }
    ```

### 2\. `Start-ThreadJob` (Recommended for performance in PowerShell 6+)

The `ThreadJob` module (included in PowerShell 7+ and installable for Windows PowerShell 5.1) is generally **faster and more lightweight** because it runs the command in a separate thread within the *same* process.

  * **Syntax:**
    ```powershell
    # In Windows PowerShell 5.1, you might need: Install-Module ThreadJob
    Start-ThreadJob -ScriptBlock { # Your asynchronous code here }
    ```
  * **Example:**
    ```powershell
    $threadJob = Start-ThreadJob -ScriptBlock {
        1..5 | ForEach-Object {
            Start-Sleep -Seconds 1
            "Thread Job output: $_"
        }
    }
    ```

-----

### Managing PowerShell Jobs

Once you start a job (using either `Start-Job` or `Start-ThreadJob`), you use the following cmdlets to manage it:

| Cmdlet | Purpose |
| :--- | :--- |
| **`Get-Job`** | Checks the status of jobs started in the current session. |
| **`Wait-Job`** | Suspends the current session until one or more jobs are in a completed state (e.g., `Completed` or `Failed`). |
| **`Receive-Job`** | Retrieves the output (results) from a job. By default, it removes the results from the job object. Use the `-Keep` parameter to retrieve them and keep them in the job object. |
| **`Stop-Job`** | Stops a job that is currently running. |
| **`Remove-Job`** | Deletes the job object from the session. |

#### Example of Job Management

1.  **Start the job:**

    ```powershell
    $job = Start-Job -ScriptBlock { Start-Sleep -Seconds 5; "Task complete!" }
    ```

2.  **Check the status:**

    ```powershell
    Get-Job $job
    ```

    (You can continue working while it runs.)

3.  **Wait for completion (optional):**

    ```powershell
    Wait-Job $job
    ```

4.  **Retrieve results:**

    ```powershell
    Receive-Job $job -Keep # Retrieves the output and keeps it available
    Receive-Job $job -AutoRemoveJob # Retrieves output and removes the job object
    ```

### Other Async/Parallel Options

1.  **`ForEach-Object -Parallel` (PowerShell 7+)**
    For running a command against a collection of input objects simultaneously, the `-Parallel` parameter of `ForEach-Object` is a very efficient and clean method:

    ```powershell
    1..10 | ForEach-Object -Parallel {
        "Processing item $_"
        # ... long-running task ...
    }
    ```

2.  **`&` (Background Operator) (PowerShell 6+)**
    You can append the background operator (`&`) to any pipeline to run it immediately as a job:

    ```powershell
    Get-Process &
    ```

    This is shorthand for `Start-Job`.

-----

### Using PowerShell Scheduled Jobs

For tasks that need to run asynchronously at a specific time or recurring interval, you can use **Scheduled Jobs**. These are built on PowerShell jobs but are integrated with the Windows Task Scheduler.

  * **Cmdlets include:** `Register-ScheduledJob`, `New-JobTrigger`, `Get-ScheduledJob`.

  * **Example:**

    ```powershell
    $trigger = New-JobTrigger -Daily -At "3:00 AM"
    Register-ScheduledJob -Name "DailyCleanup" -ScriptBlock { Cleanup-Logs } -Trigger $trigger
    ```

This video, "Working with PowerShell Scheduled Jobs | Automate Tasks with Precision," is relevant because it demonstrates how to create, manage, and trigger scheduled jobs, which are a form of asynchronous command execution in PowerShell for recurring tasks.

[Working with PowerShell Scheduled Jobs | Automate Tasks with Precision](https://www.youtube.com/watch?v=xhmHDxa_7dw)
http://googleusercontent.com/youtube_content/0
