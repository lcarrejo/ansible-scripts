==================================================
****************** README ****************** 
==================================================

It includes the following sections: 

	Section I, "Description of the Script"

	Section II, "Script Files details"
	
	Section III, "Prerequisites"

	Section IV, "Script Installation Instructions"
	
	Section V, "Script Execution Steps"
	
	Section VI, "Sample Ansible Script Outputs And How to Read them"
	
------------------------------------------------------
I. Description of the Script
------------------------------------------------------

	Ansible script provided connect to Set of Defined Hosts/IPs  and from each host ping defined network interfaces of all other hosts.

	For e.g. if you have 3 hosts - host1, host2 & host2 and each of these 3 hosts has 3 network interfaces int1, int2 & int3, Ansible script will connect to each of the 3 hosts and from each host ping all network interfaces of itself and other 2 hosts. For e.g. ansible connects to host1 and pings all 3 interfaces int1, int2 & int3 of host1 and also other 2 hosts host2 & host3.

------------------------------------------------------
II. Script Files details
------------------------------------------------------

	hosts-file:

	Define the IPs to which ansible should connect and do the ping test on all network interfaces. If user/password is specified for any listed IPs then ansible connects using the user/password defined under all:vars. If user/password is specified next to the IP then ansible connects using that user/password. Replace the username and password in this file to the username and password you want ansible to connect to the IPs listed. Replace the 10.x IP's with the range of IP's which you want ansible to connect to do ping tests.

	ping-all-net-interfaces.yml:

	This is the file which ansible checks to see which network interfaces of the hosts (defined in hosts-file)  ping test has to be done. In this file define first 3 octets of the network interfaces (under item.startswith line) which you want to ping of hosts (which are defined in hosts-file). Change the 10.x, 192.x, 172.x IP subnet first 3 octets in this file to the octets corresponding to your hosts which you want to ping.


------------------------------------------------------
III. Prerequisites
------------------------------------------------------

	1. Ansible should be istalled on the host from where you run the ping tests.
	
	2. Make sure you are able to connect to Hosts defined in hosts-file from the machine you are running ansible ping tests.
	
	3. Make sure /tmp directory exists on the machine from which you are running ansible tests.  

------------------------------------------------------
IV. Script Installation Instructions
------------------------------------------------------

	1. Download below files and copy them to any location on the machine where you want to run the ansible tests. Make sure read write execute permissions are given to the files.

		-	hosts-file
		-	ping-all-net-interfaces.yml

	2. Modify hosts-file and make following changes.

		a. Replace the 10.x IP's with the range of IP's which you want ansible to connect to do ping tests.
		b. Replace the username and password in this file to the username and password you want ansible to connect to the IPs listed.
		
	3. Modify ping-all-net-interfaces.yml file and make following and change the 10.x, 192.x, 172.x IP subnet first 3 octets in this file to the octets corresponding to your hosts which you want to ping.
	

------------------------------------------------------
V. Script Execution Steps
------------------------------------------------------

	1.	Connect to the machine where you want to run ansible tests and change directory to location where you have saved hosts-file & ping-all-net-interfaces.yml script files.
	
	2. Execute ansible script as follows:
	
		# ansible-playbook -i hosts-file ping-all-net-interfaces.yml
		
	You will console output as follows which means that ansible script is running.	

			# ansible-playbook -i hosts-file ping-all-net-interfaces.yml

			PLAY [all] **********************************************************************************************************************************************

			TASK [Gathering Facts] **********************************************************************************************************************************
			ok: [10.11.12.147]
			ok: [10.11.12.141]
			ok: [10.11.12.240]
			..................
			..................
			..................			

------------------------------------------------------
VI. Sample Ansible Script Outputs And How to Read them
------------------------------------------------------

