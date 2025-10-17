To script **PowerShell** to send an email via **Outlook** using the **COM Object** with **HTML content**, you need to follow these steps:

## Prerequisites

1.  **Microsoft Outlook must be installed** on the machine running the script.
2.  The script needs to be run as a user who has an active **Outlook profile** configured.
3.  **PowerShell execution policy** should allow running local scripts (e.g., `RemoteSigned`).

-----

## PowerShell Script with HTML Body

The following script block creates an Outlook application object, generates a new mail item, populates its properties, sets the body format to HTML, and sends the email.

```powershell
# 1. Define Email Parameters
$EmailTo = "recipient@example.com"
$EmailSubject = "PowerShell Outlook HTML Test"
$HTMLBody = @"
<html>
<head>
    <title>PowerShell Email</title>
</head>
<body>
    <h1>Hello from PowerShell!</h1>
    <p>This is a test email sent using the **Outlook COM Object** with **HTML content**.</p>
    <ul>
        <li>Item 1: <span style="color: blue;">Blue Text</span></li>
        <li>Item 2: <span style="font-weight: bold;">Bold Text</span></li>
    </ul>
    <p>Best regards,</p>
    <p>The Script</p>
</body>
</html>
"@
$AttachmentPath = "C:\Path\To\Your\File.txt" # Optional: Specify a file path for an attachment

# 2. Create the Outlook COM Object
try {
    # Attempt to get an existing Outlook instance or create a new one
    $Outlook = [Runtime.Interopservices.Marshal]::GetActiveObject('Outlook.Application')
}
catch {
    # If no active instance, create a new one
    $Outlook = New-Object -ComObject Outlook.Application
}

# 3. Create a new Mail Item (olMailItem = 0)
$Mail = $Outlook.CreateItem(0)

# 4. Configure Mail Item Properties
$Mail.To = $EmailTo
# $Mail.CC = "ccuser@example.com"  # Uncomment to add CC
# $Mail.BCC = "bccuser@example.com" # Uncomment to add BCC
$Mail.Subject = $EmailSubject

# 5. Set the Body Format to HTML and assign the content
# olFormatHTML = 2 (This property isn't directly set on the item, 
# but setting the HTMLBody automatically handles the format.)
$Mail.BodyFormat = 2 # 2 represents olFormatHTML, though assigning HTMLBody usually suffices

# Set the HTML Content
$Mail.HTMLBody = $HTMLBody

# 6. Optional: Add an Attachment
if (Test-Path $AttachmentPath) {
    $Mail.Attachments.Add($AttachmentPath)
}

# 7. Send the Email
# Uncomment the line below to SEND the email immediately
$Mail.Send()

# OR Uncomment the line below to DISPLAY the email before sending (for review)
# $Mail.Display()

# 8. Clean up COM Objects
# Best practice to release COM objects, especially in larger scripts or loops
[System.Runtime.InteropServices.Marshal]::ReleaseComObject($Mail) | Out-Null
[System.Runtime.InteropServices.Marshal]::ReleaseComObject($Outlook) | Out-Null
Remove-Variable Mail, Outlook -ErrorAction SilentlyContinue

Write-Host "Email script executed. Check Outlook for status."
```

-----

## Key Steps Explained

1.  **Define Variables**: Set your recipient, subject, and the complete HTML string for the body. Use a **here-string** (`@"..."@`) for multi-line HTML content.
2.  **Create/Get COM Object**:
      * `[Runtime.Interopservices.Marshal]::GetActiveObject('Outlook.Application')`: Attempts to connect to an already running instance of Outlook, which is often faster and avoids launching a new process if one exists.
      * `New-Object -ComObject Outlook.Application`: Creates a new instance if no active one is found.
3.  **Create Mail Item**: `$Outlook.CreateItem(0)` creates a new mail item object. The constant `0` corresponds to the Outlook enumeration value `olMailItem`.
4.  **Set Properties**: Standard properties like `.To`, `.Subject`, `.CC`, and `.BCC` are set.
5.  **Set HTML Body**:
      * Set the `.BodyFormat` to `2` (which is `olFormatHTML`) to ensure Outlook processes the body correctly.
      * Set the **`.HTMLBody`** property to your HTML string variable (`$HTMLBody`). This is the crucial step for sending HTML content. **Do not use the `.Body` property**, as that is for plain text.
6.  **Attachments (Optional)**: `$Mail.Attachments.Add($AttachmentPath)` adds a file. The `Test-Path` check ensures the script doesn't error if the file is missing.
7.  **Send or Display**:
      * **`$Mail.Send()`**: Sends the email immediately via the default Outlook account.
      * **`$Mail.Display()`**: Opens the draft email window for review, allowing the user to manually click "Send."
8.  **Cleanup**: Releasing the COM objects using `[System.Runtime.InteropServices.Marshal]::ReleaseComObject()` is important to prevent memory leaks and ensure the Outlook process terminates correctly if it was launched by the script.
