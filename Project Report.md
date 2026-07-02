# Active Directory Home Lab

**Author:** Sam Gillette

**Project:** Active Directory

**Lab Type:** Home Lab

**Date:** 5/28/2026

---

# Executive Summary

The purpose of this project was to build a simulation of what a real company would look like inside of my own Windows 2022 Server VM and Windows 11 Pro VM. I created an Active Directory Environment in which I assigned roles to users and updated security configurations that applied the principle of least privilege. I also utilized the Group Policy Objects and Item-Level Targeting in order to help put the drives onto the accounts' computers. This turned out to be successful as I was able to configure security settings to make it so that the principle of least privilege was applied, and that the accounts had the files on their computers.

---

# Environment

The environment I used was UTM on my MacBook, where I had a Domain Controller, which was a Windows Server 2022, which I had the hostname as DOMAINCONTROLLE and the domain name as SAMHOMELAB.LOCAL.

I used a folder titled **"Company Shares,"** which I divided into four branches:

- IT
- Sales
- HR
- Finance

I did this to try to simulate a real company on a very small scale.

My Target Workstation was a Windows 11 Pro VM.

I had:

- Jamal Murray as my HR user
- Chris Paul as my Sales user
- Sam Gillette as my IT user

---

# Implementation and Process

## File System and Share Permissions

To start on my Domain Controller, I created the folder:

```text
C:\Company Shares
```

which includes four folders inside, each titled their own respective departments.

I had issues allowing users inside my simulation to access their respective folders without allowing them access to all of the folders. I needed to get this right because it is a necessary skill within cybersecurity to enforce the principle of least privilege.

I configured Share Permissions to share the company shares with everyone.

I configured the NTFS Security Permissions to grant the respective privileges to the necessary individuals to allow them to do their job.

For example, I allowed my HR User to modify, read, and write inside of the HR Folder, but I didn’t allow access to the Sales folder.

> **Screenshot:** Restricted access to the Sales department while logged into the HR department.

As you can see from this screenshot, I am restricted access to the sales department while being logged into the HR department. This is a success because I enforced the principle of least privilege.

---

## Group Policy

I created a GPO called **SAMHOMELAB GPO**.

I navigated to **Drive Maps**, where I was able to create a new Mapped Drive item so that there was a drive under the **This PC** section for each department to seamlessly access the folder for their department.

I put this under the location:

```text
\\DOMAINCONTROLLE\Company Shares\X
```

where **X** is whatever department the user was a part of.

I labeled the drive letter based on the department as well. For example:

- H: Drive → HR
- S: Drive → Sales

Then I used Item-Level Targeting so that the right drive went to the right user, and they were the only ones who had access to it.

> **Screenshot:** HR Folder that the HR account has access to.

This screenshot shows the HR Folder that the HR account has access to.

---

# Troubleshooting

During this lab, I came across many issues that I had to fix in order to get them to work.

## Issue #1 – Network Path Access Failure

My first incident was a network path access failure.

When I ran:

```powershell
gpresult /h gpreport.html
```

on my Client Workstation, it indicated that the Group Policy was failing execution due to an access lock.

To fix this, I opened the share permissions to everyone and explicitly assigned NTFS directory to the necessary users and removed Domain Users so that not everyone had access to all the drives.

---

## Issue #2 – Group Policy Not Applying

The second issue I had was that the Group Policy was not applying because it was unclear which users should receive the drive.

To fix this, I updated the GPO filter to target precisely:

```text
SAMHOMELAB\GG_SALES_Staff
```

the group that I put my sales users into.

This allowed the server to accurately identify my sales team.
