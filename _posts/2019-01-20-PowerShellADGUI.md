---
layout: default
title: 'Creating AD Users using a PowerShell GUI'
excerpt: Using Powershell, you can create a GUI that can easily create AD users.
---
<h3>Creating AD Users using a PowerShell GUI</h3>
To easily create new users with their default groups and permissions, I have designed this PowerShell GUI. It utilizes System.Windows.Forms, System.Drawing and PresentationFramework. It is customizable for your organization and can create as many users as you like if you add the necessary rows. The GUI is contained within one window. When you select the Create Users button on the bottom of the window, you will be either greeted with a Success or Failure Pop-up window. Errors are displayed within the same PowerShell window that you used to launch the GUI.

We have four fields that are required to create an account as shown in Figure 1. The First and Last name fields are self-explanatory. The Job Title field is where you will need to specify what role the new user has. You will need to set a default password that adheres to your organization’s password policy.
