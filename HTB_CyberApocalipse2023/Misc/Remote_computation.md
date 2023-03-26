# Remote computation
>The alien species use remote machines for all their computation needs. Pandora managed to hack into one, but broke its functionality in the process. Incoming computation requests need to be calculated and answered rapidly, in order to not alarm the aliens and ultimately pivot to other parts of their network. Not all requests are valid though, and appropriate error messages need to be sent depending on the type of error. Can you buy us some time by correctly responding to the next 500 requests?

# Solve

When connecting to the container we get a small UI. Which have 3 options for Start, Help and Exit game , looking at Help we can see the rules of the game and Start obviously just starts the game which asks bunch of math questsions

```
$ 
[-MENU-]
[1] Start
[2] Help
[3] Exit
> 2

Results
---
All results are rounded
to 2 digits after the point.
ex. 9.5752 -> 9.58

Error Codes
---
* Divide by 0:
This may be alien technology,
but dividing by zero is still an error!
Expected response: DIV0_ERR

* Syntax Error
Invalid expressions due syntax errors.
ex. 3 +* 4 = ?
Expected response: SYNTAX_ERR

* Memory Error
The remote machine is blazingly fast,
but its architecture cannot represent any result
outside the range -1337.00 <= RESULT <= 1337.00
Expected response: MEM_ERR

[-MENU-]
[1] Start
[2] Help
[3] Exit
>

```

Manually trying to solve the questions is to slow, so we had to come up with script that does the job

```
import socket

# define the server's IP address and port number
SERVER_IP = ''  # replace with your server's IP address
SERVER_PORT = 

# create a socket object
client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# connect to the server
client_socket.connect((SERVER_IP, SERVER_PORT))

# receive the initial response from the server
response = client_socket.recv(1024).decode()
print(response)

# select an option
option = input('Select an option: ')
client_socket.send((option + '\n').encode())

# receive the next response from the server
response = client_socket.recv(1024).decode()
print(response)

# calculate the function
for i in range(500):
    # extract the expression from the server response
    start_index = response.find(':') + 1
    end_index = response.find('=')
    expression = response[start_index:end_index].strip()

    try:
        # evaluate the expression
        result = round(eval(expression), 2)  # round the result to two decimal places
        print(result)

        # check if the result is within the range of -1337.00 to 1337.00
        if result < -1337.00 or result > 1337.00:
            # send the memory error message to the server
            client_socket.send('MEM_ERR\n'.encode())
            response = client_socket.recv(1024).decode()
            print(response)
        else:
            # send the result to the server
            client_socket.send((str(result) + '\n').encode())
            response = client_socket.recv(1024).decode()
            print(response)

    except ZeroDivisionError:
        # handle division by zero error
        client_socket.send('DIV0_ERR\n'.encode())
        response = client_socket.recv(1024).decode()

    except SyntaxError:
        # handle syntax error
        client_socket.send('SYNTAX_ERR\n'.encode())
        response = client_socket.recv(1024).decode()

    print(response)
```

Flag - `HTB{d1v1d3_bY_Z3r0_3rr0r}`

