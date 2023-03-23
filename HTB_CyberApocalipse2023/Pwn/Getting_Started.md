# Getting Started
>Get ready for the last guided challenge and your first real exploit. It's time to show your hacking skills.

#Solve

We are provide with the file and docker instance, looking into the file, there is python scripts, which looks to simply try to send bunch of A's and probably something like buffer overflow which allow us to overwrite the stack and control the outcome

![image](https://user-images.githubusercontent.com/85706972/227316377-83e8716c-e6ab-43ed-9f98-55d973777d77.png)

The first and simplest thing I tried was just edited the script to send instead of just 10 A's but 100 A's and of course changed the IP and PORT to docker instance and tried to send the payload, which worked and we got the flag back 

![image](https://user-images.githubusercontent.com/85706972/227316797-8cce3da3-eb1c-4808-bf53-3a4440c7cefe.png)
