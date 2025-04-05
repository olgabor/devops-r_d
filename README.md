# devops-r_d

## Lecture 3 - Virtualization/VBox

# UTM Virtual Machine Setup Guide

### Download UTM from [UTM Official Website](https://mac.getutm.app/)

### Create a new VM
- **Type:** Linux
- **Version:** Ubuntu (64-bit)

![Screenshot Placeholder](./lecture_3/images/created_vm.png)

## OS Installation
Download the latest Ubuntu LTS version (Long Term Support) ISO image from the official website: [Ubuntu Download](https://ubuntu.com/download)

### Install Ubuntu on VM
Follow the installation steps to set up Ubuntu on the virtual machine.

## Saving and Restoring VM State
Created files under `~./Document`

![Screenshot Placeholder](./lecture_3/images/save_vm_state.png)

### VM Restored After Reboot
![Screenshot Placeholder](./lecture_3/images/restore_vm.png)

## Change VM Parameters Through the Graphical Interface

### Before Storage and CPU Update
![Screenshot Placeholder](./lecture_3/images/before_update.png)

### After VM Settings Update
![Screenshot Placeholder](./lecture_3/images/after_update.png)

# Resize Disk Size 

## Original size 

![Screenshot Placeholder](./lecture_3/images/original_disk_space.png)

UTM application -> Select VM → Click Settings -> Edit 
Drives tab -> Find the disk you want to change (vda) and increase the size (Size) - enter a new value in the size field (20 GB). Click Save.

### Check if disk size has been updated on VM
lsblk 

![Screenshot Placeholder](./lecture_3/images/updated_disk_size.png)