## Setting up Cisco Modeling Labs on Windows

[This guy is AWESOME at showing how to set up CML](https://www.youtube.com/watch?v=qKiLpN-M4Xc&ab_channel=DavidBombalTech)

Buy the personal edition of CML.

Download CML software. We're using the .ova file
Download iso.
Extra the iso.

Download VMware workstation player.

Open VMware player.

VT-X or AMD bios must be enabled for virtualizion; mine already was.

File > Open > select the .ova file
Name it what you like.
Edit Virtualization Settings.

Processors > check "Virtualize Intel VT-x/EPT or AMD-V/RVI"

CD/DVD > Use ISO image file > select the iso file you downloaded/extracted
Also on CD/DVD, check the "Connect at power on."

Enter through the welcome messages.
Choose a username/password, write those down.

Accept DHCP for configuring IPv4 address (for the sake of getting this working)

When CML2 is displaying in ASCII characters, you'll see `Access the CML UI from` and an IP address will be provided. IF the address is not `192.168.x.x`, you'll need to go back to VMWare player.

Network Adapter > Network Connection > check `NAT: Used to share the host's IP address.`

Check if the CML2 screen now shows a correct IP address. If not, restart the VM.

Open the IP address in your browser, log in with the username/password you made earlier.

Click "System Health" error in bottom right, add your license where prompted.

You're good to go!

## Installing MDD Tooling

Go to home directory.

`cd`

Clone the repo.

`/home/student/` `git clone https://github.com/model-driven-devops/mdd.git`

Go into repo.

`/home/student/` `cd mdd`

Open the envvars file and edit it as follows:

`/home/student/mdd` `vim ~/mdd/envvars`

```
export ANSIBLE_COLLECTIONS_PATH=./
export ANSIBLE_PYTHON_INTERPRETER=${VIRTUAL_ENV}/bin/python 
export CML_HOST=<use-ip-address-you-collected-earlier>       # <--- ONLY
export CML_USERNAME=<use-username-you-chose-earlier>         # <--- EDIT THESE
export CML_PASSWORD=<use-password-you-chose-earlier>         # <--- FOUR
export CML_LAB=<name-this-whatever-you-like>                 # <--- LINES!
export CML_VERIFY_CERT=false
```

Source the file to apply the variables you made.

`/home/student/mdd` `. ./envars`

You can check if it worked:

`/home/student/mdd` `echo $CML_LAB`

The playbook is written to expect execution inside a python venv, so set one up.

`/home/student/mdd` `python3 -m venv venv-mdd`

Now activate it.

`/home/student/mdd` `. ./venv-mdd/bin/activate`

Now that you're in a python venv, install the python modules needed for our playbooks:

`/home/student/mdd<venv>` `pip3 install -r requirements.txt` 

We now need to reactive the venv to ensure we're using the newly installed ansible.

`/home/student/mdd<venv>` `deactivate`

Now get back into the venv (whee)

`/home/student/mdd` `. ./venv-mdd/bin/activate`

There are some collections we'll need from ansible galaxy, install those now.

`/home/student/mdd<venv>` `ansible-galaxy collection install -r requirements.yml`

Set our collections path appropriately.

`/home/student/mdd<venv>` `export ANSIBLE_COLLECTIONS_PATH=./`

You should now be able to run a test playbook and collect info about one of our hosts:

`/home/student/mdd<venv>` `ansible-playbook ciscops.mdd.show --limit=hq-rtr1`

```

PLAY [network] *****************************************************************

TASK [ciscops.mdd.data : Combine the MDD Data] *********************************
ok: [hq-rtr1]

TASK [ciscops.mdd.data : Assign mdd_data] **************************************
ok: [hq-rtr1]

TASK [debug] *******************************************************************
ok: [hq-rtr1] => 
  mdd_data:
    mdd:openconfig:
      openconfig-interfaces:interfaces:
        openconfig-interfaces:interface:
        - openconfig-interfaces:config:
            openconfig-interfaces:enabled: true
[truncated]
```

## Installing the MDD Topology

The topology file that will describe how to build your network in CML is not correct (some of the labels are wrong). Replace those:

`/home/student/mdd<venv>` `sed -i 's/enp0s2/ens2/g; s/enp0s3/ens3/g' ~/mdd/files/arch4_csr_pop.yaml.j2`

Ensure you're in the correct directory and you're in the venv! See previous steps if not in venv.

`cd ~/mdd`

Run the playbook to build the CML topology.

`/home/student/mdd<venv>` `ansible-playbook cisco.cml.build -e startup='host' -e wait='yes'`

You can watch everything get built in your CML lab in your browser. When you inevitably run out of resources because this setup is a huge resource hog, you can tear it all down like so:

`/home/student/mdd<venv>` `ansible-playbook cisco.cml.clean`

[After solving the hardware issue, will return to this page to continue](https://github.com/model-driven-devops/mdd/blob/main/exercises/deploy-topology.md)
