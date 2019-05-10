# Virtual Machine Configuration and Operations

A virtual machine is a software computer that, like a physical computer, runs an operating system and applications. The virtual machine consists of a set of specification and configuration files and is backed by the physical resources of a host. Each virtual machine encapsulates a complete computing environment and runs independently of the underlying hardware. 

Starting with vSphere 6.5, you can configure virtual machine settings and perform power operations through the vSphere Automation SDKs.

## Filtering Virtual Machines 

You can retrieve a list of virtual machines that match a specific filter or a group of up to one thousand virtual machines available in a specific vCenter Server instance. 

You can retrieve a list of up to one thousand virtual machine IDs for a single vCenter Server instance by filtering them based on a specific requirement, such as a host, cluster, datacenter, or resource pool on which the VMs are running. 

Call the list methods of the VM service to retrieve only a list of the virtual machines that match your specific criteria. The method takes as parameter the VMTypes.FilterSpec instance that you can use to describe the virtual machine filter.

## Creating a Virtual Machine 

You can create a virtual machine by using the VM.create method. The method takes as parameter a CreateSpec instance that allows you to specify the attributes of the virtual machine. 

To create a virtual machine you must specify the virtual machine attributes by using the CreateSpec class. For example, you can specify a name, boot options, networking, and memory for the virtual machine. See [Configuring a Virtual Machine](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-D78FAD98-F11C-4B66-9460-9CA46A7F4136.html#GUID-D78FAD98-F11C-4B66-9460-9CA46A7F4136). 

All attributes are optional except the virtual machine placement information that you must provide by using the PlacementSpec class. Use the virtual machine placement specification to set the datastore, cluster, folder, host, or resource pool of the created virtual machine and make sure that all these vSphere objects are located in the same data center in a vCenter Server instance. 

## Configuring a Virtual Machine 

You can configure a virtual machine during creation. You can also reconfigure an existing virtual machine by adding or changing the type of the storage controllers, configure the virtual disks, boot options, CPU and memory information, and networks.

### Name and Placement 

You specify the display name and the location of the virtual machine by using the CreateSpec and PlacementSpec classes. 

When you create your virtual machine, use the setName method of the CreateSpec class to pass as argument the display name of the virtual machine. 

You must create also a PlacementSpec instance that describes the location of the virtual machine in regards to the resources of a given vCenter Server instance. Use the setPlacement\(PlacementSpec placement\) method of the CreateSpec class to set the placement information for the virtual machine. You can set one or all of the following vSphere resources: datastore, cluster, folder, host, and resource pool.

### Boot Options 

You can configure the boot options of a virtual machine by using the setBoot\(CreateSpec boot\) method of the CreateSpec class. 

The method takes as argument the BootTypes.CreateSpec class. You can select one of the following settings when booting the virtual machine: 

| ■  | Delay - Indicates a delay in milliseconds before starting the firmware boot process when the virtual machine is powered on.  |
| :--- | :--- |
| ■  | Retry - Indicates whether the virtual machine automatically retries to boot after a failure.  |
| ■  | Retry delay - Indicates a delay in milliseconds before retrying the boot process after a failure.  |
| ■  | Enter setup mode - If set to true, indicates that the firmware boot process automatically enters BIOS setup mode the next time the virtual machine boots. The virtual machine resets this flag to false once it enters setup mode.  |
| ■  | EFI legacy boot - If set to true, indicates that the EFI legacy boot mode is used. |

### Operating System 

The guest operating system that you specify affects the supported devices and available number of virtual CPUs. 

You specify the guest operating system by using the setGuestOS\(GuestOS guestOS\) method of the VMTypes.CreateSpec class. The GuestOSclass defines the valid guest OS types that you can use to configure a virtual machine.

### CPU and Memory 

The CreateSpec class allows you to specify the CPU and memory configuration of a virtual machine. 

To change the CPU and memory configuration settings, use the CpuTypes.UpdateSpec and MemoryTypes.UpdateSpec classes. CPU Configuration 

You can set the number of CPU cores in the virtual machine by using the setCount method of the CpuTypes.UpdateSpec class. The supported range of CPU cores depends on the guest operating system and virtual hardware version of the virtual machine. If you set CpuTypes.Info.getHotAddEnabled\(\) and CpuTypes.Info.getHotRemoveEnabled\(\) to true, you allow virtual processors to be added or removed from the virtual machine at runtime. Memory Configuration 

You can set the memory size of a virtual machine by using the setSizeMiB method of the MemoryTypes.UpdateSpec class. The supported range of memory sizes depends on the configured guest operating system and virtual hardware version of the virtual machine. If you set MemoryTypes.UpdateSpec.setHotAddEnabled\(\) to true while the virtual machine is not powered on, you enable adding memory while the virtual machine is running.

### Networks 

You configure network settings so that a virtual machine can communicate with the host and with other virtual machines. When you configure a virtual machine, you can add network adapters \(NICs\) and specify the adapter type. 

You can add virtual Ethernet adapters to a virtual machine by using the VMTypes.CreateSpec.setNics method. Pass as argument a List of EthernetTypes.CreateSpec objects that provide the configuration information of the created virtual Ethernet adapters. You can set the MAC address type to EthernetTypes.MacAddressType.MANUAL, EthernetTypes.MacAddressType.GENERATED, or EthernetTypes.MacAddressType.ASSIGNED. Select MANUAL to specify the MAC address explicitly. 

You can specify also the physical resources that back a virtual Ethernet adapter by using the EthernetTypes.BackingSpec.setType method. The method takes as argument one of the following types: EthernetTypes.BackingType.STANDARD\_PORTGROUP, HOST\_DEVICE, DISTRIBUTED\_PORTGROUP, or OPAQUE\_NETWORK.

## Performing Virtual Machine Power Operations 

You can start, stop, reboot, and suspend virtual machines by using the methods of the Power class. 

A virtual machine can have one of the following power states: 

| ■  | PowerTypes.State.POWERED\_ON - Indicates that the virtual machine is running. If a guest operating system is not currently installed, you can perform the guest OS installation in the same way as for a physical machine.  |
| :--- | :--- |
| ■  | PowerTypes.State.POWERED\_OFF - Indicates that the virtual machine is not running. You can still update the software on the physical disk of the virtual machine, which is impossible for physical machines.  |
| ■  | PowerTypes.State.SUSPENDED - Indicates that the virtual machine is paused and can be resumed. This state is the same as when a physical machine is in standby or hibernate state.  |

To perform a power operation on a virtual machine, you can use one of the methods of the Power class. Before you call one of the methods to change the power state of a virtual machine, you must first check the current state of the virtual machine by using the Power.get method. Pass as argument the virtual machine identifier. 

Following is a list if the power operations: 

| ■  | Power.start - Powers on a powered off or suspended virtual machine. The method takes as argument the virtual machine identifier.  |
| :--- | :--- |
| ■  | Power.stop - Powers off a powered on or suspended virtual machine. The method takes as argument the virtual machine identifier.  |
| ■  | Power.suspend - Pauses all virtual machine activity for a powered on virtual machine. The method takes as argument the virtual machine identifier.  |
| ■  | Power.reset - Shuts down and restarts the guest operating system without powering off the virtual machine. Although this method functions as a stop method that is followed by a start method, the two operations are atomic with respect to other clients, meaning that other power operations cannot be performed until the reset method completes. |

