This is the first of several labs putting into practice the knowledge I've learned while studying for the CompTIA Network+ N10-009 certification exam.

Here are the steps I took in order:

1. Download VirtualBox
2. Download Windows 10 iso
3. Create the Virtual Machine with the Windows 10 iso
   - I kept all default settings except for the name of the VM and the device's hostname
4. Let Windows 10 install
5. Find out my VM's current IPv4 settings
   - Open `cmd.exe`
   - Run `ipconfig /all`
6. Statically assign IPv4 settings
   - Open Control Panel
   - Click "Network and Internet"
   - Click "Network and Sharing Center"
   - Click "Change adapter settings"
   - Right-click my interface
   - Click "Properties"
   - Select "Internet Protocol Version 4 (TCP/IPv4)"
   - Click "Use the following IP address:"
   - Assign the IPv4 information displayed earlier from the `ipconfig /all` command (IP address, Subnet mask, Default gateway)
   - Click "Use the following DNS server addresses:"
   - Assign a preferred and alternate DNS server

7. Confirm changes
   - Navigate back to the `cmd.exe` window
   - Run `ipconfig /all`
   - Check for the following:
     - DHCP Enabled. . . . : No
     - IPv4 Address. . . . : 10.0.2.15(Preferred)
     - Subnet Mask . . . . : 255.255.255.0
     - Default Gateway. . .: 10.0.2.2
     - DNS Servers. . . . .: 1.1.1.1, 8.8.8.8

Because I was curious, I asked ChatGPT how to statically assign an IP address from the command line, and then assigned a new IPv4 address to test it out.

7. Statically configure IPv4 settings from the command line
   - Open `cmd.exe`
   - run `netsh interface ipv4 set address name="Ethernet" static 10.0.2.16 255.255.255.0`
   - run `netsh interface ipv4 set dns name="Ethernet" static 8.8.8.8`
   - run `netsh interface ipv4 add dns name="Ethernet" 1.1.1.1 index=2`
   - Confirm changes by running `ipconfig /all`

ChatGPT also told me that using `cmd.exe` to statically configure IPv4 settings using `netsh` is considered legacy practice, and that best practice is to use PowerShell.

8. Statically configure IPv4 settings from PowerShell
   - `Get-NetIPAddress -InterfaceAlias "Ethernet" -AddressFamily IPv4 |
  Remove-NetIPAddress -Confirm:$false`
   - `New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 10.0.2.15 -PrefixLength 24 -DefaultGateway 10.0.2.2`
   - `Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 1.1.1.1, 8.8.8.8`

9. Confirm changes in PowerShell
    - Run `Get-NetIPConfiguration`
    - Check for the following:
      - IPv4Address         : 10.0.2.15
      - IPv4DefaultGateway  : 10.0.2.2
      - DNSServer           : 1.1.1.1, 8.8.8.8
        
    - Run `Get-NetIPAddress 10.0.2.15`
    - Check for the following:
      - PrefixLength   : 24
        
    - Run `Get-NetIPInterface -InterfaceAlias "Ethernet" -AddressFamily "IPv4"`
    - Check for the following:
      - Dhcp - Disabled
        
    - Run `Get-DnsClientServerAddress -InterfaceAlias "Ethernet" -AddressFamily "IPv4"`
    - Check for the following:
      - ServerAddresses - {1.1.1.1, 8.8.8.8}

In the future, I will automate confirming network changes with a PowerShell script. For now, these manual checks are good practice.