There are four main sections in terms of reviewing ansible script output. Below are details.

	1.	TASK [Gathering Facts] 
	
			This section shows all the hosts to which ansible script connects. If there is a failure to connect to a specific hosts, we will see failure/error here.
			
			Below is example snippet of what we see in this section, where we see ansible connection succeeded to all 3 hosts 
								
					ok: [10.11.12.147]
					ok: [10.11.12.141]
					ok: [10.11.12.240]


	2.	TASK [Add all IP addresses] 
	
			This section lists all the interfaces of defined hosts in hosts-file which match the range defined ping-all-net-interfaces.yml
			
			Below is example snippet of what we see in this section, where we see all 4 interfaces of 10.11.12.141 host being listed by ansible script.
													
					changed: [10.11.12.141 -> localhost] => (item=172.16.0.10)
					changed: [10.11.12.141 -> localhost] => (item=10.11.12.141)
					changed: [10.11.12.141 -> localhost] => (item=192.168.5.177)
					changed: [10.11.12.141 -> localhost] => (item=192.168.2.156)
			
	3.  TASK [ping all IP addresses] 
	
			This section shows the ping test being done from every host to every other host interface which is defined. If there is a failure of ping from any host to interface of any other host we will see the error/failure in this section.
			
			Below is example snippet of what we see in this section, where we see ping tests from 10.11.12.141 host to other 3 interfaces of other host succeeded.
																		
					ok: [10.11.12.141] => (item=10.11.12.240)
					ok: [10.11.12.141] => (item=10.11.12.147)
					ok: [10.11.12.141] => (item=192.168.5.183)						
			
	4.  PLAY RECAP
	
			This section shows the final results of the ansible script ping tests. 
				
				- PLAY RECAPT has unreachable column for reach defined host which shows whether ansible script connected to the host or not. If connection succeeds the value of unreachable will be 0. Any value other than 0 for unreachable means that there is an issue with ansible script connecting to the host.
				
				- PLAY RECAPT has failed column for reach defined host which shows whether if pings from that host to any other hosts failed. If ping test from the host to all host interfaccs succeeded the value of failed will be 0. Any value other than 0 for failed means that there is an issue with ping tests.
				
				- PLAY RECAPT has ok column for reach defined host which shows number of ping tests which succeeded from that host.
			
			Below is example snippet of PLAY RECAP after running ansible script which from the test which suceeded.
								
					PLAY RECAP **********************************************************************************************************************************************
					10.11.12.141             : ok=3    changed=1    unreachable=0    failed=0
					10.11.12.147             : ok=3    changed=1    unreachable=0    failed=0
					10.11.12.240             : ok=4    changed=2    unreachable=0    failed=0
			
