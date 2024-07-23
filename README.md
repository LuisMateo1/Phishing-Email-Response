# Phishing Email Response on LetsDefend
This is the alert received on the SIEM
![image](https://github.com/user-attachments/assets/f16796e2-8bd3-4ee9-9c5e-df8f48673e06)
Immediately we can see that the domain of the email address is spoofed, instead of 'coffeeshop.com' it says 'coffeeshooop'

After looking up the IP address on Cisco Talos and AbuseIPDB, the IP address links back to South Korea, has a poor reputation but is only listed on one block list
<!--![image](https://github.com/user-attachments/assets/60f1599a-67a4-4812-bbe7-164af5542aeb)-->
![image](https://github.com/user-attachments/assets/18612bc8-0e83-4e11-91ed-e0253ab6dd2f)
![image](https://github.com/user-attachments/assets/b0884ae3-7c6c-4b0b-8d28-8dbbc259d213)

Now let's investigate the email further
![image](https://github.com/user-attachments/assets/5e6b9755-186a-4fcf-8851-128cb8a2bd04)

![image](https://github.com/user-attachments/assets/747d3b88-5ad9-4b7d-887b-7eb52125b653)
The email claims to offer a free coffee voucher and when the redeem button is clicked it will download the attached file

After downloading the file and putting it into VirusTotal we see that it gets flagged by 54/66 security vendors as a backdoor. A backdoor provides unauthorized access to a system this is also known as a Remote Access Trojan or RAT.
![image](https://github.com/user-attachments/assets/9c97e401-49d9-4489-99cc-6374dddd9834)

It also shows the other names this file goes by and the other hash values of the other algorithms
![image](https://github.com/user-attachments/assets/51bf1559-d615-46d8-8e4a-d310e982eedd)

On the community tab, it was investigated on the ANY.RUN sandbox. There is a tag saying 'asyncrat' after a Google search this is a Remote Access Trojan or RAT, it provides unauthorized remote access to the system.

![image](https://github.com/user-attachments/assets/14794fda-90aa-47d0-aa08-f0e28d534ad5)

Let's check the ANY.RUN report
Here we see that the aforementioned AsyncRAT, is flagged as a threat. The RAT is installed when the Coffee.exe file is run.
![image](https://github.com/user-attachments/assets/cdb3b0e4-ec59-44bf-8c78-d27f2b6926f2)

The report also tells us the behavior, I can use this to help me investigate whether the email recipient opened the malware.
![image](https://github.com/user-attachments/assets/339f7b83-7a12-48a2-96d0-084c047616a8)
Also here we see the Process ID(PID:2476), Port: 3451, and the IP: 37.120.233.226 of the Command and Control (C2) server the RAT communicates with.


Now that the file has been identified as malicious, let's check if the recipient clicked the link, downloaded the file, and opened it

The file was opened (Event time, PID, process name, parent process, command line)
![image](https://github.com/user-attachments/assets/469fcf4f-02e8-4067-8d60-e5415479f848)

Coffee.exe opens cdm.exe to run commands
![image](https://github.com/user-attachments/assets/f2c8ceaf-e082-416e-9625-0073452244ae)

Runs systeminfo.exe, hostname.exe, and other processes to achieve its purpose of communicating with the C2 server
![image](https://github.com/user-attachments/assets/646cb3c6-6e30-44f2-a638-31b2d80e5171)
![image](https://github.com/user-attachments/assets/b6841b5b-4077-4f19-b6ad-b87d3ca90e06)
![image](https://github.com/user-attachments/assets/3b989f3d-ae21-4a7c-9021-03c56701c7e2)
The observed behavior matches what was on the ANY.RUN report.

Now I'll check the logs to see if the connection was made to the C2 server

Two logs show up in the query, and we see that Coffee.exe did create a TCP connection to the C2 server over port 3451
![image](https://github.com/user-attachments/assets/9bf643b6-c708-4199-9e0d-4383d9f75011)
![image](https://github.com/user-attachments/assets/fcb2eba6-e01d-48b5-b4c3-8f81ac83effe)
![image](https://github.com/user-attachments/assets/dc023660-04e3-4f17-86f5-c3b6adc4f8f5)

Now that I'm sure that the system has been infected I'll contain/isolate the system so that no further damage occurs, and delete the email
![image](https://github.com/user-attachments/assets/f7941d43-984b-40b2-bdbf-f6c820fec8f5)

Now I'll add the necessary artifacts to the block lists

![image](https://github.com/user-attachments/assets/29f8dd11-7251-4feb-9a07-f9449b85438c)


