This investigation was conducted following a suspected insider threat acrivity involving an employee named Karen at TAAUSAI. A disk image of the suspect's Linux machine was acquired and analysed using FTK Imager.

The objective of this investigation was to identify evidence of malicious activity, reconstruct user actions, and determine whether the system was used for unauthorised or potentially harmful operations.

<img width="1126" height="178" alt="Preface" src="https://github.com/user-attachments/assets/73c3f95e-454b-43c4-9e85-ae764a24dbc6" />

The forensic analysis was conducted using FTK Imager to examine the acquired disk image.
Key areas of focus included:
System directories to identify OS and installed tools
Log files (e.g. Apache logs, authentication logs)
User artefacts such as .bash_history
Suspicious files within user directories
The investigation followed a systematic approach, beginning with system identification and progressing into user activity reconstruction through artefact analysis.

Q1: Which Linux distribution is being used on this machine?
Analysis of the /boot directory revealed files consistent with the Kali Linux distrivution, confirming that the system was running Kali Linux.

<img width="600" height="800" alt="Q1 answer" src="https://github.com/user-attachments/assets/0294fcfb-6ef6-4b80-a911-942c33815374" />

Q2: What is the MD5 hash of the Apache access.log file?
The Apache access log was located at /var/log/apache2/access.log.
Hash analysis was performed on this file using FTK Imager to ensure integrity. The MD5 hash value was d41d8cd98f00b204e9800998ecf8427e.

<img width="600" height="800" alt="Q2 Answer" src="https://github.com/user-attachments/assets/143094d6-e963-4774-ab77-0912b45d5e53" />

Notably, the file size was 0 bytes, Indicating that Apache had not been actively used on the system.

Q3: It is suspected that a credential dumping tool was downloaded. What is the name of the downloaded file?
The examination of the /root user directory revealed the presence of a compressed archive: mimikatz_trunk.zip
This file is associated with Mimikatz, a well known credential dimping tool used to extract authentication material such as passwords and hashes.

<img width="600" height="800" alt="Q3 Answer" src="https://github.com/user-attachments/assets/9ebe7882-e3e2-412b-b9ba-4bb6227e53c8" />

Its presence strongly suggests preparation for credential harvesting activity.

Q4: A super-secret file was created. What is the absolute path to this file?
Analysis of the .bash_history file revealed that a file was created using the touch command: /root/Desktop/SuperSecretFile.txt

<img width="600" height="800" alt="Q4 answer" src="https://github.com/user-attachments/assets/7139b3c0-9b54-46e1-ac9c-91ef29eaf665" />

This indicates deliberate user interaction and potential staging of senstive or concealed data.

Q5: What program used the file didyouthinkwedmakeiteasy.jpg during its execution?
Further inspection of .bash_history revealed execution of the command: binwalk didyouthinkwedmakeiteasy.jpg

<img width="600" height="800" alt="Q5 Answer" src="https://github.com/user-attachments/assets/3e073fb0-98cb-401a-b261-b1dda5c8a664" />

This confirms that the program binwalk was used to analyse the file, liekly indicating attempts to extract hidden or embedded data from the image.

Q6: What is the third goal from the checklist Karen created?
A checklist file located on the Desktop contained multiple goals, with the third goal identified as: "Profit"

<img width="600" height="800" alt="Q6 Answer" src="https://github.com/user-attachments/assets/1200daf2-ccec-4c53-8505-870d9fc254b3" />

This suggests financial motivation behind the activities conducted on the system.

Q7: How many times was Apache run?
The Apache access log file was found to be empty (0 bytes) refering from earlier, indicating there is no evidence of Apache activity on the system.

<img width="600" height="800" alt="Q7 Answer" src="https://github.com/user-attachments/assets/cf6aa702-2a92-4d7b-8ece-d583c507be2e" />

Q8: This machine was used to launch an attack on another. Which file contains the evidence for this?
A file named: irZLA0h1.jpeg was identified as containing evidence of an attack launched from the system.

<img width="600" height="800" alt="Q8 Answer" src="https://github.com/user-attachments/assets/b2cf91bd-e893-440c-884a-99fd6930bcda" />

Q9: It is believed that Karen was taunting a fellow computer expert through a bash script within the Documents directory. Who was the expert that Karen was taunting?
A script located in the Documents directory contained taunting language directed toward another individual.
The trageted indvidual was identified as: "young"

<img width="600" height="800" alt="Q9 Answer" src="https://github.com/user-attachments/assets/4cb82ff3-6282-4a3f-92aa-a40ee25ded58" />

Q10: A user executed the su command to gain root access multiple times at 11:26. Who was the user?
Log analysis revealed multiple executions of the "su" command at 11:26.
The user performing these actions was identified as: "postgres"

<img width="600" height="800" alt="Q10" src="https://github.com/user-attachments/assets/635c9afe-144e-4ee2-96c5-7184b2cc17ec" />

This indicates repeated attempts to gain elevated (root) privileges.

Q11: Based on the bash history, what is the current working directory?
Based on .bash_history, the working directory at the time of activity was: /root/Documents/myfirsthack/

<img width="600" height="800" alt="Q11 Answer" src="https://github.com/user-attachments/assets/43e3d97d-7f42-41be-a551-5eee092e2158" />

Timeline of artefacts:
1. System running Kali Linux identified
2. Credential dumping tool (Mimikatz) downloaded
3. User navigated and operated within /root/Documents/myfirsthack/
4. Suspicious image analysed using binwalk
5. File /root/Desktop/SuperSecretFile.txt created
6. Bash script used to taunt user "young"
7. Multiple privilege escalation attempts using "su"
8. Evidence suggests system used to support external attack activity

Conclusion:
The investigation identified clear indicators of malicious insider activity conducted by the user Karen.
Evidence shows: 
Use of offensive security tools (Mimikatz)
Attempts to analyse hidden data (binwalk usage)
Creation of suspicious files
Repeated privilege escalation attempts
Indicators of external activity
Additionally, artefacts such as the checklist and taunting script suggest intent and motivation, including financial gain aspects.

Overall, the findings strongly support the conclusion that the system was used for unauthorised and potentially malicious activities.
