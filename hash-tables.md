A **PowerShell Hash Table** is a compact data structure used to store one or more key-value pairs. They are also known as **dictionaries** or **associative arrays** in other programming languages.

Here's how to create and work with PowerShell hash tables:

## Creating a Hash Table

Hash tables are enclosed in **@{ }** and use a **semicolon (;) or newline** to separate key-value pairs, and an **equals sign (=)** to separate the key from its value.

### Simple Creation

```powershell
$MyHashTable = @{
    Name   = "Alice"
    Age    = 30
    City   = "New York"
}
```

### Type and Count

You can check the type and the number of entries:

```powershell
$MyHashTable.GetType().Name  # Output: Hashtable
$MyHashTable.Count           # Output: 3
```

-----

## Accessing Values

You can retrieve values using their associated key, similar to how you access properties of an object.

### By Key Name (Dot Notation)

```powershell
$MyHashTable.Name   # Output: Alice
$MyHashTable.City   # Output: New York
```

### By Key Name (Array Notation)

This is useful when keys contain spaces or special characters (though generally best to avoid those in keys).

```powershell
$MyHashTable["Age"] # Output: 30
```

-----

## Modifying Hash Tables

### Adding New Key-Value Pairs

You can add a new pair using the same notation used for accessing a value:

```powershell
$MyHashTable.Country = "USA"

# Or
$MyHashTable["PostalCode"] = 10001
```

### Updating Existing Values

If a key already exists, assigning a new value will update the existing one:

```powershell
$MyHashTable.Age = 31 # Age is now 31
```

-----

## Iterating Over a Hash Table

You can use a `foreach` loop to process the keys and values.

### Iterating over Keys and Values

You can use the **`GetEnumerator()`** method, which returns a **DictionaryEntry** object for each item.

```powershell
foreach ($entry in $MyHashTable.GetEnumerator()) {
    Write-Host "Key: $($entry.Key), Value: $($entry.Value)"
}
# Output:
# Key: Name, Value: Alice
# Key: Age, Value: 31
# ...
```

### Iterating over Keys Only

```powershell
foreach ($key in $MyHashTable.Keys) {
    Write-Host "Key: $key"
}
```

### Iterating over Values Only

```powershell
foreach ($value in $MyHashTable.Values) {
    Write-Host "Value: $value"
}
```

-----

## Removing Entries

Use the **`Remove()`** method and provide the key you want to delete.

```powershell
$MyHashTable.Remove("PostalCode")

# Check if it was removed
$MyHashTable.Count # Output: 4 (if it started at 5)
```

-----

## Ordered Hash Tables

By default, the order in which you define or add elements to a hash table is *not* preserved. If the **order of insertion is important**, you must use an **Ordered Hash Table** by casting the structure as `[ordered]`.

```powershell
$MyOrderedTable = [ordered]@{
    First = 1
    Second = 2
    Third = 3
}

# When displayed, the output will be in the order they were defined.
$MyOrderedTable
```
