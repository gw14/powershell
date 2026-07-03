Working with encryption in PowerShell can feel tricky at first, but mastering `SecureString` is a game-changer for keeping passwords, API keys, and sensitive data out of your plain-text scripts.

Here is a comprehensive cheatsheet for handling encryption and `SecureString` in PowerShell.

---

### **1. Creating a SecureString**

A `SecureString` encrypts the text in RAM, preventing it from being read by memory-scraping tools.

* **From User Input (Safest Method):**
Prompts the user to type a password. Characters are masked with asterisks.
```powershell
$SecurePassword = Read-Host -Prompt "Enter your password" -AsSecureString

```


* **From Plain Text (Hardcoded):**
*Note: Avoid hardcoding plain text secrets in scripts whenever possible.*
```powershell
$PlainText = "SuperSecretPassword123!"
$SecurePassword = ConvertTo-SecureString -String $PlainText -AsPlainText -Force

```



### **2. Decrypting SecureString Back to Plain Text**

Because `SecureString` is designed to *stay* secure, PowerShell doesn't have a simple `-AsPlainText` flag to reverse it. You have to use .NET classes to unwrap it.

* **Using the NetworkCredential Class (Cleanest & Most Common):**
```powershell
$BSTR = [System.Net.NetworkCredential]::new("", $SecurePassword)
$PlainText = $BSTR.Password

```


* **Using the Marshal Class (Low-level .NET):**
```powershell
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($SecurePassword)
$PlainText = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
[System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($BSTR) # Cleans up memory

```



### **3. Local Encryption (DPAPI)**

By default, PowerShell uses the Windows Data Protection API (DPAPI). This encrypts the string so that it can **only be decrypted by the same user on the same exact computer**. This is perfect for local automation.

* **Encrypt and Save to Disk:**
```powershell
$SecurePassword | ConvertFrom-SecureString | Out-File "C:\temp\encrypted_secret.txt"

```


* **Read and Decrypt from Disk:**
```powershell
$EncryptedText = Get-Content "C:\temp\encrypted_secret.txt"
$RecoveredSecureString = $EncryptedText | ConvertTo-SecureString

```



### **4. Portable Encryption (AES)**

If you need to share an encrypted file across different computers or run it under different service accounts, you must provide your own AES encryption key. The key must be exactly 16, 24, or 32 bytes (128, 192, or 256-bit encryption).

* **Generate a 32-byte AES Key:**
```powershell
$Key = (1..32 | ForEach-Object { Get-Random -Maximum 256 })

```


* **Encrypt Using the Key:**
```powershell
$EncryptedString = ConvertFrom-SecureString -SecureString $SecurePassword -Key $Key
$EncryptedString | Out-File "C:\temp\portable_secret.txt"

```


* **Decrypt Using the Key:**
```powershell
$EncryptedText = Get-Content "C:\temp\portable_secret.txt"
$RecoveredSecureString = ConvertTo-SecureString -String $EncryptedText -Key $Key

```


> **Security Warning:** You must securely store and transfer the `$Key` array. If an attacker gets both the encrypted text and the key, they can decrypt your secret.



### **5. Working with Credentials (`PSCredential`)**

Many PowerShell cmdlets (like `Invoke-RestMethod`, `Connect-AzAccount`, or Active Directory modules) require a `PSCredential` object rather than just a `SecureString`.

* **Creating a PSCredential Object:**
```powershell
$Username = "AdminUser"
$SecurePassword = Read-Host "Enter Password" -AsSecureString

$Cred = New-Object System.Management.Automation.PSCredential ($Username, $SecurePassword)

```


* **Extracting Data from a PSCredential Object:**
```powershell
$Cred.UserName                  # Returns plain text username
$Cred.Password                  # Returns the SecureString
$Cred.GetNetworkCredential().Password # Returns plain text password

```
