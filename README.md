# Adding Proxmox to your netboot.xyz installation

To get Proxmox installable via netboot.xyz, you have a couple choices.  You will need to host the assets to install Proxmox, as the ISO they provide won't work directly with netboot. You can then create a netboot-custom repsoitory in GitHub that you can easily chain in from the hosted netboot.xyz implementation.  Or you can self host netboot.xyz and update the scripts locally.  I've chosen to do the later, and am running [netboot.xyz in a container](https://hub.docker.com/r/linuxserver/netbootxyz).

This has been tested as of May 2021 with Proxmox v.6.4-1 and netboot.xyz v.2.0.38.


## Creating Proxmox Assets for PXE
Take the current [Proxmox ISO](https://www.proxmox.com/en/downloads/category/iso-images-pve) and run the `pve-iso-2-pxe.sh` script that you can find at [morph027/pve-iso-2-pxe](https://github.com/morph027/pve-iso-2-pxe).

You'll need to host the assets the script derives from the ISO.  Host them somewhere that is reachable from the machine that you're netboot'ing from.

The assets should be stored in the following location:

```/proxmox/<<version>>/```

So at the time of this writing, I have the following two files hosted at the following paths:

```
/proxmox/6.4-1/linux26
/proxmox/6.4-1/initrd.iso.img
```




## Adding Proxmox to the Menu Setup

### proxmox.ipxe
First add the ```proxmox.ipxe``` file content from this repo to the location with the rest of your menus.  If you are using the containerized version of netboot.xyz from above, this is easily achieved by creating a new menu in the netboot.xyz interface on port 3000.

### menu.ipxe
In the `menu.ipxe` file, I added an item for Proxmox.  I show the Proxmox option regardless of which architecture has been booted to, but have only tested it with x86 architectures.  Add this line to add the Proxmox option to the main menu:

```
item proxmox ${space} Proxmox ||
```

You can see an example ```menu.ipxe``` in this repo (line 39).

### boot.cfg
Finally, I also added a variable to the `boot.cfg` file for the location of my local "live endpoint".  This allows me to easily swap out whichever menus/oses that I want to install from my LAN very easily.  Add the following around the top of your `boot.cfg` file.


```
# set location of local netboot.xyz live assets
set live_endpoint_local http://<<ip-address>>:<<port-number>>
```

Where `<<ip-address>>` and `<<port-number>>` have been filled in with the ip and port of your local asset store/cache.  For example:


```
# set location of local netboot.xyz live assets
set live_endpoint_local http://192.168.1.2:8080
```

The ```proxmox.ipxe``` file utilizes the ```live_endpoint_local``` variable by replacing the value of ```live_endpoint```.

You can see an example ```boot.cfg``` in this repo (lines 16-17).


## Boot and Install!

Now you should be able to boot using PXE, to get to iPXE, to get to this updated netboot menu that now includes Proxmox!  Happy VM'ng!
