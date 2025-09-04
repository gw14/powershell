Working with **Invoke-WebRequest** in PowerShell to connect to an HTTPS site is straightforward, but it requires addressing a few key aspects, primarily related to security and authentication. The command sends HTTP/HTTPS requests to a web page or web service and retrieves a response that includes the content, headers, and status code.

### Basic Usage

To make a simple GET request to an HTTPS site, you can use the following basic syntax:

```powershell
Invoke-WebRequest -Uri "https://www.example.com"
```

This command will send a GET request to the specified URL. The output is an object that contains various properties of the response, such as `Content`, `StatusCode`, `Headers`, and `Links`.

### Handling SSL/TLS

When connecting to an HTTPS site, you're relying on a secure connection using SSL (Secure Sockets Layer) or its successor, TLS (Transport Layer Security). PowerShell, by default, is configured to trust standard, valid certificates. However, you might encounter issues if:

  * The site uses a **self-signed certificate**.
  * The certificate has expired.
  * The certificate's common name doesn't match the URL.
  * The certificate chain is incomplete or untrusted by your system.

To bypass these certificate validation errors for testing or internal network use (though it's **not recommended for production or public-facing sites**), you can use the `-SkipCertificateCheck` parameter.

```powershell
Invoke-WebRequest -Uri "https://self-signed-site.local" -SkipCertificateCheck
```

-----

### Using Headers and Cookies

Many APIs and web services require custom headers for authentication, such as an **API key** or a **bearer token**. You can specify these using the `-Headers` parameter, which accepts a hash table.

```powershell
$headers = @{
    "Authorization" = "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9"
    "Content-Type"  = "application/json"
}

Invoke-WebRequest -Uri "https://api.example.com/data" -Headers $headers
```

For sites that use **cookies** to manage sessions, `Invoke-WebRequest` can automatically handle them. It has a built-in session management feature. For instance, to log in and maintain a session, you can create a session variable and use it for subsequent requests.

```powershell
$session = New-PSSession
$loginUri = "https://www.example.com/login"
$loginData = @{
    "username" = "myuser"
    "password" = "mypassword"
}

Invoke-WebRequest -Uri $loginUri -Method Post -Body $loginData -Session $session

# Now, subsequent requests will use the same session and cookies
Invoke-WebRequest -Uri "https://www.example.com/dashboard" -Session $session
```

-----

### Sending Data (POST, PUT, etc.)

While the default method is GET, you can send data to an HTTPS site using other HTTP verbs like POST, PUT, and DELETE. This is common when submitting forms or interacting with RESTful APIs.

Use the `-Method` parameter to specify the verb and the `-Body` parameter to provide the data. The data can be a string, a hash table (which will be automatically converted to form data), or a file path.

**Example: Submitting form data**

```powershell
$formData = @{
    "name"  = "John Doe"
    "email" = "john.doe@example.com"
}

Invoke-WebRequest -Uri "https://www.example.com/submit-form" -Method Post -Body $formData
```

**Example: Sending JSON data**

For APIs that require a JSON payload, you need to convert your data to a JSON string and specify the `Content-Type` header.

```powershell
$jsonData = @{
    "userId"   = 123
    "userName" = "Jane Doe"
}

$jsonBody = ConvertTo-Json $jsonData
$headers = @{ "Content-Type" = "application/json" }

Invoke-WebRequest -Uri "https://api.example.com/users" -Method Post -Body $jsonBody -Headers $headers
```

Using `Invoke-WebRequest` to log in with a user and password often involves one of two main authentication methods: **Basic Authentication** or **Forms-Based Authentication**. The method you use depends entirely on how the website or API handles login requests.

### Basic Authentication

**Basic Authentication** is a simple, standard way to pass credentials. It involves encoding the username and password (separated by a colon) in a Base64 string and including it in a header named `Authorization`. This is commonly used with REST APIs.

  * **Step 1: Create a `PSCredential` object.** This is a secure way to store credentials, preventing the password from being in plain text in your script. You can use `Get-Credential` to prompt the user for their username and password.

    ```powershell
    $cred = Get-Credential
    ```

  * **Step 2: Use the `-Credential` parameter.** `Invoke-WebRequest` can take a `PSCredential` object directly and will handle the Base64 encoding and header creation for you.

    ```powershell
    Invoke-WebRequest -Uri "https://api.example.com/protected-resource" -Credential $cred -Authentication Basic
    ```

    Using the `-Credential` parameter with `-Authentication Basic` is the most secure and recommended approach for this type of authentication.

-----

### Forms-Based Authentication

**Forms-Based Authentication** is what you see when you visit a website with a login page. You enter your username and password into a form, and the browser sends that data as a POST request to a specific URL (the form's "action" URL).

  * **Step 1: Get the login form.** Make an initial request to the login page to retrieve the HTML content, including the login form's details. It's best to create a web session to maintain cookies between requests.

    ```powershell
    $loginPage = Invoke-WebRequest -Uri "https://www.example.com/login" -SessionVariable mySession
    ```

  * **Step 2: Find the form and its fields.** Use the `-Form` and `-Fields` properties of the `Invoke-WebRequest` output object to locate and inspect the login form. You'll need to know the names of the username and password input fields.

    ```powershell
    $form = $loginPage.Forms[0]
    # Example: Check the field names
    # $form.Fields.Keys
    ```

  * **Step 3: Populate the form with credentials.** Assign your username and password to the correct fields in the form object.

    ```powershell
    $form.Fields["username"] = "myuser"
    $form.Fields["password"] = "mypassword"
    ```

  * **Step 4: Submit the form.** Send a POST request to the form's action URL with the populated form data. This request will use the same session variable to ensure the cookies are passed along.

    ```powershell
    Invoke-WebRequest -Uri ("https://www.example.com" + $form.Action) -WebSession $mySession -Method Post -Body $form.Fields
    ```

After this POST request is successful, the `$mySession` variable will contain the necessary cookies to access authenticated parts of the website in subsequent `Invoke-WebRequest` commands.
