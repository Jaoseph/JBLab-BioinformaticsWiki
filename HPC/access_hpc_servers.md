# Accessing JBLab Servers and HPC

*Coming soon — a quickstart guide to connecting to the cluster and gateway nodes*

--- 

## Overview

This section will cover:

- What servers are available
- How to access the servers required for your specific task
- (Potentially) SSH config set up
- Potential Troubleshooting issues

--- 

## Availability

#### JBLab Servers

As as group, we have our own private server which contains more processing power than your local work computer, but less than the HPC.

#### Institution-wide High Performance Cluster (HPC) servers




## Getting Access

For HPC access → `Please kindly see the IT department to set up as a user on the HPC`

## Basic HPC Login

#### 0.5) If you are **outside of the institute's internet**, activate your VPN by following these steps:
- Click on the control panel on the bottom right corner ( *where you see your wifi, sound, battery* )
- Click `VPN`
- Click `CRI VPN` and `Connect`
- Type in your `CRUK password` and click `Connect`


#### 1) Enter the HPC Cluster

Open Windows Powershell and enter the command:

```powershell
ssh clust1-sub-1
```

Then, enter your CRUK password.

> BOOM! You're in!

## Basic JBLab Login


Open Windows Powershell and enter the command:

```powershell
ssh jblab-srv003
```

Then, enter your CRUK password.

> BOOM! You're in!

## Troubleshooting issues / FAQ

#### Your first login into either JBLab or HPC servers will pop a message as shown below:

The authenticity of host '{INSERT} can't be established.
{INSERT} key fingerprint is {INSERT}.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? 

Don't worry, its a first-time connection warning to trust or not trust the server.

**Enter:** `yes`


