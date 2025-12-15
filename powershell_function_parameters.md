PowerShell function parameters are a key part of writing reusable, flexible scripts. Let’s go step by step so you get a clear understanding.

---

### **1. Basic Parameter Syntax**

You can define a function with parameters like this:

```powershell
function Get-Greeting {
    param (
        $Name
    )
    "Hello, $Name!"
}

Get-Greeting -Name "Guy"
```

* `param` block defines parameters.
* `$Name` is a **positional parameter** by default.
* You call it using `-Name <value>`.

---

### **2. Positional vs Named Parameters**

* **Positional**: You can pass the argument without specifying the parameter name.

```powershell
function Add-Numbers {
    param ($x, $y)
    $x + $y
}

Add-Numbers 5 10   # 5 is $x, 10 is $y
Add-Numbers -y 10 -x 5  # Order doesn’t matter if you use names
```

* **Named**: Using `-ParameterName value` allows you to specify parameters in any order.

---

### **3. Parameter Types**

You can enforce the type of a parameter:

```powershell
function Multiply-Numbers {
    param (
        [int]$a,
        [int]$b
    )
    $a * $b
}

Multiply-Numbers 3 4  # Works
Multiply-Numbers 3 "Hello"  # Error
```

* Common types: `[int]`, `[string]`, `[bool]`, `[datetime]`, `[object]`.

---

### **4. Default Values**

You can provide default values:

```powershell
function Greet {
    param (
        [string]$Name = "World"
    )
    "Hello, $Name!"
}

Greet          # "Hello, World!"
Greet -Name "Guy"  # "Hello, Guy!"
```

---

### **5. Mandatory Parameters**

Make a parameter required:

```powershell
function Send-Message {
    param (
        [Parameter(Mandatory=$true)]
        [string]$Message
    )
    "Sending: $Message"
}

Send-Message  # Prompts for Message if not provided
```

* Can be combined with types and default values.

---

### **6. Parameter Validation**

You can restrict input with validation attributes:

```powershell
function Set-Level {
    param (
        [ValidateRange(1, 10)]
        [int]$Level
    )
    "Level set to $Level"
}

Set-Level 5   # OK
Set-Level 11  # Error
```

Other validation options:

* `[ValidateSet("Low","Medium","High")]` → only allow specific values.
* `[ValidatePattern("\d{3}-\d{2}-\d{4}")]` → match regex.

---

### **7. Switch Parameters**

Switches are like Boolean flags:

```powershell
function Show-Message {
    param (
        [switch]$Verbose
    )
    if ($Verbose) { "Verbose mode ON" } else { "Normal mode" }
}

Show-Message -Verbose
Show-Message  # Verbose off
```

* No need to specify `$true` or `$false`.

---

### **8. Advanced Parameter Features**

* **Aliases**: Allow multiple names for a parameter.

```powershell
param (
    [Alias("n")]
    [string]$Name
)
```

* **Pipeline input**: Accept values from the pipeline.

```powershell
param (
    [Parameter(ValueFromPipeline=$true)]
    [string]$Name
)
```

* **Parameter sets**: Different sets of parameters for different use cases.

---

### ✅ **Tips**

* Always define parameters in a `param` block (cleaner than `$args`).
* Use types to catch errors early.
* Mandatory parameters are great for scripts that must have certain inputs.
* Switch parameters simplify Boolean flags.

---

If you want, I can make a **cheat sheet showing all common PowerShell parameter features in one table**—super handy for reference.

Do you want me to make that?

Here’s a **PowerShell Function Parameters Cheat Sheet** you can keep as a quick reference.

---

## 🧩 PowerShell Function Parameters – Cheat Sheet

### **Basic Structure**

```powershell
function My-Function {
    param (
        $Param1,
        $Param2
    )
}
```

---

### **Parameter Types**

```powershell
[string]    # Text
[int]       # Integer
[bool]      # Boolean
[datetime]  # Date & time
[array]     # Collection
[object]    # Anything
```

Example:

