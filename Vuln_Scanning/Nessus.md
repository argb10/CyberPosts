# Install
```bash
curl --request GET \ --url 'https://www.tenable.com/downloads/api/v2/pages/nessus/files/Nessus-10.5.0-debian10_amd64.deb' \ --output 'Nessus-10.5.0-debian10_amd64.deb'

sudo apt install ./Nessus-10.5.0-debian10_amd64.deb

sudo /bin/systemctl start nessusd.service
```
- Go to https://kali:8834/ to configure the scanner.

Configure account:
- Go here https://www.tenable.com/products/nessus/nessus-essentials
- Register
- Get your activation code in your email
- Enter email in the Nessus instalation.

# Run
Just use the GUI to run scans against targets.
Be aware that the nessus needs to download plugins before starting an scan, this can take a while.