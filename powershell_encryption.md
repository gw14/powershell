Here is a compacted, all-in-one cheatsheet that integrates standard `SecureString` operations with the advanced concepts of AES key generation, type coercion, and strict byte casting we discussed.

---

## **PowerShell SecureString & Encryption Cheatsheet**

### **1. Creating a SecureString**

Encrypts text in RAM to protect against memory scraping.

* **From User Input (Safest):**
```powershell
$SecurePassword = Read-Host -Prompt "Enter password" -AsSecureString

```


* **From Plain Text (Hardcoded):**
```powershell
$SecurePassword = ConvertTo-SecureString -String "Secret123!" -AsPlainText -Force

```



### **2. Decrypting to Plain Text**

Because PowerShell protects `SecureString`, you must use .NET classes to unwrap it.

* **Using NetworkCredential (Recommended):**
```powershell
$PlainText = [System.Net.NetworkCredential]::new("", $SecurePassword).Password

```



### **3. Local Machine Encryption (DPAPI)**

Default method. Tied to the **current user** and **current computer**. Perfect for local automation.

* **Encrypt & Save:**
```powershell
$SecurePassword | ConvertFrom-SecureString | Out-File "C:\temp\local_secret.txt"

```


* **Load & Decrypt:**
```powershell
$RecoveredSecret = Get-Content "C:\temp\local_secret.txt" | ConvertTo-SecureString

```



### **4. Portable Encryption (AES-256)**

Required if you need to move the encrypted file to another machine or use a different service account. Requires a 32-byte key.

**Generating the AES-256 Key:**
By default, `Get-Random` generates an `Int32` (standard integer). PowerShell's *type coercion* automatically converts these integers to bytes during encryption as long as they are under 256. However, for perfectly typed code, you should strictly cast the array as `[byte[]]`.

* **Strictly Typed 32-Byte Key Generation:**
```powershell
# Creates 32 random numbers (0-255) and forces them to be actual Byte data types
[byte[]]$Key = (1..32 | ForEach-Object { Get-Random -Maximum 256 })

```



**Using the Key:**

* **Encrypt (Requires the Key):**
```powershell
$EncryptedText = ConvertFrom-SecureString -SecureString $SecurePassword -Key $Key
$EncryptedText | Out-File "C:\temp\portable_secret.txt"

```


* **Decrypt (Requires the Key):**
```powershell
$EncryptedText = Get-Content "C:\temp\portable_secret.txt"
$RecoveredSecret = ConvertTo-SecureString -String $EncryptedText -Key $Key

```



> **Security Note:** The `$Key` must be stored and transferred securely. If compromised alongside the encrypted file, the secret is exposed.

### **5. Working with PSCredentials**

Used when cmdlets (like `Invoke-RestMethod` or Active Directory commands) require a full credential object.

* **Create the Credential Object:**
```powershell
$Cred = New-Object System.Management.Automation.PSCredential ("AdminUser", $SecurePassword)

```


* **Extract Data from the Object:**
```powershell
$Cred.UserName                        # Plain text username
$Cred.Password                        # SecureString password
$Cred.GetNetworkCredential().Password # Plain text password

```