```powershell
param (
    [int]$Count,
    [string]$Name
)
```

---

### **Named vs Positional**

```powershell
param (
    $First,
    $Second
)

My-Function 1 2
My-Function -Second 2 -First 1
```

---

### **Default Values**

```powershell
param (
    [string]$Env = "Dev"
)
```

---

### **Mandatory Parameters**

```powershell
param (
    [Parameter(Mandatory)]
    [string]$Path
)
```

---

### **Switch Parameters**

```powershell
param (
    [switch]$Force
)
```

Usage:

```powershell
My-Function -Force
```

---

### **Parameter Validation**

```powershell
param (
    [ValidateSet("Low","Medium","High")]
    [string]$Priority,

    [ValidateRange(1,10)]
    [int]$Level,

    [ValidatePattern("^\d+$")]
    [string]$Id
)
```

---

### **Aliases**

```powershell
param (
    [Alias("n")]
    [string]$Name
)
```

Usage:

```powershell
My-Function -n "Guy"
```

---

### **Pipeline Input**

```powershell
param (
    [Parameter(ValueFromPipeline)]
    [string]$Name
)
```

Usage:

```powershell
"Guy","Dana" | My-Function
```

---

### **Pipeline by Property Name**

```powershell
param (
    [Parameter(ValueFromPipelineByPropertyName)]
    [string]$Name
)
```

---

### **Parameter Sets**

```powershell
param (
    [Parameter(ParameterSetName="ByName")]
    [string]$Name,

    [Parameter(ParameterSetName="ById")]
    [int]$Id
)
```

Usage:

```powershell
My-Function -Name "Guy"
My-Function -Id 5
```

---

### **Advanced Function (Cmdlet-like)**

```powershell
function Get-Data {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [string]$Source
    )
}
```

Enables:

* `-Verbose`
* `-Debug`
* `-ErrorAction`

---

### **Common Parameter Attributes**

| Attribute                         | Purpose                   |
| --------------------------------- | ------------------------- |
| `Mandatory`                       | Require input             |
| `Position`                        | Control argument position |
| `HelpMessage`                     | Prompt help text          |
| `ValueFromPipeline`               | Accept pipeline input     |
| `ValueFromPipelineByPropertyName` | Bind object properties    |
| `ParameterSetName`                | Mutually exclusive params |

---

### **Best Practices**

✔ Use **types**
✔ Use **switches** for flags
✔ Use **validation** to prevent bad input
✔ Prefer **advanced functions** for reusable scripts
✔ Avoid `$args` unless absolutely needed

---

If you want, I can:

* 🔥 Show **real-world function examples** (filesystem, network, APIs)
* 🧪 Explain **parameter binding rules** (how PowerShell decides what goes where)
* ⚙️ Convert one of your existing scripts into a **proper advanced function**

Just tell me what you want next.

Great question — this is where you move from “I can write functions” to **“I write PowerShell like a pro.”**
Below are the **less obvious but very important things** about PowerShell function parameters that most beginners miss.

---

## 1️⃣ Parameter Binding (How PowerShell Decides What Goes Where)

PowerShell binds parameters in this order:

1. **Named parameters**
2. **Positional parameters**
3. **Pipeline input (by value)**
4. **Pipeline input (by property name)**

Why this matters:

```powershell
function Test-Bind {
    param (
        [string]$Name,
        [int]$Age
    )
}

Test-Bind "Guy" 30
```

✔ `"Guy"` → `$Name`
✔ `30` → `$Age`

But:

```powershell
"Guy" | Test-Bind
```

Only works if `$Name` accepts pipeline input.

---

## 2️⃣ ValueFromPipeline vs ValueFromPipelineByPropertyName

These are **not the same**:

```powershell
param (
    [Parameter(ValueFromPipeline)]
    [string]$Name
)
```

✔ Accepts **raw values**:

```powershell
"Guy" | My-Function
```

```powershell
param (
    [Parameter(ValueFromPipelineByPropertyName)]
    [string]$Name
)
```

