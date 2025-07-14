PowerShell offers flexible ways to create custom objects and, since PowerShell 5.0, full-fledged classes. Both are incredibly useful for structuring data and making your scripts more readable, maintainable, and reusable.

## Custom Objects ([PSCustomObject])

Custom objects are lightweight and widely used for returning structured data from functions and scripts. They are essentially collections of properties with associated values.

### 1\. Using a Hash Table (Recommended for simplicity)

This is the most common and often preferred method for creating simple custom objects.

```powershell
# Create a custom object directly from a hash table
$myObject = [PSCustomObject]@{
    Name       = "John Doe"
    Age        = 30
    City       = "New York"
    IsActive   = $true
    CreatedDate = Get-Date
}

# Access properties
Write-Host "Name: $($myObject.Name)"
Write-Host "Age: $($myObject.Age)"
Write-Host "City: $($myObject.City)"

# You can also add or modify properties after creation
$myObject | Add-Member -MemberType NoteProperty -Name "Email" -Value "john.doe@example.com"
$myObject.Age = 31

# View the object
$myObject | Format-List
```

**Explanation:**

  * `[PSCustomObject]@{ ... }`: This is a type accelerator that casts a hash table into a `PSCustomObject`.
  * The hash table defines the properties (keys) and their initial values.
  * Properties can be accessed using dot notation (`$myObject.Name`).
  * `Add-Member` can be used to add new properties or methods to an existing `PSCustomObject`.

### 2\. Using `New-Object PSObject` and `Add-Member` (Older method, still valid)

This method is more verbose but provides more granular control, especially for adding different member types (e.g., ScriptProperty, ScriptMethod) later.

```powershell
# Create an empty PSObject
$myObject = New-Object -TypeName PSObject

# Add properties using Add-Member
$myObject | Add-Member -MemberType NoteProperty -Name "Name" -Value "Jane Smith"
$myObject | Add-Member -MemberType NoteProperty -Name "Occupation" -Value "Engineer"
$myObject | Add-Member -MemberType NoteProperty -Name "YearsExperience" -Value 5

# Add a ScriptMethod
$myObject | Add-Member -MemberType ScriptMethod -Name "Introduce" -Value {
    Write-Host "Hello, my name is $($this.Name) and I am an $($this.Occupation)."
}

# Access properties and methods
Write-Host $myObject.Occupation
$myObject.Introduce()

# You can also specify an ordered hash table with New-Object for property order
$orderedProperties = [ordered]@{
    FirstName = "Alice"
    LastName  = "Wonderland"
    ID        = 12345
}
$person = New-Object -TypeName PSObject -Property $orderedProperties
$person | Format-List
```

**Explanation:**

  * `New-Object -TypeName PSObject`: Creates a generic PowerShell object.
  * `Add-Member`: This cmdlet is used to add various types of members (like `NoteProperty` for simple properties or `ScriptMethod` for methods defined by a script block) to the object.
  * `$this` inside a `ScriptMethod` refers to the current object instance.

## Classes

PowerShell classes, introduced in PowerShell 5.0, provide a more structured and object-oriented approach to defining custom types. They allow for strong typing, constructors, methods, and inheritance, similar to other object-oriented languages.

### Basic Class Definition

```powershell
class Person {
    # Properties
    [string]$FirstName
    [string]$LastName
    [int]$Age
    [datetime]$DateOfBirth

    # Constructor (optional - default constructor is created automatically if none defined)
    Person([string]$firstName, [string]$lastName, [int]$age, [datetime]$dateOfBirth) {
        $this.FirstName = $firstName
        $this.LastName = $lastName
        $this.Age = $age
        $this.DateOfBirth = $dateOfBirth
    }

    # Method
    [string]SayHello() {
        return "Hello, my name is $($this.FirstName) $($this.LastName)."
    }

    # Another Method
    [void]CalculateAge() {
        $today = Get-Date
        $dob = $this.DateOfBirth
        $ageYears = $today.Year - $dob.Year
        if ($dob.AddYears($ageYears) -gt $today) {
            $ageYears--
        }
        $this.Age = $ageYears
    }
}

# Create an instance of the class using the constructor
$person1 = [Person]::new("Alice", "Johnson", 30, (Get-Date).AddYears(-30))

# Access properties
Write-Host "Full Name: $($person1.FirstName) $($person1.LastName)"
Write-Host "Age: $($person1.Age)"

# Call a method
$person1.SayHello()

# Update a property and call a method to recalculate age
$person1.DateOfBirth = (Get-Date).AddYears(-31)
$person1.CalculateAge()
Write-Host "New Age: $($person1.Age)"

# Create another instance with a different constructor if overloaded
# (Not shown here, but you can have multiple constructors with different parameters)
```

**Explanation:**

  * `class Person { ... }`: Defines a class named `Person`.
  * **Properties:** Declared with a type (`[string]`, `[int]`, `[datetime]`) followed by the property name. You can also set default values.
  * **Constructor:** A method with the same name as the class (`Person(...)`). It's used to initialize the object when it's created. `$this` refers to the current instance of the class.
  * **Methods:** Functions defined within the class. They can take parameters and return values (or `[void]` if they don't return anything).
  * **Instantiation:**
      * `[Person]::new(...)`: The preferred way to create a new instance of a class, using the static `new()` method and passing arguments to a constructor.
      * You can also use `New-Object -TypeName Person -ArgumentList ...` (though `::new()` is generally faster).

### Key Differences and When to Use Which:

| Feature           | `[PSCustomObject]`                                   | Class                                                                  |
| :---------------- | :--------------------------------------------------- | :--------------------------------------------------------------------- |
| **Purpose** | Simple data structuring, quick ad-hoc objects.       | Defining reusable, strongly-typed data structures with behavior.       |
| **Type** | Always `PSCustomObject` (base type `System.Object`). | A unique, user-defined type (e.g., `Person`).                          |
| **Properties** | Dynamically added, no explicit type declaration.     | Strongly typed, defined in the class blueprint.                        |
| **Methods** | Can be added dynamically using `Add-Member`.         | Defined directly within the class, part of the class blueprint.        |
| **Constructors** | Not applicable (initialization via hash table).      | Explicitly defined to control object creation and initial state.       |
| **Inheritance** | Not supported directly.                              | Fully supported, allowing for code reuse and hierarchical structures. |
| **Performance** | Generally good for small-scale use.                  | Can offer better performance for complex object manipulation.          |
| **Code Structure**| Less formal, more script-like.                       | More formal, object-oriented programming (OOP) structure.              |
| **Maintainability**| Good for simple scenarios, can become complex.       | Excellent for large, complex projects, promoting modularity.           |

**When to use `[PSCustomObject]`:**

  * When you need to return structured data from a script or function for simple display or piping.
  * For ad-hoc data structures that don't require complex behavior or strict typing.
  * When you're working with older PowerShell versions (prior to 5.0).

**When to use Classes:**

  * When you need to define a clear blueprint for objects with specific properties and behaviors.
  * For creating complex data models where strong typing is beneficial for data integrity and auto-completion.
  * When you want to leverage object-oriented principles like encapsulation, inheritance, and polymorphism.
  * For building reusable components and modules.

By understanding these options, you can choose the best approach for structuring your data and logic in PowerShell.
