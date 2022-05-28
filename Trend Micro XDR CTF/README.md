## LDS IT Conference 27-28 May at Baltic Beach Hotel ##

<p>I participated in IT conference which was happening on 27-28 May in Latvia. During this event, there were a lot of valuable presentations from different
vendors like - checkpoint, Red Hat, Trend Micro, Veritas, Aruba, Splunk, Fortinet, etc. In the first day of the conference, there was hosted CTF by Trend Micro, which
I took a part in it and actually made some good results, so I'll try to give some little insight into the challenges.</p>





## Trend Micro XDR CTF ##

![image](https://user-images.githubusercontent.com/85706972/170838490-baaf8aff-8da7-4f4e-997c-e436c6b1f195.png)


<p>
 This was my first CTF where I'd play a part as a blue teamer (Security Analyst), but it was a new experience for me, and I also had some fun time while competing for the top3 position.
 We were tasked to investigate many Incidents on Trend Micro Vision One Console (XDR), which I'd no experience before.
 </p>
 
 <p> Four scenarios to investigate in total and you had to answer the first question correctly in order to get further, at the end of each scenario you had to put a mind map of the whole attack, I was able to complete the first two. 
I'm not going to post whole walkthrough, as I did not had a time to write down each question and put answer that was correct, but will post mind maps and some information on challenges.</p>
 
 ![image](https://user-images.githubusercontent.com/85706972/170839111-43cce6d2-48c5-4aae-9419-80444a03e715.png)
 
 <li> Adventures in Wonderland - Spear Phishing Attack </li>
 
 </br>
 
 ![image](https://user-images.githubusercontent.com/85706972/170839254-12d7a364-85a0-45f0-a7f0-873f82991315.png)
 
 <li>Back to the Future - Not sure about one exact attack type, but there was a lot of things happening, but everything started from malicious word document. </li>
</br>

![image](https://user-images.githubusercontent.com/85706972/170839486-54e9efa1-efdc-47e2-a342-4339391a955d.png)

<li> Carrie - Reverse Shell via Powershell </li> 

<p> As mentioned I wasn't able to put down the mind map of whole attack, but in short attack somehow started with remote shell connection via powershell and 
  afterwards there was a lot of enumeration to many *.txt files and thats about it.
 
  <li>Dreamcatcher - Remote Shell via Perl </li>
  
  <p> Last of the challenge I believe was one of the hardest ones as it had much more events happening and you really needed to adapt fast so you can search for correct information.
    If I recall correctly then there was injection with perl reverse shell so that foothold could be established and afterwards python one liner to call back to c2 site...
  
    
 ## The End ##
    
<p>I might be maybe wrong on some information, but that's what I wrote down fast in my notes as the CTF was only happening for few hours.
In total this is first time that I've landed that high in CTF, so I'm happy that I was able to fast find information and get new expierience with Trend Micro Console.
Not to forget to mention that some guys who were also doing CTF were present physically on conference, while I was taking a part in it from home (online). </p>
      
      
 <li> My username is u333, I took this because this was username that I was registered with LDS event </li>
     
    
 ![image](https://user-images.githubusercontent.com/85706972/170839799-e33cf4b3-b0b7-4bc9-bd58-7eb111c11f8d.png)
    
    
  