✔ Accepts **objects with matching properties**:

```powershell
[pscustomobject]@{ Name="Guy" } | My-Function
```

💡 Real-world PowerShell relies heavily on **property-name binding**.

---

## 3️⃣ BEGIN / PROCESS / END Blocks (Critical for Pipeline)

If your function accepts pipeline input, you **must** understand this:

```powershell
function Show-Name {
    param (
        [Parameter(ValueFromPipeline)]
        [string]$Name
    )

    begin { "Starting" }
    process { "Hello $Name" }
    end { "Done" }
}
```

Pipeline:

```powershell
"Guy","Dana" | Show-Name
```

Output:

```
Starting
Hello Guy
Hello Dana
Done
```

✔ `begin` → runs once
✔ `process` → runs per input
✔ `end` → runs once

---

## 4️⃣ CmdletBinding Is a Big Deal

```powershell
[CmdletBinding()]
param ()
```

Adds **built-in parameters for free**:

* `-Verbose`
* `-Debug`
* `-WhatIf`
* `-Confirm`
* `-ErrorAction`

Example:

```powershell
[CmdletBinding(SupportsShouldProcess)]
param (
    [string]$Path
)

if ($PSCmdlet.ShouldProcess($Path)) {
    Remove-Item $Path
}
```

This is **mandatory** for safe, production scripts.

---

## 5️⃣ `$PSBoundParameters` (Know What Was Actually Passed)

```powershell
$PSBoundParameters
```

Returns a hashtable of **only parameters the user provided**.

Why useful?

```powershell
if ($PSBoundParameters.ContainsKey('Force')) {
    "Force was explicitly set"
}
```

✔ Distinguishes between default values and user input.

---

## 6️⃣ Dynamic Parameters (Advanced but Powerful)

Parameters that appear **only under certain conditions**.

Example use cases:

* Different params per OS
* Different params per mode

```powershell
dynamicparam {
    # Rare, but extremely powerful
}
```

⚠️ Only use when absolutely necessary.

---

## 7️⃣ Parameter Sets = Mutual Exclusivity

Parameter sets prevent invalid combinations:

```powershell
param (
    [Parameter(Mandatory, ParameterSetName="ByName")]
    [string]$Name,

    [Parameter(Mandatory, ParameterSetName="ById")]
    [int]$Id
)
```

PowerShell **enforces correctness** for you.

---

## 8️⃣ Validation Happens Before Your Code Runs

This means:

```powershell
param (
    [ValidateSet("Dev","Prod")]
    [string]$Env
)
```

If input is invalid:
❌ Your function **never executes**

Use this to:

* Prevent bad states
* Simplify logic
* Reduce `if` statements

---

## 9️⃣ Help Messages & Self-Documenting Code

```powershell
param (
    [Parameter(
        Mandatory,
        HelpMessage="Path to the configuration file"
    )]
    [string]$Path
)
```

Users get prompted with help automatically.

---

## 🔟 Common Mistakes to Avoid

❌ Using `$args` instead of `param`
❌ Not typing parameters
❌ Not supporting pipeline when appropriate
❌ Forgetting `CmdletBinding()`
❌ Writing functions that only work with strings (not objects)

---

## 🧠 Mental Model (This Will Level You Up)

Think of PowerShell functions as:

> **Object processors, not string processors**

Good parameters:

* Accept objects
* Bind from pipeline
* Validate early
* Fail safely

---

## 🚀 What I Recommend Next (Based on Your Interests)

Given your DevOps / automation focus:

* Write **1 function** that:

  * Accepts pipeline input
  * Uses `CmdletBinding`
  * Uses parameter sets
  * Uses validation
* Then turn it into a **module**

If you want, I can:

* 🔨 Review a function you wrote
* 🧪 Give you **parameter-binding exercises**
* 📦 Show how to package functions into a module
* ⚔️ Compare PowerShell parameters vs Bash flags (useful for your stack)

Just tell me what you want to dive into next.