Below are 3 scenarios which are described which are possible ansible outputs based on success or failure.

		scenario 1:	Ansible ping tests have passed successfully.

		scenario 2: Ansible script could not connect to one or more hosts to do ping tests
		
		scenario 3:	There is ping failure to few network interfaces of few hosts from particular host.
	
	Below is snippet of sample outputs on each of above scenario.
	
		========================================================
		scenario 1:	Ansible ping tests have passed successfully.
		========================================================
						
				# ansible-playbook -i hosts-file ping-all-net-interfaces.yml

				PLAY [all] **********************************************************************************************************************************************

				TASK [Gathering Facts] **********************************************************************************************************************************
				ok: [10.11.12.147]
				ok: [10.11.12.141]
				ok: [10.11.12.240]

				TASK [Delete ipaddresses.txt] ***************************************************************************************************************************
				changed: [10.11.12.240 -> localhost]

				TASK [Add all IP addresses] *****************************************************************************************************************************
				skipping: [10.11.12.240] => (item=172.19.0.1)
				changed: [10.11.12.141 -> localhost] => (item=172.16.0.10)
				changed: [10.11.12.240 -> localhost] => (item=10.11.12.240)
				changed: [10.11.12.147 -> localhost] => (item=10.11.12.147)
				changed: [10.11.12.147 -> localhost] => (item=192.168.5.183)
				changed: [10.11.12.141 -> localhost] => (item=10.11.12.141)
				changed: [10.11.12.240 -> localhost] => (item=10.11.12.226)
				changed: [10.11.12.147 -> localhost] => (item=172.17.0.104)
				changed: [10.11.12.240 -> localhost] => (item=192.168.5.240)
				changed: [10.11.12.141 -> localhost] => (item=192.168.5.177)
				changed: [10.11.12.147 -> localhost] => (item=192.168.2.130)
				changed: [10.11.12.141 -> localhost] => (item=172.17.0.29)
				changed: [10.11.12.240 -> localhost] => (item=172.17.0.40)
				changed: [10.11.12.147 -> localhost] => (item=172.16.0.35)
				changed: [10.11.12.141 -> localhost] => (item=192.168.2.156)
				changed: [10.11.12.240 -> localhost] => (item=192.168.2.240)
				skipping: [10.11.12.141] => (item=10.141.172.165)
				changed: [10.11.12.240 -> localhost] => (item=192.168.2.116)
				changed: [10.11.12.240 -> localhost] => (item=192.168.2.117)
				changed: [10.11.12.240 -> localhost] => (item=172.16.0.39)
				skipping: [10.11.12.240] => (item=172.18.0.1)

				TASK [ping all IP addresses] ****************************************************************************************************************************
				ok: [10.11.12.147] => (item=10.11.12.240)
				ok: [10.11.12.141] => (item=10.11.12.240)
				ok: [10.11.12.240] => (item=10.11.12.240)
				ok: [10.11.12.141] => (item=10.11.12.147)
				ok: [10.11.12.147] => (item=10.11.12.147)
				ok: [10.11.12.240] => (item=10.11.12.147)
				ok: [10.11.12.147] => (item=192.168.5.183)
				ok: [10.11.12.141] => (item=192.168.5.183)
				ok: [10.11.12.240] => (item=192.168.5.183)
				ok: [10.11.12.147] => (item=10.11.12.141)
				ok: [10.11.12.141] => (item=10.11.12.141)
				ok: [10.11.12.240] => (item=10.11.12.141)
				ok: [10.11.12.147] => (item=10.11.12.226)
				ok: [10.11.12.141] => (item=10.11.12.226)
				ok: [10.11.12.240] => (item=10.11.12.226)
				ok: [10.11.12.147] => (item=172.17.0.104)
				ok: [10.11.12.141] => (item=172.17.0.104)
				ok: [10.11.12.240] => (item=172.17.0.104)
				ok: [10.11.12.147] => (item=192.168.5.240)
				ok: [10.11.12.141] => (item=192.168.5.240)
				ok: [10.11.12.240] => (item=192.168.5.240)
				ok: [10.11.12.147] => (item=192.168.5.177)
				ok: [10.11.12.141] => (item=192.168.5.177)
				ok: [10.11.12.240] => (item=192.168.5.177)
				ok: [10.11.12.147] => (item=192.168.2.130)
				ok: [10.11.12.141] => (item=192.168.2.130)
				ok: [10.11.12.240] => (item=192.168.2.130)
				ok: [10.11.12.147] => (item=172.17.0.29)
				ok: [10.11.12.141] => (item=172.17.0.29)
				ok: [10.11.12.240] => (item=172.17.0.29)
				ok: [10.11.12.147] => (item=172.17.0.40)
				ok: [10.11.12.141] => (item=172.17.0.40)
				ok: [10.11.12.240] => (item=172.17.0.40)
				ok: [10.11.12.147] => (item=172.16.0.35)
				ok: [10.11.12.141] => (item=172.16.0.35)
				ok: [10.11.12.240] => (item=172.16.0.35)
				ok: [10.11.12.147] => (item=192.168.2.156)
				ok: [10.11.12.141] => (item=192.168.2.156)
				ok: [10.11.12.147] => (item=192.168.2.240)
				ok: [10.11.12.240] => (item=192.168.2.156)
				ok: [10.11.12.141] => (item=192.168.2.240)
				ok: [10.11.12.147] => (item=192.168.2.116)
				ok: [10.11.12.240] => (item=192.168.2.240)
				ok: [10.11.12.141] => (item=192.168.2.116)
				ok: [10.11.12.147] => (item=192.168.2.117)
				ok: [10.11.12.141] => (item=192.168.2.117)
				ok: [10.11.12.240] => (item=192.168.2.116)
				ok: [10.11.12.147] => (item=172.16.0.39)
				ok: [10.11.12.141] => (item=172.16.0.39)
				ok: [10.11.12.240] => (item=192.168.2.117)
				ok: [10.11.12.240] => (item=172.16.0.39)

				PLAY RECAP **********************************************************************************************************************************************
				10.11.12.141             : ok=3    changed=1    unreachable=0    failed=0
				10.11.12.147             : ok=3    changed=1    unreachable=0    failed=0
				10.11.12.240             : ok=4    changed=2    unreachable=0    failed=0
				
						
		=======================================================================================
		scenario 2: Ansible script could not connect to one or more hosts to do ping tests		=======================================================================================	


				Below error is seen during "TASK [Gathering Facts]" task that the IP is not reachable from machine where ansible is executed.

					TASK [Gathering Facts] **********************************************************************************************************************************
					ok: [10.11.12.147]
					ok: [10.11.12.141]
					ok: [10.11.12.240]
					fatal: [10.11.12.244]: UNREACHABLE! => {"changed": false, "msg": "Failed to connect to the host via ssh: ssh: connect to host 10.11.12.244 port 22: Connection timed out", "unreachable": true}

				Below status is listed under PLAY RECAP task that IP is not reachable.

					PLAY RECAP **********************************************************************************************************************************************
					10.11.12.141             : ok=3    changed=1    unreachable=0    failed=0
					10.11.12.147             : ok=3    changed=1    unreachable=0    failed=0
					10.11.12.240             : ok=4    changed=2    unreachable=0    failed=0
					10.11.12.244             : ok=0    changed=0    unreachable=1    failed=0
							

		===============================================================================================
		scenario 3:	There is ping failure to few network interfaces of few hosts from particular host.		===============================================================================================	

				Below is the failure seen under TASK [ping all IP addresses] when interface IP of any defined host is not reachable from particular host. In this example host with IP 10.11.12.240 is not able to ping 10.11.12.141 IP interface of other defined host.

						ok: [10.11.12.141] => (item=172.16.0.39)
						ok: [10.11.12.147] => (item=172.16.0.39)
						failed: [10.11.12.240] (item=10.11.12.141) => {"changed": true, "cmd": "ping 10.11.12.141 -c 1", "delta": "0:00:10.003095", "end": "2019-11-05 04:57:54.597819", "item": "10.11.12.141", "msg": "non-zero return code", "rc": 1, "start": "2019-11-05 04:57:44.594724", "stderr": "", "stderr_lines": [], "stdout": "PING 10.11.12.141 (10.11.12.141) 56(84) bytes of data.\n\n--- 10.11.12.141 ping statistics ---\n1 packets transmitted, 0 received, 100% packet loss, time 0ms", "stdout_lines": ["PING 10.11.12.141 (10.11.12.141) 56(84) bytes of data.", "", "--- 10.11.12.141 ping statistics ---", "1 packets transmitted, 0 received, 100% packet loss, time 0ms"]}
						ok: [10.11.12.240] => (item=192.168.5.177)
						ok: [10.11.12.240] => (item=192.168.5.240) 
						
				Below status is listed under PLAY RECAP task that there is a failure (ping result) on 10.11.12.240 defined host.

						PLAY RECAP **********************************************************************************************************************************************
						10.11.12.141             : ok=3    changed=1    unreachable=0 failed=0
						10.11.12.147             : ok=3    changed=1    unreachable=0 failed=0
						10.11.12.240             : ok=3    changed=2    unreachable=0 failed=1