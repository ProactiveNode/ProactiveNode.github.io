---
layout: default
title: 'Creating AD Users using a PowerShell GUI'
excerpt: Using Powershell, you can create a GUI that can easily create AD users.
---
<h3>Creating AD Users using a PowerShell GUI</h3>
To easily create new users with their default groups and permissions, I have designed this PowerShell GUI. It utilizes System.Windows.Forms, System.Drawing and PresentationFramework. It is customizable for your organization and can create as many users as you like if you add the necessary rows. The GUI is contained within one window. When you select the Create Users button on the bottom of the window, you will be either greeted with a Success or Failure Pop-up window. Errors are displayed within the same PowerShell window that you used to launch the GUI.

We have four fields that are required to create an account as shown in Figure 1. The First and Last name fields are self-explanatory. The Job Title field is where you will need to specify what role the new user has. You will need to set a default password that adheres to your organization’s password policy.

![Figure 1](/assets/psGUI-Figure1.png)
<p style="font-size:90%"> Figure 1: Required Fields to Create a User. </p>

The First Name textbox and Last Name textbox use the same class from System.Windows.Forms as shown below in Figure 2. To place them in the correct area and length, I used System.Drawing.Point and System.Drawing.Size System.Drawing.Point and System.Drawing.Size use the following (horizontal, vertical) Point places where the textbox should be on the window. Size allocates how large the box should be. Once the values have been inputted, you will place the data onto the window by using $Screen.Controls.Add

{% highlight powershell %}
$firstName = New-Object System.Windows.Forms.TextBox
$firstName.Location = New-Object System.Drawing.Point($col1,30)
$firstName.Size = New-Object System.Drawing.Size(100,30)
$Screen.Controls.Add($firstName)

$lastName = New-Object System.Windows.Forms.TextBox
$lastName.Location = New-Object System.Drawing.Point($col2,30)
$lastName.Size = New-Object System.Drawing.Size(100,30)
$Screen.Controls.Add($lastName)
{% endhighlight %}
<p style="font-size:90%"> Figure 2: Code used to create First Name and Last Name </p>

Once those fields are filled in, the Job Title field must be used. In Figure 3 shown below, I have used a combo box which uses a dropdown to select the appropriate role for the new user. To populate the dropdown menu, I have used a hash table. You will have to enter the appropriate groups you would want to add into the hash table.

{% highlight powershell %}
$selectTitle = New-Object System.Windows.Forms.ComboBox
$selectTitle.Location = New-Object System.Drawing.Point($col3,30)
$selectTitle.Size = New-Object System.Drawing.Size(100,30)

$titleList = @{'' = ""; Accountant = "$Accountant"; Developer = "$Developer"}
$selectTitle.items.addRange($titleList.keys)
$selectTitle.DropDownStyle = [System.Windows.Forms.ComboBoxStyle]::DropDownList

$Screen.Controls.Add($selectTitle)
{% endhighlight %}
<p style="font-size:90%"> Figure 3: Job Title Field </p>


The final field that is required to create a new user is the Default Password. In Figure 4, I am using the MaskedTextBox instead of a Textbox because I wanted to mask the input of the password with an asterisk. Once you have entered the default password that coincides with your organizations password policy, it gets converted into a secure string that will be passed into the new user as shown in Figure 5.

{% highlight powershell %}
$passwordTextBox = New-Object System.Windows.Forms.MaskedTextBox
$passwordTextBox.Location = New-Object System.Drawing.Point(30,170)
$passwordTextBox.Size = New-Object System.Drawing.Size(80,60)
$passwordTextBox.PasswordChar = "*"
$Screen.Controls.Add($passwordTextBox)
{% endhighlight %}
<p style="font-size:90%"> Figure 4: Default password MaskedTextBox </p>

{% highlight powershell %}
$defaultPassword = $passwordTextBox.Text | ConvertTo-SecureString -AsPlainText -
{% endhighlight %}
<p style="font-size:90%"> Figure 5: Default password MaskedTextBox </p>

After all the fields have been populated, you will have to click the Create Users button to create them. It first goes through a few error checks to see if the fields have been populated. If they have been populated with data, then it will proceed to create the user. In Figure 6, three variables are being used to create the new user which is using the Powershell command New-ADUser. It will be up to the person who uses this tool to create the appropriate emails address. I am following the format of putting the first letter of the first name following a period and their last name E.g John Doe = J.Doe@ @magicTestDomain.test It will also be up to the user on what other fields they would want to add into the New-ADUser command such as their Phone Number, Manager, Address and more. In Figure 7, the $addAccountant and $addDev are used to put the user in the appropriate groups that were provided. Once they are added and everything was successful, the Textboxes are cleared and you are able to add more users once again.

{% highlight powershell %}
$fullName = $firstName.Text + " " + $lastName.Text
$emailAddr = $firstName.Text[0] + "." + $lastName.Text + "@magicTestDomain.test"
$defaultPassword = $passwordTextBox.Text | ConvertTo-SecureString -AsPlainText -Force
New-ADUser -Name $fullName -GivenName $firstName.Text -Surname $lastName.Text -EmailAddress $emailAddr -Title $selectTitle.Text -AccountPassword $defaultPassword
{% endhighlight %}
<p style="font-size:90%"> Figure 6: PowerShell Commands to Create New Users </p>

{% highlight powershell %}
	if ($?) {
		if ($selectTitle.Text -eq 'Accountant') {
                	$addAccountant = @("Account-Grp","Chicago-Office"); ForEach ($grp in $addAccountant) {Add-ADPrincipalGroupMembership $fullName -MemberOf $Grp }
            	} elseif ($selectTitle.Text -eq 'Accountant') {
                	$addDev = @("Developers-Grp","Chicago-Office"); ForEach ($grp in $addDev) {Add-ADPrincipalGroupMembership $fullName -MemberOf $Grp }
            	}
		$popupComplete = [System.Windows.MessageBox]::Show('User has been created','Completed')
		$FirstName.Clear()
		$LastName.Clear()
		$selectTitle.SelectedIndex = -1
            } else {
		$popupFailed = [System.Windows.MessageBox]::Show('User failed to be created','Failed','Ok','Error')
	    }
{% endhighlight %}
<p style="font-size:90%"> Figure 7: Command to Add User to Groups </p>

Reference:
[1] https://docs.microsoft.com/en-us/powershell/module/addsadministration/new-aduser
