# Small Steps
>As you continue your journey, you must learn about the encryption method the aliens used to secure their communication from eavesdroppers. The engineering team has designed a challenge that emulates the exact parameters of the aliens' encryption system, complete with instructions and a code snippet to connect to a mock alien server. Your task is to break it.

# Solve

Inspecting given files provided files we found hint in readme.md file about weak point in `e` RSA implementation, so we connect to docker and grab the key

![image](https://user-images.githubusercontent.com/85706972/227796384-6cfb310f-ab27-41c1-ba2a-d6ebe1a25e27.png)

![image](https://user-images.githubusercontent.com/85706972/227796408-f5770eaa-2c86-45ea-8d3d-d1030ea9ad70.png)

This was was easy reversable using online tool and provided and information that we had


![image](https://user-images.githubusercontent.com/85706972/227796449-d9b535e8-d22f-43f2-ab9c-0eac3aa75e66.png)

Flag - `HTB{5ma1l_E-xp0n3nt}`
