Managing certificates in PowerShell is incredibly efficient because Windows maps your certificate stores directly to a virtual drive: `Cert:\`.

Here is a breakdown of the essential commands for handling certificates, organized by task.

---

## 1. Navigating and Viewing Certificates

Because the certificate store acts like a file system, you can use standard navigation commands like `cd`, `dir`, and `Get-ChildItem`.

### List All Certificate Stores

To see the top-level stores (CurrentUser vs. LocalMachine):

```powershell
Get-ChildItem -Path Cert:\

```

### View Certificates in a Specific Store

To see certificates in your personal store under the current user:

```powershell
Get-ChildItem -Path Cert:\CurrentUser\My

```

### Find an Expiring Certificate

To find certificates expiring within the next 30 days:

```powershell
Get-ChildItem -Path Cert:\CurrentUser\My | 
    Where-Object { $_.NotAfter -le (Get-Date).AddDays(30) }

```

---

## 2. Searching for Specific Certificates

Instead of scrolling through lists, you can filter by Thumbprint, Subject, or EKU (Enhanced Key Usage).

### Find by Thumbprint

```powershell
Get-ChildItem -Path Cert:\LocalMachine\My\A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6Q7R8S9T0

```

### Find by Subject Name (Wildcard Search)

```powershell
Get-ChildItem -Path Cert:\LocalMachine\My -Recurse | 
    Where-Object { $_.Subject -like "*contoso*" }

```

---

## 3. Exporting Certificates

When exporting, you need to decide if you are exporting just the public key (`.cer`) or the private key as well (`.pfx`).

### Export Public Key (.cer)

```powershell
$cert = Get-ChildItem -Path Cert:\CurrentUser\My\A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6Q7R8S9T0
Export-Certificate -Cert $cert -FilePath "C:\Certs\public_key.cer"

```

### Export Private Key (.pfx)

> **Note:** Exporting a PFX requires a secure password.

```powershell
$cert = Get-ChildItem -Path Cert:\CurrentUser\My\A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6Q7R8S9T0
$password = ConvertTo-SecureString -String "YourSecurePassword123!" -Force -AsPlainText

Export-PfxCertificate -Cert $cert -FilePath "C:\Certs\private_key.pfx" -Password $password

```

---

## 4. Importing Certificates

### Import a Public Key (.cer)

To import a root certificate into the Local Machine's Trusted Root store (requires Admin privileges):

```powershell
Import-Certificate -FilePath "C:\Certs\root_ca.cer" -CertStoreLocation Cert:\LocalMachine\Root

```

### Import a Private Key (.pfx)

```powershell
$password = ConvertTo-SecureString -String "YourSecurePassword123!" -Force -AsPlainText

Import-PfxCertificate -FilePath "C:\Certs\private_key.pfx" -CertStoreLocation Cert:\CurrentUser\My -Password $password

```

---

## 5. Creating Self-Signed Certificates

PowerShell makes it incredibly easy to spin up self-signed certificates for testing or internal IIS sites.

### Create a Basic Self-Signed Certificate

```powershell
New-SelfSignedCertificate -DnsName "dev.local" -CertStoreLocation "Cert:\LocalMachine\My"

```

### Create an SSL Server Certificate with Custom Expiration

```powershell
New-SelfSignedCertificate -DnsName "api.contoso.local" `
                          -CertStoreLocation "Cert:\LocalMachine\My" `
                          -NotAfter (Get-Date).AddYears(2) `
                          -Type SSLServerAuthentication

```

---

## 6. Deleting Certificates

> ⚠️ **Warning:** Be very careful when deleting certificates, especially in `LocalMachine\Root`. Removing the wrong one can break applications or OS stability.

### Remove a Specific Certificate

```powershell
Remove-Item -Path Cert:\CurrentUser\My\A1B2C3D4E5F6G7H8I9J0K1L2M3N4O5P6Q7R8S9T0

```

### Remove All Expired Certificates (Personal Store)

```powershell
Get-ChildItem -Path Cert:\CurrentUser\My | 
    Where-Object { $_.NotAfter -lt (Get-Date) } | 
    Remove-Item

```

---

## Quick Reference Table

| Action | Cmdlet |
| --- | --- |
| **View/Find** | `Get-ChildItem` (or `dir`) |
| **Create** | `New-SelfSignedCertificate` |
| **Export Public** | `Export-Certificate` |
| **Export Private** | `Export-PfxCertificate` |
| **Import Public** | `Import-Certificate` |
| **Import Private** | `Import-PfxCertificate` |
| **Delete** | `Remove-Item` (or `del`) |

---

To view the details of a `.crt` (or `.cer`) certificate file in PowerShell, you can use the `System.Security.Cryptography.X509Certificates.X509Certificate2` .NET class.

Since the certificate is just a file on your disk and not yet imported into the Windows certificate store, standard `Get-ChildItem` won't show its internal details.

Here are the best ways to view those details, depending on how much information you need.

---

### 1. The Quick Summary (One-Liner)

To quickly see the thumbprint, subject, and expiration date, instantiate the object directly:

```powershell
[System.Security.Cryptography.X509Certificates.X509Certificate2]::new("C:\path\to\your_certificate.crt")

```

---

### 2. View All Properties (Detailed List)

To see *everything* inside the certificate—including the issuer, serial number, signature algorithm, and version—pipe the object into `Format-List *`:

```powershell
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]::new("C:\path\to\your_certificate.crt")
$cert | Format-List *

```

---

### 3. Cherry-Picking Specific Details

If you are writing a script and only need specific properties (like checking if a certificate is expired), you can access them as properties of the object:

```powershell
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]::new("C:\path\to\your_certificate.crt")

# Output specific pieces of data
[PSCustomObject]@{
    Subject    = $cert.Subject
    Issuer     = $cert.Issuer
    ValidFrom  = $cert.NotBefore
    ValidTo    = $cert.NotAfter
    Thumbprint = $cert.Thumbprint
    Expired    = (Get-Date) -gt $cert.NotAfter
}

```

---

### 4. Extensions and SANs (Subject Alternative Names)

If you need to view advanced extensions, such as the **Subject Alternative Names (SAN)** or **Enhanced Key Usage (EKU)**, you can drill down into the `Extensions` property:

```powershell
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]::new("C:\path\to\your_certificate.crt")

# Loop through extensions and print their decoded text
foreach ($ext in $cert.Extensions) {
    [PSCustomObject]@{
        Name  = $ext.Oid.FriendlyName
        Value = $ext.Format($true) # $true enables multi-line formatting
    }
}

```

---

### 💡 Bonus: The GUI Way (From PowerShell)

If you just want Windows to pop up the classic, user-friendly certificate viewer window from your PowerShell session, use `Format-Hex` or an invocation trick via the crypto shell extensions:

```powershell
Start-Process "C:\path\to\your_certificate.crt"

```

*(As long as `.crt` files are associated with the Windows Crypto Shell Extensions, this will open the native Windows GUI viewer).*
