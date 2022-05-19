## Challenge Info ##
<p>
 Ramona's obsession with modifications and the addition of artifacts to her body has slowed her down and made her fail and almost get killed in many missions. For this reason, she decided to hack a tiny robot under Golden Fang's ownership called "Compressor", which can reduce and increase the volume of any object to minimize/maximize it according to the needs of the mission. With this item, she will be able to carry any spare part she needs without adding extra weight to her back, making her fast. Can you help her take it and hack it?
</p>

## Solve ##

<li>
  This challenge had not files to download, we had only to work with docker..
<li>
  After challenge docker was ready, We started by checking it on browser
 
  ![image](https://user-images.githubusercontent.com/85706972/169377810-54f76968-f3fc-4841-88dd-b84746b249f1.png)

<li>
  This did not helped to get better view of the challenge so I tried to connect to the challenge with nc
 
  ![image](https://user-images.githubusercontent.com/85706972/169378156-53bb66f6-c311-4e67-bfaf-08c11d4dd576.png)

<li>
  First we can choose one of the body ports, this was random pick for me , in the next action I took 3rd as it stood out for possibility to cat ./ file in current directory , at first tried to cat flag.txt , but that did not worked, I tried some bypass ways which in the end worked from LFI technique where we move up one directory ../ and in the end this was the solution that gave us the flag 
  
  ![image](https://user-images.githubusercontent.com/85706972/169378446-77d5c414-1b0b-41dd-99c2-2dad89c4dc1b.png)
