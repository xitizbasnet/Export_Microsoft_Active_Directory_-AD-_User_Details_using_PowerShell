# Export Microsoft Active Directory (AD) User Details Using PowerShell

📅 **Date:** Dec 02, 2024

---

This guide outlines the steps to export all users from Microsoft Active Directory (AD) using PowerShell. It assumes that you are working on a Windows machine with access to the Active Directory environment and have the necessary permissions.

---

## Prerequisites

- 🛠️ **Active Directory Module for Windows PowerShell** must be installed.
- 🔑 You need the necessary permissions to read user information from Active Directory.
- 💻 **PowerShell must be run as an Administrator.**

---

## Chapter 1: Step-by-Step Process to Export All AD Users

---

### Step 1: Open PowerShell as an Administrator

1. Press `Windows + X` and choose **Windows PowerShell (Admin)** from the menu.
2. Alternatively, search for **PowerShell** in the Start menu, right-click, and select **Run as Administrator**.

---

### Step 2: Import the Active Directory Module

The Active Directory module might not be loaded by default. If it's not available, you can import it.

```powershell
Import-Module ActiveDirectory
```

---

### Step 3: Export All Active Directory Users to a CSV File

Use the `Get-ADUser` cmdlet to retrieve all users from Active Directory and export them to a CSV file.

#### Basic Command to Export All Users:

```powershell
Get-ADUser -Filter * -Property * | Select-Object SamAccountName, Name, GivenName, Surname, EmailAddress, Department, Title, Enabled | Export-Csv -Path "C:\AD_Users.csv" -NoTypeInformation
```

#### Explanation of the Command:

- `Get-ADUser -Filter *`: Fetches all user accounts in Active Directory (no filter applied).
- `-Property *`: Retrieves all available properties of the users (e.g., email, department).
- `Select-Object`: Chooses specific attributes to export (modify according to your needs).
- `Export-Csv`: Saves the result to a CSV file.
- `-Path "C:\AD_Users.csv"`: Specifies the file path for the CSV.
- `-NoTypeInformation`: Prevents additional type information from being added to the CSV.

#### Running the Command:
After executing the command, the system will process all the users and export their details to the file you specified. You should find a new `AD_Users.csv` file at the specified path.

---

### Step 4: Open the CSV File

Navigate to the directory where you saved the CSV file (e.g., `C:\AD_Users.csv`) and open it with Excel or any text editor to view the exported data.

---

## Chapter 2: Customizing the Export

---

### Export Additional Fields

If you need more details, you can modify the export to include additional properties.

```powershell
Get-ADUser -Filter * -Property * | Select-Object SamAccountName, Name, GivenName, Surname, EmailAddress, Department, Title, Enabled, Manager, LastLogonDate | Export-Csv -Path "C:\AD_Users.csv" -NoTypeInformation
```

This command includes **Manager** and **LastLogonDate** fields in the export.

---

### Export Specific Users or Organizational Units (OUs)

To export users from a specific Organizational Unit (OU), use the `-SearchBase` parameter:

```powershell
Get-ADUser -Filter * -Property * -SearchBase "OU=Employees,DC=example,DC=com" | Select-Object SamAccountName, Name, GivenName, Surname, EmailAddress | Export-Csv -Path "C:\AD_Users.csv" -NoTypeInformation
```

Replace the `OU=Employees,DC=example,DC=com` with the DN of your target OU.

---

### Filter by Specific Criteria

For example, you can export only active users with this filter:

```powershell
Get-ADUser -Filter {Enabled -eq $true} -Property * | Select-Object SamAccountName, Name, GivenName, Surname, EmailAddress, Department | Export-Csv -Path "C:\Active_Users.csv" -NoTypeInformation
```

---

## Chapter 3: Common Issues

---

### Module Not Found

If the **ActiveDirectory** module isn't found, you may need to install the **Remote Server Administration Tools (RSAT)** for Active Directory. You can download RSAT from Microsoft's website or install it via **Windows Settings**.

---

### Insufficient Permissions

Ensure that you have the necessary permissions to read user data from Active Directory. You'll need read access to the user attributes you wish to export.

---

## Conclusion

---

This method provides a simple and efficient way to export Active Directory user details using PowerShell. The exported CSV file can be customized with additional fields, filtered by specific criteria, or limited to particular Organizational Units (OUs). Once exported, the CSV file can be opened in Excel or any text editor for further analysis.

---

## Master Final Code

---

### Corrected PowerShell Script:

```powershell
Get-ADUser -Filter * -Property * |  
Select-Object SamAccountName, Name, GivenName, Surname, EmailAddress, Department, Title, Enabled,  
              @{Name="OUPath";Expression={ 
                  $ouParts = ($_.DistinguishedName -split ",") 
                  $ouComponents = $ouParts | Where-Object {$_ -like "OU=*"} 
                  $ouPath = $ouComponents -join " > " 
                  ($_.'SamAccountName') + " > " + $ouPath
              }} | 
Export-Csv -Path "C:\AD_Users.csv" -NoTypeInformation
```

---

### Key Adjustments:

- **Username First:** The username (`SamAccountName`) is now placed at the beginning of the **OUPath**.
- **OUs in Top-to-Bottom Order:** Organizational Units (OU) are listed from top to bottom in the **OUPath** using the `>` separator.

---

### Section Breaks & Icons

To enhance the look and feel of the documentation, you can use various Markdown features like section breaks (`---`), headings (`#` for titles, `##` for sub-headings), and inline code blocks (backticks). Consider adding icons or images for better visual appeal, as demonstrated in the document. Icons and images can be added using Markdown image syntax, such as:

```markdown
![Description of Image](url_to_image)
```

---
