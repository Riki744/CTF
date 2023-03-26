# Persistence
>Thousands of years ago, sending a GET request to /flag would 
grant immense power and wisdom. Now it's broken and usually returns 
random data, but keep trying, and you might get lucky... Legends say it 
works once every 1000 tries.

# Solve

To solve the challenge we just had to simply create a script that does 100 requests to docker instance at location /flag and inspecting content of response we can look for content starting HTB to get the flag

```
#!/usr/bin/python3

#Misc - Persistence

import requests

url = 'http://68.183.37.122:30100/flag'
count = 0

print("PLease wait, looking for flag:)")

while count < 1000:
	x = requests.get(url)
	result = x.content.decode()
	count += 1
	if result.startswith('HTB'):
		print(f"Flag: {result}")
		break
	else:
		continue
  ```
  
  Flag - `HTB{y0u_h4v3_p0w3rfuL_sCr1pt1ng_ab1lit13S!}`
