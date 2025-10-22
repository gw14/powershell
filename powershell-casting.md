A **PowerShell cast** is an operation that explicitly converts a value from one data type to another. It tells the PowerShell engine to treat an expression's result as a specific data type, even if it might naturally be something else.

You use a cast to ensure that data conforms to a required format for a command, property, or method.

## How to Perform a Cast

You perform a cast in PowerShell by placing the desired **data type enclosed in square brackets** (`[]`) immediately before the value or variable you want to convert.

The general syntax is:

```powershell
[DataType] $ValueOrExpression
```

| Component | Description | Example |
| :--- | :--- | :--- |
| `[DataType]` | The **target data type** to convert the value to (e.g., `[int]`, `[string]`, `[datetime]`). | `[int]` |
| `$ValueOrExpression` | The variable or expression containing the value to be converted. | `"100"` |

### Examples of Casts

| Scenario | Code | Result/Explanation |
| :--- | :--- | :--- |
| **String to Integer** | `[int] "123"` | Converts the string `"123"` to the integer `123`. |
| **Integer to String** | `[string] 456` | Converts the integer `456` to the string `"456"`. |
| **String to Boolean** | `[bool] "true"` | Converts the string `"true"` to the boolean value `$True`. Note that *any* non-empty string casts to `$True`. |
| **Integer to DateTime** | `[datetime] 0` | Converts the number `0` (which is treated as the number of days since $1/1/1900$ in this context) to a `DateTime` object. |
| **Cast to a Specific Type** | `[System.Net.IPAddress] "192.168.1.1"` | Converts the string to a `.NET` **IPAddress** object, allowing access to its properties and methods. |
| **Casting a Variable** | `$num = "789"; [int] $num` | The variable `$num`'s string value is cast to an integer. |

-----

## When to Use Casting

Casting is crucial in several common PowerShell scenarios:

1.  **Enforcing Data Types for Parameters:** Many cmdlets or functions require parameters to be of a specific type (e.g., an `int` for a port number, or a `switch` for a boolean flag). Casting ensures the value you pass is accepted.

      * *Example:* If a function expects a `[long]` but you provide a large number as a string, casting it with `[long]` prevents an error.

2.  **Arithmetic and Comparisons:** Casting numeric strings to an integer (`[int]`) or decimal (`[decimal]`) is necessary to perform correct mathematical operations, as PowerShell's default behavior might treat the value as a string.

      * *Example:* `"5" + "5"` results in the string `"55"`, but `[int]"5" + [int]"5"` results in the integer `10`.

3.  **Using .NET Framework Types:** To interact with the full power of the .NET Framework, you often need to cast simple PowerShell data into specific .NET objects.

      * *Example:* Creating a color object: `[System.Drawing.Color]::FromName("Red")`

4.  **Enumerators (Enums):** When working with an enumeration (a set of named integral constants), you often need to cast the string name of the constant to the actual enumeration type.

      * *Example:* `[System.IO.FileAccess]::Read`

-----

## Implicit vs. Explicit Casting

  * **Explicit Casting (The focus of this explanation):** This is when **you** use the `[DataType]` syntax to force the conversion. If the conversion is impossible (e.g., casting the string `"hello"` to an `[int]`), PowerShell throws an error.
  * **Implicit Casting:** This is when **PowerShell** automatically converts a value for you, usually when assigning it to a variable that has already been strongly typed or when passing it to a parameter that expects a specific type.

<!-- end list -->

```powershell
# Example of a strongly-typed variable forcing an implicit cast
[int]$i = "99"  # PowerShell implicitly converts the string "99" to the integer 99
```
