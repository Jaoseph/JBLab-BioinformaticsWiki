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

## JBLab Servers

As as group, we have our own private server which contains more processing power than your local work computer, but less than the HPC.

### Basic JBLab Login

Open Windows Powershell and enter the command:

```powershell
ssh jblab-srv003
```

Then, enter your CRUK password.

> 👍 BOOM! You're in!

## Institution-wide High Performance Cluster (HPC) servers

The CI institution has a High Performance Cluster (HPC) server which provides greater processing power for larger datasets which JBLab might not have enough to fully run.


### Getting Access

For HPC access → `Please kindly see the IT department to set up as a user on the HPC`

### Basic HPC Login

#### 0.5) If you are **outside of the institute's internet**, activate your VPN by following these steps:
- Click on the control panel on the bottom right corner ( *where you see your wifi, sound, battery* )
- Click `VPN`
- Click `CRI VPN` and `Connect`
- Type in your `CRUK password` and click `Connect`


#### 1) Enter the HPC Cluster on the Command Line 

Open Windows Powershell and enter the command:

```powershell
ssh clust1-sub-1
```

Then, enter your CRUK password.

> 👍 BOOM! You're in!

## FAQ/ Troubleshooting issues /

### Which Server Should I Use?

| Task Type | Use JBLab? | Use HPC? |
|-----------|------------|----------|
| **Small test scripts** | Yes | Not needed |
| **Exploratory analysis (R, Python)** | Yes | Optional |
| **Whole-genome alignments, large-scale jobs** | No | Strongly recommended |
| **Jobs taking >1 hour or using >8GB RAM** | No | Required |

#### Your first login into either JBLab or HPC servers will pop a message as shown below:

The authenticity of host [INSERT] can't be established.
[INSERT] key fingerprint is [INSERT].
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? 

Don't worry, its a first-time connection warning to trust or not trust the server.

**Enter:** `yes`


