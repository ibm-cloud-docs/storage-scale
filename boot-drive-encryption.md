---

copyright:
  years: 2022, 2023, 2024
lastupdated: "2024-06-13"

keywords: 

subcollection: storage-scale

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:external: target="_blank" .external}
{:pre: .pre}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:step: data-tutorial-type='step'}
{:table: .aria-labeledby="caption"}

# Enabling boot drive encryption for Bare Metal Server
{: #boot-drive-encryption}

Enable the boot drive encryption for IBM Bare Metal Servers for VPC to use a local disk drive for boot. This ensures that the responsibility for encryption of the drive is in the end-users hands. Boot drive encryption encrypts the drive using LUKS and leverages the local Trusted Platform Module (TPM) for key management.

|Boot drive variable|	Description|	Example value|
|-------------|------------|--------------|
|`bms_boot_drive_encryption`|Enable the boot drive encryption for the bare metal server. Select true or false	|false|

## Verifying boot drive encryption on the Bare Metal Server
{: #boot-drive-encryption-verify}

To verify if the boot drive is encrypted, user can run the `lsblk` command to check the root type partition mounted at `/` is set to crypt.

```
[root@scale-bm-drive-strg-002 ~]# lsblk
NAME        MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINT
sda           8:0    0 894.2G  0 disk
├─sda1        8:1    0     1M  0 part
├─sda2        8:2    0   100M  0 part  /boot/efi
├─sda3        8:3    0  99.9G  0 part  /boot
├─sda4        8:4    0   1.3M  0 part
└─sda5        8:5    0 794.2G  0 part
└─root    253:0    0 794.2G  0 crypt /
nvme2n1     259:0    0   2.9T  0 disk
└─nvme2n1p1 259:13   0   2.9T  0 part
nvme3n1     259:1    0   2.9T  0 disk
└─nvme3n1p1 259:18   0   2.9T  0 part
nvme4n1     259:2    0   2.9T  0 disk
└─nvme4n1p1 259:10   0   2.9T  0 part
nvme5n1     259:3    0   2.9T  0 disk
└─nvme5n1p1 259:14   0   2.9T  0 part
nvme7n1     259:4    0   2.9T  0 disk
└─nvme7n1p1 259:15   0   2.9T  0 part
nvme0n1     259:5    0   2.9T  0 disk
└─nvme0n1p1 259:11   0   2.9T  0 part
nvme1n1     259:6    0   2.9T  0 disk
└─nvme1n1p1 259:9    0   2.9T  0 part
nvme6n1     259:7    0   2.9T  0 disk
└─nvme6n1p1 259:8    0   2.9T  0 part
[root@scale-bm-drive-strg-002 ~]
[root@scale-bm-drive-strg-002 ~]
```

Once the boot drive is encrypted, it is recommended to update the recovery passphrase and store it. It is also important that you do not leave the recovery passphrase the same as initial setup as this data is not secure.

The initial passphrase used in initial setup is `n0tsecret`.
{: note}

Following are the steps to rotate the LUKS Encryption Keys:

* List the TPM keys:

```
[root@scale-boot-test-strg-001 ~]# clevis luks list -d /dev/sda5
1: tpm2 '{"hash":"sha256","key":"rsa"}'
```

* Regenerate the TPM key (rotate it):

```
[root@scale-boot-test-strg-002 ~]# clevis luks regen -d /dev/sda5 -s 1
Regenerating binding (device /dev/sda5, slot 1):
Pin: tpm2, Config: '{"hash":"sha256","key":"rsa"}'
Do you want to proceed? [ynYN] y
Binding regenerated successfully
```

* Change the recovery key:

```
[root@scale-boot-test-strg-002 ~]# cryptsetup luksChangeKey /dev/sda5
Enter passphrase to be changed: 
Enter new passphrase: 
Verify passphrase: 
```

* Test the passphrase:

```
[root@scale-boot-test-strg-002 ~]# cryptsetup luksOpen --test-passphrase /dev/sda5
Enter passphrase for /dev/sda5: 
```

* Back up the key:

```
[root@scale-boot-test-strg-002 ~]# cryptsetup luksHeaderBackup /dev/sda5 --header-backup-file luksbackup-sensitive.raw
```

* Restore the key:

```
[root@scale-boot-test-strg-002 ~]# cryptsetup luksHeaderRestore /dev/sda5 --header-backup-file luksbackup-sensitive.raw

WARNING!
=========

Device /dev/sda5 already contains LUKS2 header. Replacing header will destroy existing keyslots.

Are you sure? (Type 'yes' in capital letters): YES
```