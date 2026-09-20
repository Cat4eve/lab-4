# Network Programming Lab #2 - Command-line Networking Tools

## Task 1: Calculate IP ranges with `ipcalc`

Commands used:

```sh
ipcalc 192.168.1.13/24
ipcalc 192.168.1.13/24 | tee ipcalc1.txt
ipcalc 192.168.1.13/29 | tee ipcalc2.txt
```

Results:

- Network address (`/24`): `192.168.1.0`
- Broadcast address (`/24`): `192.168.1.255`
- Usable hosts (`/24`): `254` hosts (`192.168.1.1` - `192.168.1.254`)
- Network address (`/29`): `192.168.1.8`
- Broadcast address (`/29`): `192.168.1.15`
- Usable hosts (`/29`): `6` hosts (`192.168.1.9` - `192.168.1.14`)

The `/24` network contains 256 total IP addresses, while the `/29` network contains 8 total addresses. In each subnet, the network and broadcast addresses are not usable as normal host addresses.

---

## Task 2: Chat with yourself using Netcat

Commands used:

Terminal 1:

```sh
nc -l -p 5555
```

Terminal 2:

```sh
nc localhost 5555
```

Observation:

After the second Netcat process connected to the listener, text typed in one terminal appeared in the other terminal. Communication worked in both directions over a TCP connection.

---

## Task 3: Connect to a fellow student

I left a Netcat listener running:

```sh
nc -l -p 5555
```

I checked listening TCP ports using:

```sh
netstat -ltn
```

I then asked a classmate which listening port belonged to them and connected using:

```sh
nc localhost <their_port>
```

We were able to send text to each other using Netcat.

### What did you use to find the listening port?

I used:

```sh
netstat -ltn
```

The `-l` option shows listening sockets, `-t` limits the output to TCP, and `-n` displays numerical addresses and port numbers.

---

## Task 4: Finger yourself and your classmates

First I checked my local Finger information:

```sh
finger "$USER"
```

I created a `.project` file:

```sh
echo 'Network Programming Lab' > ~/.project
```

I created a `.plan` file:

```sh
nano ~/.plan
```

Example contents of `.plan`:

```text
Today I am learning about TCP connections, ports, Netcat, and Finger.
I learned that Finger normally uses TCP port 79.
Networking is interesting!
```

I made both files readable:

```sh
chmod a+r ~/.project ~/.plan
```

Then I checked my information again:

```sh
finger "$USER"
```

My `.project` and `.plan` information was displayed by Finger.

I also queried the Finger network service:

```sh
finger "$USER"@the.hell.am
```

and tested the service with a classmate's username:

```sh
finger <classmate_username>@the.hell.am
```

### What is the difference between these commands?

```sh
finger <username>
```

queries information about a user locally on the current machine.

```sh
finger <username>@the.hell.am
```

connects to the Finger server running on `the.hell.am` over the network and requests that user's information through the Finger application protocol.

---

## Task 5: Speak the Finger protocol using Netcat

I queried the Finger server directly using Netcat:

```sh
printf '%s\r\n' "$USER" | nc the.hell.am 79
```

I compared it with:

```sh
finger "$USER"@the.hell.am
```

The results were similar because both commands communicate with the Finger service on TCP port 79. The `finger` command is a specialized Finger client, while Netcat lets us manually send the protocol request.

### Why do we use `\r\n` at the end of the request?

`\r\n` is the CRLF line ending used by the Finger protocol. It marks the end of the request line so that the server knows the username request is complete and can process it.

---

### Checking port 79

Command used:

```sh
ss -ltnp | grep ':79'
```

The output showed that `inetutils-inetd` was listening for connections on TCP port 79.

### Why might old Unix systems use `inetd` instead of keeping every small network service running all the time?

`inetd` acts as a super-server. It listens for connections on behalf of several small services and starts the correct server program only when a request arrives. This saves memory and system resources because rarely used services do not need to run continuously.

---

### What if the username is empty?

Commands used:

```sh
finger @the.hell.am
```

and:

```sh
printf '\r\n' | nc the.hell.am 79
```

With an empty username, the Finger server can reveal general user information instead of information about one specific user. Depending on the server configuration, this may include usernames and login/session information.

### Could exposing this information have privacy or security implications?

Yes. Revealing usernames or login information can expose details about users and system activity. An attacker could use this information for reconnaissance, for example to discover valid usernames or learn who is currently using the system.

---

## Task 6: Visit the Fingerverse

I tested external Finger services such as:

```sh
finger random@happynetbox.com
finger ring@thebackupbox.net
```

I also queried a Finger service directly using Netcat:

```sh
printf 'random\r\n' | nc happynetbox.com 79
```

### What does this experiment tell you about the difference between an application protocol and a client program?

An application protocol defines the rules and message format used for communication between a client and a server.

The `finger` program is only one client program that implements the Finger protocol. Netcat can also communicate with the same Finger server because it can open a TCP connection and manually send a correctly formatted Finger request.

Therefore, the protocol is independent of the specific client program used to communicate with the server.

---

## Optional Task: Reverse Shell Simulation

This task is optional and was not required for completing the lab.

Security reflection:

A shell exposed through Netcat could allow another user to execute commands remotely. If such a service were exposed without authorization, it would be a serious security risk.

Possible protections include:

- using firewalls to restrict access;
- avoiding unsafe Netcat options;
- applying least-privilege permissions;
- monitoring unexpected listening ports and processes;
- disabling unnecessary network services;
- using secure authenticated remote-access tools such as SSH instead.

---

## Files included in the submission

The repository should contain:

```text
README.md
ipcalc1.txt
ipcalc2.txt
```

The `ipcalc` files were created using:

```sh
ipcalc 192.168.1.13/24 | tee ipcalc1.txt
ipcalc 192.168.1.13/29 | tee ipcalc2.txt
```

Final Git commands:

```sh
git add README.md ipcalc1.txt ipcalc2.txt
git commit -m "Complete network programming lab"
git push
```
