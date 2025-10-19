The term "PowerShell Heredoc" actually refers to a **PowerShell Here-String**. It's a powerful and convenient way to define **multi-line strings** in PowerShell scripts.

Here's what you need to know:

## Key Features

  * **Multi-Line Support:** A here-string allows you to include line breaks, tabs, and other whitespace directly in the string definition without using escape characters (`\n`, `\t`, etc.). This is especially useful for large blocks of text, such as:
      * HTML, XML, or JSON data.
      * SQL queries.
      * Embedded code (e.g., C\# for `Add-Type`).
      * Help text for functions/scripts.
  * **Literal Quotes:** Quotation marks (single or double) within a here-string are generally interpreted **literally**, meaning you don't need to escape them (unless the inner quote matches the closing delimiter).

-----

## Syntax and Types

A here-string is enclosed by the `@` symbol followed by a quote mark (`'` or `"`) and then another `@` symbol and the matching quote mark on a line by itself.

### 1\. Literal Here-String (Single-Quoted)

  * **Syntax:** `@'<newline>...<string content>...<newline>'@`
  * **Behavior:** The content is treated **literally**. **Variables and subexpressions are NOT expanded** (they appear as plain text, including the `$`).
  * **Use Case:** Ideal when you need a string that must be exactly as written, without any dynamic substitution.

<!-- end list -->

```powershell
$myVariable = "World"
$literalString = @'
Hello, $myVariable!
This is a literal string.
It will not expand variables or $(Get-Date)
'@
# Output will literally contain "$myVariable" and "$(Get-Date)"
```

### 2\. Expanding Here-String (Double-Quoted)

  * **Syntax:** `@`\<newline\>...\<string content\>...\<newline\>`"@`
  * **Behavior:** The content is an **expandable string**. **Variables** (preceded by `$`) and **subexpressions** (enclosed in `$()`) **are replaced** with their values.
  * **Use Case:** Best for strings where you need to inject dynamic values, like inserting a variable's value or the output of a command.

<!-- end list -->

```powershell
$myVariable = "PowerShell"
$expandingString = @"
Hello, $myVariable!
The current date is: $(Get-Date)
This is an expanding string.
"@
# Output will substitute "$myVariable" with "PowerShell" and run the Get-Date command.
```

-----

## Important Rules

1.  **Delimiters on Separate Lines:** The opening delimiter (`@"` or `@'`) and the closing delimiter (`"@` or `'@`) **must be on lines by themselves**.
      * **NO** characters are allowed after the opening delimiter on the first line.
      * The closing delimiter must be the very first characters on its line, followed immediately by a newline (or end of file). **No indentation is allowed** before the closing delimiter.
2.  **Quotes Inside:**
      * In a **literal** here-string (`@'...'@`), a single quote (`'`) *inside* the string must be doubled (`''`) to appear literally.
      * In an **expanding** here-string (`@"..."@`), a double quote (`"`) *inside* the string must be doubled (`""`) to appear literally.

-----

[PowerShell Basics - Strings](https://www.youtube.com/watch?v=n8tZ4jfpPrs) provides a more comprehensive look at various string concepts in PowerShell, including here-strings.
http://googleusercontent.com/youtube_content/0
