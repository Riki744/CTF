## Challenge Info ##
<p>
  Emergency! A space station near Urkir was compromised. Although Urkir is considered to be the very embodiment of the neutral state, it is rich of fuel substances, something that Dreager is very much interested in. Thus, there are now fears that the intergalactic war will also affect this neutral planet. If Draeger and his mercenaries manage to maintain unauthorised access in Urkir's space station and escalate their privileges, they will soon be able to activate the station's defence mechanisms that are able to prevent any spaceship from entering Urkir's airspace. For now, the infected machine is isolated until the case is closed. Help Miyuki find their persistence mechanisms so they cannot gain access again.
</p>

File to download - forensics_golden_persistence.zip (NTUSER.DAT)

## Solve ##

<li>
  First I check if that is really a NTUSER.DAT file by running file command in kali
</li>

![image](https://user-images.githubusercontent.com/85706972/169376085-3b1b4077-2900-43f0-92bc-cb821add9135.png)


<li>
  To extract any info fast from NTUSER.DAT file we can use tool "regripper" which is going to dump all info in plaintext to our pointed log file
</li>

![image](https://user-images.githubusercontent.com/85706972/169376176-e8005333-d907-460f-904c-28c64e46fc7e.png)

<li>
  When extract have completed let's open our log.txt and go through to see if anything stands out...At the end of the log.txt file I noticed some weird powershell command
</li>

![image](https://user-images.githubusercontent.com/85706972/169376428-99f1582b-d451-4c65-9b2b-c92bde1b8b1b.png)

<li>
  So I put this huge unknown string in txt file and tried to decode it using base64 were the output was put to decode.txt
</li>

![image](https://user-images.githubusercontent.com/85706972/169376619-378c2149-357c-4671-92f7-5b45c61528ca.png)

![image](https://user-images.githubusercontent.com/85706972/169376710-2f1a1667-1f4d-4adb-90ec-2a06378e5e81.png)

<li>
  It looks like we found the persistence mechanisms as when I tried to save the script in my Windows VM the AV flagged it as malicious, so I’d to disable it for further inspection 
<li>
  At first I tried to get overall understanding what script does and obviously it’s trying to download something malicious on execution, so my thoughts was why not to try to as we are in sandbox VM and we can load that script in memory and see what it does, but I’d to replace those encrypted1-5 values which you can find by opening NTUSER.DAT file with OSForensic software
  
![image](https://user-images.githubusercontent.com/85706972/169376990-c839fad2-0450-4903-9a16-c19222317ad7.png)
  
<li>
  Replacing the values and loading the script in memory, we see that it tries to browse the link which was hidden in script and in the end we got the flag after getting error.... 

![image](https://user-images.githubusercontent.com/85706972/169377120-16826e8c-357f-424d-abce-219a875cacf7.png)

</li>
  
 Flag - HTB{g0ld3n_F4ng_1s_n0t_st34lthy_3n0ugh}
  
