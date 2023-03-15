# Install
```bash
sudo apt install openvas
sudo gvm-setup
```
Wait until it's done, it can take a LOT of time.

# Run
```bash
sudo gvm-start
```
Go to https:localhost:9392 and sign in with admin:(password provided before)

Look for targets and add them: via GUI
```bash
nmap <subnet> -n -A
```

# Troubleshoot
## **Failed to find config 'daba56c8-73ec-11df-a475-002264764cea’**
https://www.youtube.com/watch?v=J7SrS4qDzM0
```bash
sudo su -
greenbone-feed-sync --type GVMD_DATA  
greenbone-feed-sync --type SCAP  
greenbone-feed-sync --type CERT
gvm-feed-update

sudo gvm-stop

Wait for 30 seconds and then enter this command

sudo gvm-start
```

OpenVas comes with a lot of problems and a lot of time to install. Takes a lot of patience.