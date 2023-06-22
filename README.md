# Intro 

| SQLServer AG Synchronization | The Synchronization of | Security Principals | Server Role Memberships | Permissions |
	
Sam Karamalisho

(https://github.com/SKaramalisho/AutoSync)

(https://www.linkedin.com/in/karamalishosamandar/)

# List of synchronized tasks:

•	Login Creation (SQL/Windows/Win Group)

•	Login Removal (SQL/Windows/Win Group)

•	SID Mismatches (SQL Auth)

•	Password Changes/Mismatches (SQL Auth)

•	Login Renames/Mismatches (SQL Auth)

•	Check Policy Changes (SQL Auth)

•	Check Expiration Changes (SQL Auth)

•	Password Changes (SQL Auth)

•	Login Enablement (SQL/Windows/Win Group)

•	Login Disablement (SQL/Windows/Win Group)

•	Add Server Role Membership (SQL/Windows/Win Group)

•	Remove Server Role Membership (SQL/Windows/Win Group)

•	Grant Server Permission (SQL/Windows/Win Group)

•	Revoke Server Permission (SQL/Windows/Win Group)


# Install Instructions

Using the Attached ZIP file, you can deploy the sync process across your AG Replicas.
Simply follow these instructions:

1.	Copy and Unzip ‘AGPrincipals_Installer_x.x.zip’, to the cluster (hosting the target AG)
2.	In a PowerShell Window (as Administrator), execute ‘AGPrincipals_Installer_x.x.ps1’, with or without parameters: (See Parameters listed in the "# PARAMETERS:" section (below))

a.	If you provide parameters, the installer will start the deployment (If your AGListener differs from the AGName, you MUST provide the parameter)

b.	If you don’t provide parameters, the installer will prompt for the required parameters, then start the deployment.

c.	Examples are listed in the "# EXMAPLES:" section (below)

NOTE: If you select WHITELIST or BLACKLIST, you must update the associated table, before enabling jobs (PrimaryPrincipals_Blacklist or PrimaryPrincipals_Whitelist) You can stage a text file, to insert a list of logins, and apply them with the $InputFile parameter. 

Once updated, and ready to enable, rerun the script with -Action “Enable” This can be circumvented with the $SkipEnablementSafetyCheck (great caution is needed here, as logins will begin to sync immediately, and may lead to unintentional changes on secondary replicas).

# Validation and Testing 

Once you’ve deployed the Sync Process, you should see the following:
1)	A Database (AGPrincipals_AGName) 
2)	The DB should be added, and seeded, on all AG replicas.
3)	A SQL Agent Job (!AOAG Sync Logins - AGName) should be added on all AG replicas.
4)	The job, on all replicas, should say: The Step Succeeded.


NOTE: If changes are applied to the secondary replicas, the changes will be logged in the job step details. 

# PARAMETERS:

$AG #TARGET AG NAME
 
$Type #Desired Sync Type
 
$AGListener #AG Listener Name (Required if different from AG Name)
 
$Frequency #Frequency of the sync job (10 second default)
 
$Action #Desired Action (Install/Uninstall/Enable)
 
$InputFile #File "path\name.txt" containing logins to insert into the Blacklist or Whitelist tables (Single column no headers)
 
$SkipEnablementSafetyCheck #Set to true IF you want to enable BlackList/Whitelist jobs immediately, without manual verification
 

# EXAMPLES:

#Basic Interactive Install (Script will prompt for values)
.\AGPrincipals_Installer_1.8.ps1

#Non-Interactive Install (Installs a WHITELIST sync, specifies a different listener, inserts values from file, auto-enables jobs)
.\AGPrincipals_Installer_1.8.ps1 -Action Install -AG POCAG2 -AGListener POCAG2Listener -Type WHITELIST -SkipEnablementSafetyCheck True -InputFile "C:\DL\InputFile.txt"

#Basic Uninstall (No prompts)
.\AGPrincipals_Installer_1.8.ps1 -Action Uninstall -AG POCAG1

#Basic Uninstall with -AGListener specified (No prompts)
.\AGPrincipals_Installer_1.8.ps1 -Action Uninstall -AG POCAG2 -AGListener POCAG2Listener
