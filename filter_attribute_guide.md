The **`-Filter`** parameter for `Get-ADGroup` uses a **PowerShell Expression Language** syntax to query and filter Active Directory objects. This is a key distinction from other types of filtering, such as the LDAP filter syntax. Using `-Filter` is generally preferred for its simplicity and improved performance, as the filtering is done server-side on the domain controller.

***

### Syntax and Operators

The basic syntax for a filter is `"<Property> <Operator> '<Value>'"`. The entire expression is enclosed in quotes, and the value you're comparing against is enclosed in single quotes.

* `Get-ADGroup -Filter "Name -eq 'Domain Admins'"`

You can chain multiple conditions together using logical operators.

* `Get-ADGroup -Filter "Name -eq 'IT Support' -or Name -eq 'Helpdesk'"`

Here's a list of common operators used with the `-Filter` parameter:

* **Comparison Operators:**
    * `-eq` (Equal to)
    * `-ne` (Not equal to)
    * `-gt` (Greater than)
    * `-ge` (Greater than or equal to)
    * `-lt` (Less than)
    * `-le` (Less than or equal to)
    * `-like` (Wildcard match)
    * `-notlike` (Wildcard mismatch)

* **Logical Operators:**
    * `-and` (Logical AND)
    * `-or` (Logical OR)
    * `-not` (Logical NOT)

### Examples

* **Find a group by a specific name:**
    * `Get-ADGroup -Filter "Name -eq 'Domain Admins'"`

* **Find all groups with a name containing a specific string (using a wildcard):**
    * `Get-ADGroup -Filter "Name -like '*Admins*'" `

* **Combine conditions to find groups based on name and scope:**
    * `Get-ADGroup -Filter "Name -like '*IT*' -and GroupScope -eq 'Global'"`

* **Find all groups that are not of a specific type:**
    * `Get-ADGroup -Filter "GroupCategory -ne 'Distribution'"`

This video provides a great walkthrough of how to find Active Directory user group memberships using PowerShell.

[Get AD User Group List from Membership Tab with PowerShell – The Server Room #059](https://www.youtube.com/watch?v=9NJ9henhByo)
http://googleusercontent.com/youtube_content/0
