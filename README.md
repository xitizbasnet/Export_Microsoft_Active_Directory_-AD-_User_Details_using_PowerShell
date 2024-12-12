# Export Microsoft Active Directory (AD) User Details Using PowerShell

📅 **Date:** Dec 02, 2024

---

This guide explains how to export all users from Microsoft Active Directory (AD) using PowerShell. The steps assume that you are working on a Windows machine with access to the Active Directory environment and the necessary permissions.

---

## Prerequisites

- **Active Directory Module for Windows PowerShell** must be installed.
- You need the necessary permissions to read user information from Active Directory.
- **PowerShell** must be run as an Administrator.

---

## Chapter 1: Step-by-Step Process to Export All AD Users

### Step 1: Open PowerShell as an Administrator

1. Press `Windows + X` and choose **Windows PowerShell (Admin)** from the menu.
2. Alternatively, search for **PowerShell** in the Start menu, right-click it, and select **Run as Administrator**.

---

### Step 2: Import the Active Directory Module

The Active Directory module may not always be loaded by default, so you'll need to import it if it's not available.

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

- **`Get-ADUser -Filter *`**: Fetches all user accounts in Active Directory (no filter applied).
- **`-Property *`**: Retrieves all available properties of the users (e.g., email, department, etc.).
- **`Select-Object`**: Chooses specific attributes of the user to export. You can modify this part based on the attributes you need.
  - Common attributes include:
    - `SamAccountName` (User login name)
    - `Name` (Full Name)
    - `GivenName` (First Name)
    - `Surname` (Last Name)
    - `EmailAddress`
    - `Department`
    - `Title`
    - `Enabled` (Status: Active/Disabled)
- **`Export-Csv`**: Saves the result to a CSV file.
- **`-Path "C:\AD_Users.csv"`**: Specifies the path where the CSV will be saved (you can change this path to suit your needs).
- **`-NoTypeInformation`**: Prevents the extra type information from being included in the CSV file.

#### Running the Command:

After running the command, the system will process all the users and export the details to the file you specified (e.g., `C:\AD_Users.csv`). If successful, you should see a new CSV file with the user details saved at the chosen path.

---

### Step 4: Open the CSV File

Navigate to the directory where you saved the CSV file (e.g., `C:\AD_Users.csv`). Open the CSV file using a program like Excel or Notepad to view the user details.

---

## Chapter 2: Customizing the Export

### Export Additional Fields

If you want to include more information, you can add additional properties to the `Select-Object` portion of the command. For example:

```powershell
Get-ADUser -Filter * -Property * | Select-Object SamAccountName, Name, GivenName, Surname, EmailAddress, Department, Title, Enabled, Manager, LastLogonDate | Export-Csv -Path "C:\AD_Users.csv" -NoTypeInformation
```

This will include the **Manager** and **LastLogonDate** fields.

---

### Export Specific Users or Organizational Units (OUs)

To filter users in a specific Organizational Unit (OU), use the `-SearchBase` parameter:

```powershell
Get-ADUser -Filter * -Property * -SearchBase "OU=Employees,DC=example,DC=com" | Select-Object SamAccountName, Name, GivenName, Surname, EmailAddress | Export-Csv -Path "C:\AD_Users.csv" -NoTypeInformation
```

Replace `OU=Employees,DC=example,DC=com` with the appropriate distinguished name (DN) of your target OU.

---

### Filter by Specific Criteria

You can also use filters to export only users that meet specific criteria. For example, exporting only active users:

```powershell
Get-ADUser -Filter {Enabled -eq $true} -Property * | Select-Object SamAccountName, Name, GivenName, Surname, EmailAddress, Department | Export-Csv -Path "C:\Active_Users.csv" -NoTypeInformation
```

---

## Chapter 3: Common Issues

### Module Not Found

If the **ActiveDirectory** module isn't installed, you may need to install the **RSAT** (Remote Server Administration Tools) for Active Directory. You can download RSAT from Microsoft's website or install it via **Windows Settings**.

---

### Insufficient Permissions

Ensure you have the correct permissions to query Active Directory for user information. You'll need read access to the user attributes you want to export.

---

## Conclusion

This method allows you to easily export all Active Directory users and their details using PowerShell. You can customize the output to include specific user attributes and even filter users based on specific criteria (such as active users or users in specific OUs). The resulting CSV file can be opened in Excel or any text editor for analysis.

---

## Master Final Code

### Corrected PowerShell Script:

```powershell
Get-ADUser -Filter * -Property * |  
Select-Object SamAccountName, Name, GivenName, Surname, EmailAddress, Department, Title, Enabled,  
              @{Name="OUPath";Expression={ 
                  # Split the DistinguishedName into parts based on commas
                  $ouParts = ($_.DistinguishedName -split ",") 
                  # Extract the organizational units (OU=...)
                  $ouComponents = $ouParts | Where-Object {$_ -like "OU=*"} 
                  # Combine organizational units (OU) in top-to-bottom order
                  $ouPath = $ouComponents -join " > " 
                  # Prepend the SamAccountName (username) at the beginning
                  ($_.'SamAccountName') + " > " + $ouPath
              }} | 
Export-Csv -Path "C:\AD_Users.csv" -NoTypeInformation
```

---

### Key Adjustments:

- **Username First**: The username (`SamAccountName`) is now placed at the beginning of the **OUPath**.
- **OUs in Top-to-Bottom Order**: The OUs (OU=*) are extracted and joined together in top-to-bottom order using the `>` separator.

---

### Section Breaks
