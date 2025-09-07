PowerShell arrays are data structures that store a collection of one or more items. They are a fundamental part of the language and can hold different data types, including strings, integers, and objects.

-----

## Creating Arrays

There are several ways to create an array in PowerShell.

  * **Using a comma-separated list:** The simplest method is to list the items separated by commas. PowerShell automatically recognizes this as an array.

<!-- end list -->

```powershell
$myArray = 'item1', 'item2', 'item3'
```

  * **Using the `@()` operator:** The array subexpression operator ensures the output is always an array, even if there's only one item. This is a good practice to avoid unexpected behavior when a command might return a single object or multiple objects.

<!-- end list -->

```powershell
$singleItemArray = @('single item')
$multiItemArray = @('item1', 'item2')
```

  * **Using the `New-Object` cmdlet:** This method explicitly creates an array of a specific size and type, which can be useful for performance in large-scale scripting.

<!-- end list -->

```powershell
$integerArray = New-Object 'System.Int32[]' 5 # Creates an integer array of size 5
```

-----

## Accessing and Modifying Arrays

You can access and modify array elements using their index. PowerShell arrays are **zero-indexed**, meaning the first element is at index 0.

  * **Accessing an element:**

<!-- end list -->

```powershell
$myArray = 'apple', 'banana', 'cherry'
$firstItem = $myArray[0] # $firstItem will be 'apple'
$lastItem = $myArray[-1] # $lastItem will be 'cherry'
```

  * **Accessing a range of elements:**

<!-- end list -->

```powershell
$myArray = 'apple', 'banana', 'cherry', 'grape'
$subset = $myArray[1..2] # $subset will be 'banana', 'cherry'
```

  * **Modifying an element:**

<!-- end list -->

```powershell
$myArray = 'apple', 'banana', 'cherry'
$myArray[1] = 'orange' # $myArray is now 'apple', 'orange', 'cherry'
```

-----

## Array Properties and Methods

Arrays have built-in properties and methods that provide information and functionality.

  * **`.Count` or `.Length`:** These properties return the number of elements in the array.

<!-- end list -->

```powershell
$myArray = 1, 2, 3, 4, 5
$myArray.Count # Returns 5
```

  * **`.Add()`:** This method is not available on fixed-size arrays. To add an item, you often create a new array or use the **`+=`** operator, which creates a new array and adds the item to it. Keep in mind that for very large arrays, this can be inefficient as it involves creating a new array in memory each time.

<!-- end list -->

```powershell
$myArray = 'item1', 'item2'
$myArray += 'item3' # $myArray is now 'item1', 'item2', 'item3'
```

  * **`.GetType()`:** This method shows the data type of the array, which will be an array of objects by default.

<!-- end list -->

```powershell
$myArray = 1, 'string', (Get-Process)
$myArray.GetType() # Returns System.Object[]
```

-----

## Iterating Through an Array

You can process each item in an array using a **`foreach`** loop.

```powershell
$files = Get-ChildItem -Path C:\temp
foreach ($file in $files) {
    Write-Host "File name: $($file.Name)"
}
```

Alternatively, you can use the `ForEach-Object` cmdlet, which is particularly useful in a pipeline.

```powershell
Get-ChildItem -Path C:\temp | ForEach-Object {
    Write-Host "File name: $($_.Name)"
}
```
