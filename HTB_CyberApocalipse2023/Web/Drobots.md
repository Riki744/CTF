# Drobots

>Pandora's latest mission as part of her reconnaissance training is to infiltrate the Drobots firm that was suspected of engaging in illegal activities. Can you help pandora with this task?

# Solve

For this challenge we got login page when accessing spawned docker instance

![image](https://user-images.githubusercontent.com/85706972/227310825-6aec1570-22c5-46e9-8bae-efeb05a94ab4.png)

I usually for these try to use some default credentials like <strong>admin:admin</strong> or <strong>admin:password</strong>etc.
But nothing worked so, then started to look into the provided source code of the challenge and we see how login data is processed

![image](https://user-images.githubusercontent.com/85706972/227311258-27dc89cd-885b-4af6-ac92-8acc1981e598.png)

Seeing this I started to test for SQL injection as I did not see any checks on user input and luckily for us this worked
```
user: " OR 1=1 -- LIMIT 1
password: foo

```
This redirected us to the /home page were flag was presented 

![image](https://user-images.githubusercontent.com/85706972/227311801-2850106b-5b5e-4e34-8813-892bd02b5df0.png)
