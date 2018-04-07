# Week 9 Project: Honeypot

## Honeypots Deployed
1. dionaea (7,703 attacks)
2. snort (2,057 attacks)

## Brief
For this project, I deployed a Dionaea honeypot and a Snort honeypot. I was able to follow the instructions in the assignment and used the MHN server tool to set up three virtual machines - an admin and the two honeypots.

I didn't encounter any issues when setting things up. The instructions were straightforward, and the scripts from MHN worked as expected.

## Data Collected
See the [json export](https://github.com/robeau/codepath-assignments/blob/master/session.json) for a copy of the raw data.

Between the two honeypots, I received 9,760 attacks (7703 on Dionaea and 2057 on Snort).
The attacks came from 1,859 different IP addresses.
The attacks used 13 different protocols:
- pcap: 5,183 attacks
- TCP: 1,807
- SipSession: 1,017
- mysqld: 615
- SipCall: 390
- smbd: 365
- UDP: 250
- httpd: 59
- mssqld: 47
- ftpd: 19
- RtpUdpStream: 3
- epmapper: 3
- TftpServerHandler: 2
