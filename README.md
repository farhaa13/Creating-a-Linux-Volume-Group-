# Creating-a-Linux-Volume-Group-
Creating a Linux Volume Group with AWS EBS Volumes
This repository provides a step-by-step guide for creating a flexible and scalable storage solution on an AWS EC2 instance by combining multiple EBS (Elastic Block Store) volumes into a single LVM (Logical Volume Manager) Volume Group.

This approach is ideal for scenarios where you need storage that can be easily resized or that exceeds the maximum size of a single EBS volume.

# Part 1: AWS Setup (Creating and Attaching EBS Volumes)
In this part, we will create two new EBS volumes and attach them to our EC2 instance.

Create EBS Volumes

1.Navigate to the EC2 Dashboard in your AWS Management Console.

2.In the left-hand menu, go to Elastic Block Store > Volumes.

3.Click Create volume.

4.Configure the first volume:

Volume type: General Purpose SSD (gp2 or gp3) is a good default.

Size (GiB): Enter a size, for example, 10.

Availability Zone: CRITICAL! You must select the same Availability Zone as your EC2 instance.

Tags: Add a tag (e.g., Name: lvm-disk-1) to keep things organized (OPTIONAL).

5.Click Create volume.

<img width="1628" height="329" alt="linux-vol1" src="https://github.com/user-attachments/assets/986d541d-603e-4857-84dd-b073f92d2a4e" />

2.Attach EBS Volumes to Your EC2 Instance

1.In the Volumes list, select your first volume (lvm-disk-1).

2.Click Actions > Attach volume.

3.In the Instance field, select your EC2 instance from the list.

4.Note the Device name suggested by AWS (e.g., /dev/sdf).

5.Click Attach volume.

<img width="1920" height="852" alt="linux-vol2" src="https://github.com/user-attachments/assets/6f67a7d2-cb4f-49f1-b979-33ae2a34e8ab" />

# Part 2: Linux Setup (Configuring LVM on the EC2 Instance)
Now, we will SSH into the instance and configure LVM.

1. Connect to Your EC2 Instance
Connect to your instance using your SSH key.

```
ssh -i /path/to/your-key.pem ec2-user@your-instance-public-ip
```

2. Identify the Attached Volumes
Run the lsblk command to list the available block devices. You should see the new disks you attached.

Note: AWS may present device names like /dev/sdf as /dev/xvdf inside the operating system. Use the names shown by lsblk.

```
lsblk
```

Example output:

<img width="1897" height="265" alt="linux-vol3" src="https://github.com/user-attachments/assets/adcde769-2c25-4a3d-8db2-b09f799f4cec" />

Here, xvdf and xvdg are our new 10 GiB and 12 GiB volumes respectively.

3. Create Physical Volumes (PVs)
Initialize the disks as LVM Physical Volumes.

```
sudo pvcreate /dev/xvdf /dev/xvdg
sudo pvs
```

<img width="1901" height="165" alt="linux-vol4'" src="https://github.com/user-attachments/assets/4ec10b6d-fa4c-45a0-a17c-669d3ab9ac7d" />

Verify with ``` sudo pvs ```

4. Create the Volume Group (VG)
Group the PVs into a single storage pool called a Volume Group.

```
sudo vgcreate ebs_vg /dev/xvdf /dev/xvdg
sudo vgs
```

<img width="1903" height="165" alt="linux-vol5" src="https://github.com/user-attachments/assets/f01aafe2-2a01-4ccf-93aa-3a89eecc62d4" />

Verify with ``` sudo vgs ```. You should see your new VG with a total size of ~22 GiB.












