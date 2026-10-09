- Before Increase

<img width="891" height="468" alt="image" src="https://github.com/user-attachments/assets/2bedf6f2-fc06-4a7c-b0e6-08a02d4045d3" />

- Current volume size is 8 GB, now increase it to 30 GB. Once we modify, it will go to optimizing state, once done we can increase the size in vm.

<img width="1917" height="723" alt="image" src="https://github.com/user-attachments/assets/8f6a7a35-8e49-40a6-bf81-cb28881bf6d6" />
<img width="1914" height="679" alt="image" src="https://github.com/user-attachments/assets/abd34c87-487b-4f56-80a7-22e01339f53e" />
<img width="1919" height="386" alt="image" src="https://github.com/user-attachments/assets/97ade00e-561f-40f4-8f75-28eb1d36525f" />

- It came to `In-use` state, now we can resize.

<img width="1918" height="330" alt="image" src="https://github.com/user-attachments/assets/5cf63778-f4b1-49b6-8b52-fd9c4cabf25a" />

- Run the below command to resize

```bash
sudo apt install cloud-guest-utils
sudo growpart /dev/xvda 1
sudo resize2fs /dev/xvda1
```


- From the below image we can confirm that size is increased to 29G.

<img width="1784" height="960" alt="image" src="https://github.com/user-attachments/assets/42768fd0-8e39-4d96-af40-b2a07633908f" />


----
## for nvme01

```bash
df -hT /
sudo parted /dev/nvme0n1 print free
Fix
sudo growpart /dev/nvme0n1 1
```

<img width="1890" height="1006" alt="image" src="https://github.com/user-attachments/assets/7f2aeedd-97d9-47ba-9941-314fbcbd0fc9" />



The command

```
sudo growpart /dev/xvda 1
```

is used in Linux to expand partition 1 of the disk `/dev/xvda` so it can use newly available disk space.

This is commonly used on AWS EC2 when you increase an EBS volume size.

## 1. Understand each part

| Command     | Meaning                                        |
| ----------- | ---------------------------------------------- |
| `sudo`      | Runs the command with administrator privileges |
| `growpart`  | Expands a disk partition                       |
| `/dev/xvda` | The disk device                                |
| `1`         | The partition number to expand                 |

For example, `/dev/xvda1` refers to partition 1 on disk `/dev/xvda`.

Important: `/dev/xvda` is the whole disk, whereas `/dev/xvda1` is a partition on that disk.

## 2. Real-world AWS EC2 example

Suppose your EC2 instance has a 10 GB EBS volume, and you increase it to 20 GB in the AWS Console.

Before expanding

Disk `/dev/xvda`

20 GB

10 GB partition

10 GB unused

The disk is 20 GB, but the partition is still 10 GB.

After `growpart`

Disk `/dev/xvda`

20 GB

20 GB partition

The partition now uses the available disk space.

## 3. Is `growpart` alone enough?

Not always. You may also need to expand the filesystem so Linux can use the additional space.

First, check the disk and filesystem:

```
lsblk
df -hT
```

Then, for a typical EC2 instance using an ext4 filesystem:

```
sudo growpart /dev/xvda 1
sudo resize2fs /dev/xvda1
```

For an XFS filesystem, you typically use `xfs_growfs` on the mounted filesystem instead:

```
sudo xfs_growfs /
```

The correct filesystem command depends on your partition layout and mount point. Check `lsblk` and `df -hT` before running it.

DevOps interview answer: “When an EBS volume is increased, `growpart` expands the partition to use the additional disk space. Then I resize the filesystem, if required, so the operating system can use that space.”
