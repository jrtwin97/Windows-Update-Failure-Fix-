# Windows-Update-Failure-Fix

I recently installed Windows Updates on my Host Machine & Virtual Machine. After doing so I ran into error:

<img width="677" height="393" alt="Screenshot 2026-09-13 210221" src="https://github.com/user-attachments/assets/0b8d8686-f88a-4ac4-a717-b1f2468f4199" />

Windows recently released a September Update and have been crashing Clients, Servers & RDP Sessions. Windows Admins are reporting RDS issues after deploying the September 2026 Windows Update. 
This has affected multiple editions such as Windows Server 2022 KB5122882 & Windows Server 2025 KB5122871
Reported issues were: 

RDP sessions stuck at “Connecting” or “Securing remote connection”, New RDS sessions unable to connect, Logoff/disconnect operations hanging, Server restart temporarily restoring RDS availability, Console or hypervisor access sometimes required for recovery.

I experienced this while attempting to connect to VM Hyper-V thru Windows Server. Before update no issues connecting. 

<img width="552" height="484" alt="Screenshot 2026-09-27 211315" src="https://github.com/user-attachments/assets/be2cc63f-7f53-4551-bf4e-3b0b1948e2bf" />

Microsoft’s own description of the issue is: “RDS might become unstable, resulting in RDP connections failing after several minutes, sign-in issues, or servers hanging at ‘Please wait for the Remote Desktop Configuration.'” Related tools including Microsoft Management Console (MMC), the RDS Licensing Diagnose, and File Explorer could also become unresponsive, and the Windows Update page itself could hang on a loading indicator.

The best way is to rollback back before the update. You can also uninstall & reinstall windows vm. The first instance is to ensure that Virtual Machine is enabled. 

Open Run prompt: Reg Edit> Registry Editor Go to:  

<img width="1065" height="127" alt="Screenshot 2026-09-28 091436" src="https://github.com/user-attachments/assets/90d11958-ec70-47c8-b2b5-538ad4d948e4" />

Select ListenerPort, check the value is (2179). This is the default value for Windows OS. 
We will check that the connectivity is there, since we know that Windows is not able to connect with this port number. We will verify though cmd line. Open CMD as Admin.
Type: Netstat -ano | find “2179”  I did not receive any result so this port is missing 

<img width="1020" height="236" alt="Screenshot 2026-09-28 092242" src="https://github.com/user-attachments/assets/6205692b-7a1a-47cd-be6a-d9e8d421d986" />

Will have to change from “2179” to “21791” 

<img width="1332" height="218" alt="Screenshot 2026-09-28 093136" src="https://github.com/user-attachments/assets/e37a5192-40f3-4a15-9405-793d64369b7e" />

After changing value cmd cannot find listener port.

<img width="988" height="181" alt="Screenshot 2026-09-28 093500" src="https://github.com/user-attachments/assets/c537c50b-b82f-4529-a891-86f975471c54" />

I will need to make windows listen to VM. Open Powershell as Admin.

<img width="882" height="169" alt="Screenshot 2026-09-28 094047" src="https://github.com/user-attachments/assets/e265a23e-4b2e-4861-92c8-18b91fc7ca18" />

Stop-service vmms 
Start-service vmms 
Now go back to CMD and run command again. Now port 9324 is listening 

<img width="1278" height="153" alt="Screenshot 2026-09-28 094228" src="https://github.com/user-attachments/assets/df9a46f5-66b5-48d6-a66f-129e73d877af" />

Now we will go back to VM- Hyper V Manager to see if it connects 

<img width="1095" height="185" alt="Screenshot 2026-09-28 095055" src="https://github.com/user-attachments/assets/7798c149-202d-4f98-b1af-b367cafab4e2" />





<img width="999" height="663" alt="Screenshot 2026-09-21 192640" src="https://github.com/user-attachments/assets/069cd061-6f4a-49dc-aee3-a1c8b54e3f05" />

Windows Server now connects. 


<img width="696" height="519" alt="Screenshot 2026-09-13 211814" src="https://github.com/user-attachments/assets/ffe751db-a5cd-4a40-be6a-128e7db1030d" />


You can also uninstall & reinstall VM. Go to windows turn features on or off. Deselect Hyper-V. This will remove Hyper-V and VM settings. This will enforce to reboot device. 


<img width="806" height="249" alt="Screenshot 2026-09-28 101147" src="https://github.com/user-attachments/assets/35c0c56c-ff77-4023-b9d9-249d045c6847" />


After rebooting device. Go back to Turn features on and select Hyper-V.

<img width="841" height="349" alt="image" src="https://github.com/user-attachments/assets/0f78d077-45cd-4292-9cfe-ff742f73c8a2" />


After rebooting again Hyper-V is reenabled. You may need to reconfigure network adapter settings since this was removed. After doing so you just reconnect to VM. This time I temporary disabled Windows Auto update. Security updates are essential, do disabling windows update is not recommended.


<img width="560" height="442" alt="Screenshot 2026-09-28 011214" src="https://github.com/user-attachments/assets/969b2f9b-0f74-4975-bf08-98f729cc11db" />

At the date of this lab, Microsoft has recently rolled out updated fix for this. 


<img width="732" height="279" alt="Screenshot 2026-09-28 102329" src="https://github.com/user-attachments/assets/be8aa14a-eb07-4e15-b90e-d1678358b92c" />




