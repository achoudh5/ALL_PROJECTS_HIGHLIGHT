---
title = "Configuring Ixia with Linux Chassis"
---
# What is IXIA and what is does ?

IXIA provides *application performance, testing, visibility, and security solutions* to strengthen applications across **physical and virtual networks**. IXIA’s Network Test Solutions enables the network equipment builders to perform “pre-deployment testing” by testing their newly developed equipment through simulation in a complex network dealing with real-time traffic. 

It provides with an “end-to-end approach” to justify the built network equipment, determine the performance of the built “networks and data centers”. IXIA has released three products, namely, IxNetwork, IxLoad, IxChariot for testing the built networks in a test bed at various layers. All these three products are sold in the form of physical and virtual load modules, in the form of a chassis.  We are focusing on the IxNetwork.

## Configuring the Ixia

*This was performed on a chassis with a Load Module XGS12-HS, running IxOS 8.40.1400.11*

### Steps

1. Rack the Ixia
2. Perform initial setup

## Initial Network Setup

Plug in peripherals (monitor, keyboard, & mouse) and power on. This includes
whatever network cabling is needed.

The system will boot into a CLI framework. If it boots into a GUI, the
"Getting Started" guide in the manuals on the included USB should help (see
section "Install" or "Installation").

For the headless CLI configuration, do the following:

1. Login using the username and password `admin`.
  a. *We can also ssh into the Ixia once we assign it an ip which is 172.22.60.3 for Raleigh.*
     
     `ssh admin@172.22.60.3`
     `Password:- admin`

2. Enter the chassis subsystem with the command `enter chassis`. You will see all the information in tabular form.
You can also use below steps.
3. Make the sure the chassis is ready with `show chassis status`--it should show
`STATUS: READY`.
4. Find the network interface to configure: `show ip` or `show ip --all`. It
will probably be `mgmt0`.
5. The latest version of IxOS can be checked with `show ixos available-updates`. If it prompts no updates available then, you are good to go but if there is any update avaialble (update name would be an ip) then :-
   
   a. `Set proxy --server WEBPROXY --ip <ip_address> --port 8000`
   
   b. `Install ixos <ip-address of the available update>`
6. Another important thing is to check if the IxTCL server version is installed.
  
   a. `Show ixtclserver active-version` would tell if the TCL server is there or not.  
7. Configure the network interface from step 4 to use DHCP: `set ip <interface>
DHCP`.
8. Grab the MAC address of the system: `show ip`.
9. Follow the standard procedure to assign the system an IP address. For
example:
   
   a. Contact a network administrator and reserve an IP in the correct range.
   
   b. Fork, edit, and PR the DHCP configuration (located in the
   infra/pxe-configs repo)
   
   c. Use `ping` and `ssh` to test the connection. The system may need rebooted
   (at which time `show ip` will help verify the IP).
10. (Optional): Set the system time and date.
   
    a. Set the timezone: `set time-zone` and follow the onscreen prompts. This
       should be doable over ssh if steps 1-7 were achieved.
   
    b. If necessary, use `set time` and `set date` to correct the time and date.
   
    c. Restart the ixServer with `restart-service ixServer`

If at __any__ stage the MAC address of the system changes (this has happened
before), the DHCP will need to be edited to reflect the correct MAC address.


Before any changes are being made to the 
An example of how a VPLS will look like configured on a SR
 
Use “admin display-config” to check the configuration of the sr7
Use “configure service vpls X” to update a vpls, where X represents which vpls you want to change
And “configure service vpls X create” to create a new vpls, where X represents the next available vpls
 
vpls 46 customer 1 create
    description "mvnsghw08 ixia2 ovs1p4 ovs2p4"
    service-mtu 9194
    disable-learning
    stp
        shutdown
    exit
    sap 4/1/34 create
        description "ixia 2"
    exit
    sap 4/1/35 create
        description "ovs1 p4"
    exit
    sap 4/1/36 create
        description "ovs2 p4"
    exit
    no shutdown
Exit

Deciphering commands in the QA/infra repo :-
The ixia ports being used are connected to the SR(Service Router) , writing down somewhere what ports are connected to into the ransghw01.tcl file is the next step . The file can be found at QA/infra-->bin--->testbed-->ransghw01.tcl

The only ixia info needed in this file is what ports are used on the ixia chassis. then configs would have to be done to get the ixia ports on the correct vpls's.

## setGlobalVar ixVersion 6.90
## lappend ::TestTopo::ixiaChassisList "1 $ixChassisIp"
## lappend ::TestTopo::ixiaTclServer "$ixTclServerIp $ixTclServerPort"
## lappend ::TestTopo::ixiaPortList "1 12 1"
## lappend ::TestTopo::ixiaPortList "1 12 2"
## lappend ::TestTopo::ixiaPortList "1 12 3"
## lappend ::TestTopo::ixiaPortList "1 12 4"


The only place where the ports are defined. The first number is always going to be 1, the second number is the card, the third is the actual ports. preferably try to use 4 consecutive ports. Besides that update the ixversion number to 8.40 (here, latest till date)

The NICs on the SIMs don't actually go in the file, this saves you some time.
