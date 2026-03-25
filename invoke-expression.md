It looks like you are trying to download a script and then execute its contents. However, the way your current command is structured, it will likely just **print the text of the script** to your console rather than actually running the commands inside it.

Here is the breakdown of why that happens and the two best ways to fix it:

### The Problem
When you run `Invoke-Command -ScriptBlock { $script }`, PowerShell sees `$script` as a string variable. It simply "evaluates" the variable, which results in outputting the text content of the file. To PowerShell, this is like typing `"Hello World"`—it doesn't actually *do* anything.

---

### Method 1: The "Clean" Way (Using `Invoke-Expression`)
This is the most direct way to take a string of text and tell PowerShell, "Treat this text as code and run it."

```powershell
$url = "https://raw.githubusercontent.com/gw14/devops-sysadmin/refs/heads/main/arch-install-notes/links.ps1"
$scriptContent = (Invoke-WebRequest -Uri $url -UseBasicParsing).Content

# This executes the string as a command
Invoke-Expression $scriptContent
```

### Method 2: The "ScriptBlock" Way
If you want to use `Invoke-Command` (which is useful if you eventually want to run this on remote computers), you need to convert that string into a real ScriptBlock first.

```powershell
$url = "https://raw.githubusercontent.com/gw14/devops-sysadmin/refs/heads/main/arch-install-notes/links.ps1"
$scriptText = (Invoke-WebRequest -Uri $url -UseBasicParsing).Content

# Convert string to executable script block
$executable = [scriptblock]::Create($scriptText)

# Now run it
Invoke-Command -ScriptBlock $executable
```

---

### A Quick Security Note
Since you are pulling this from a public GitHub repo:
1.  **Trust:** Ensure you trust `gw14` (or that this is your own repo).
2.  **Verification:** Since the file is public, `Invoke-WebRequest` won't run into the Cloudflare issues your private GitLab repo had. 
3.  **Errors:** If the script requires specific modules or administrative privileges, `Invoke-Expression` will trigger those errors just as if you ran the script locally.

**Would you like me to help you combine this with the private GitLab logic we discussed earlier so you can run your private scripts the same way?**
