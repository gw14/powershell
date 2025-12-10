Here’s a clean, practical **PowerShell Escaped Characters Cheatsheet** — everything you need for strings, regex, and special cases.

---

# **PowerShell Escaped Characters Cheatsheet**

## **1. Basic Escape Character**

PowerShell uses the **backtick (`)** as the escape character.

```
`n  New line
`r  Carriage return
`t  Tab
`b  Backspace
`0  Null
`a  Alert (beep)
`"  Double quote
`'  Single quote (rarely needed)
```

---

## **2. String Escaping**

### **Inside Double-Quoted Strings (" ")**

```
"`"   → "
"`$   → $
"``   → `
```

### Example:

```powershell
"Path is C:`Windows`System32"
```

### **Inside Single-Quoted Strings (' ')**

Single quotes are literal.
To escape a single quote inside `' '`, **double it**:

```powershell
'It''s a test'
```

---

## **3. File Paths**

PowerShell accepts normal Windows paths without escaping:

```powershell
"C:\Program Files\MyApp"
```

But you can escape backslashes if needed:

```powershell
"C:\\Program Files\\MyApp"
```

---

## **4. Variables in Strings**

To prevent variable expansion:

```powershell
"`$myvar"   # shows $myvar instead of expanding
```

---

## **5. Escaping in Regex**

PowerShell uses .NET regex — escape with a backslash `\`:

```
\.  \*  \+  \?  \(  \)  \[  \]  \{  \}  \|  \^  \$  \\  
```

### Example:

```powershell
"hello.txt" -match "\.txt$"
```

To use a literal backslash:

```powershell
"c:\" -match "c:\\"
```

---

## **6. Escaping for PowerShell Commands**

### **Escape characters in Command Arguments**

```powershell
ping google.com -n 5
Write-Host "You said: `"hello`""
```

### **Escaping in Here-Strings**

**Single-quoted here-string = literal**

```powershell
@'
This is *literal* text.
No escaping needed.
'@
```

**Double-quoted here-string = expanded**

```powershell
@"
New line = `n
Variable: $env:COMPUTERNAME
"@
```

---

## **7. JSON Escaping**

When building JSON manually:

```
\"  → Double quote
\\  → Backslash
\n  → Newline (JSON standard)
```

Example:

```powershell
$json = "{""name"":""Guy""}"
```

---

## **8. XML Escaping**

```
&   → &amp;
<   → &lt;
>   → &gt;
"   → &quot;
'   → &apos;
```

---

## **9. Escaping Special Characters in PowerShell Patterns (`-like`)**

Use backtick:

```
`*  → Literal *
`?  → Literal ?
`[  → Literal [
```

Example:

```powershell
"file[1]" -like "file`[1`]"
```

---

## **10. Useful Examples**

### **Escape double quotes**

```powershell
Write-Host "He said: `"Yes`""
```

### **Escape dollar sign**

```powershell
"`$100 cost"
```

### **Escape backtick itself**

```powershell
"Use two backticks `` to show one."
```

---

If you want, I can also create:
✅ A printable PDF
✅ A color-coded Markdown table
✅ A VSCode / PowerShell ISE snippet version
