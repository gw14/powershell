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

