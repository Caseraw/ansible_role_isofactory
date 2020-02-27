# Ansible ISO factory

Making a custom ISO image for Enterprise Linux distributions.

[<img src="https://img.shields.io/ansible/role/44669">](https://galaxy.ansible.com/caseraw/ansible_role_chrony) [<img src="https://img.shields.io/ansible/role/d/44669">](https://galaxy.ansible.com/caseraw/ansible_role_chrony) [<img src="https://img.shields.io/ansible/quality/44669">](https://galaxy.ansible.com/caseraw/ansible_role_chrony)

- [Ansible ISO factory](#ansible-iso-factory)
  - [License](#license)
  - [Author Information](#author-information)
  - [Requirements](#requirements)
  - [Dependencies](#dependencies)
  - [Compatibility](#compatibility)
  - [Role Variables](#role-variables)
  - [Example Playbook](#example-playbook)
  - [Build files](#build-files)
  - [Task tags](#task-tags)
  - [Useful shell commands](#useful-shell-commands)
  - [Additional documentation resources](#additional-documentation-resources)
  - [Testing the role and the ISO](#testing-the-role-and-the-iso)
  - [Useful links](#useful-links)

## License

MIT / BSD

## Author Information

- Made and maintained by: [Kasra Amirsarvari](https://www.linkedin.com/in/caseraw)
- Ansible Galaxy community author: <https://galaxy.ansible.com/caseraw>

## Requirements

- Run on a properly configured and up-to-date Linux distribution.
- Ensure proper user permissions to mount ISO files and to edit their contents.
- Ensure sufficient storage space on the machine to work with multiple ISO files including their extracts.
- Ensure a "**files**" directory exists within the Ansible project directory as it serves as the default location where several folders and files will me made.
- Have at least 1 ISO file ready, preferably from the original vendor.

## Dependencies

- Ensure installed packaged:
  - Command: **mkisofs** - Package: **genisoimage**
  - Command: **isohybrid** - Package: **syslinux**
  - Command: **implantisomd5** - Package: **isomd5sum**
  - Command: **rsync** - Package: **rsync**

## Compatibility

Compatible with the following list of operating systems:

- CentOS 7
- CentOS 8
- RHEL 7.x
- RHEL 8.x

## Role Variables

| Variable name | Description |
|---------------|-------------|
| role_isofactory_arena_persistent_build_directory | Optional absolute path to persistent storage location for extracting ISO files. |
| role_isofactory_arena_persistent_iso_files_directory | Optional absolute path to persistent storage location for storing ISO files. |
| role_isofactory_arena_persistent_iso_mount_directory | Optional absolute path to persistent storage location for mounting an ISO. |
| role_isofactory_arena_persistent_build_files_directory | Optional absolute path to persistent storage location for storing build files. |
| role_isofactory_iso_file_input | Name of an existing/original ISO file. |
| role_isofactory_iso_file_output | Name of the to be created new ISO file |
| role_isofactory_iso_file_output_checksum | Name of the file that will contain the checksum of the to be created new ISO file. |
| role_isofactory_iso_volume_label | The label for the ISO. This should strictly be the same as shown in the GRUB menu configuration file. |
| role_isofactory_cleanup_unmount_iso | Wether to unmount the ISO after the creation of a new ISO is done. |
| role_isofactory_cleanup_arena_build_directory | Wether to remove the extracted ISO files after the creation of a new ISO is done. |
| role_isofactory_build_file_list | A list of files to put in or to remove from the contents of an ISO file. |

## Example Playbook

```yaml
---
- name: Create a custom bootable ISO based on an original ISO
  hosts: localhost
  become: false
  gather_facts: false
  tasks:
    - import_role:
        name: ansible_role_isofactory
      vars:
        role_isofactory_iso_file_input: rhel-server-7.7-x86_64-dvd.iso
        role_isofactory_iso_file_output: rhel-server-7.7-x86_64-dvd-custom_base01.iso
        role_isofactory_iso_file_output_checksum: rhel-server-7.7-x86_64-dvd-custom_base01.txt
        role_isofactory_iso_volume_label: RHEL-7.7 Server.x86_64
        role_isofactory_cleanup_unmount_iso: True
        role_isofactory_cleanup_arena_build_directory: True
        role_isofactory_build_file_list:
          - src: RHEL/RHEL7x_ks_legacy_boot.cfg
            dest: isolinux/ks.cfg
            state: present
            action: copy
            owner: root
            group: root
            mode: 0444
          - src: RHEL/RHEL7x_ks_uefi_boot.cfg
            dest: EFI/BOOT/ks.cfg
            state: present
            action: copy
            owner: root
            group: root
            mode: 0444
          - src: RHEL/RHEL77_isolinux.cfg
            dest: isolinux/isolinux.cfg
            state: present
            action: copy
            owner: root
            group: root
            mode: 0444
          - src: RHEL/RHEL77_grub.cfg
            dest: EFI/BOOT/grub.cfg
            state: present
            action: copy
            owner: root
            group: root
            mode: 0444

...
```

## Build files

Build files are specified files used to place within or remove from the ISO.  
These files can be almost everything such as:

- Kickstart configuration files
- GRUB bootmenu
- RPM packages

Examples are made available at: [docs/example_build_files](docs/example_build_files/)

## Task tags

| Tag name | Description |
|----------|-------------|
| role_isofactory_tag_verify | Check if required variables are filled. |
| role_isofactory_tag_setup_working_dir | Setting up the working area. |
| role_isofactory_tag_mount_iso | Mounting the original ISO. |
| role_isofactory_tag_copy_iso_contents | Extracting the contents of the original ISO. |
| role_isofactory_tag_manage_build_files | Add or remove files into or from the extracted contents of the original ISO. |
| role_isofactory_tag_build_iso | Create a new ISO. |
| role_isofactory_tag_cleanup | Cleanup the working area. |

## Useful shell commands

N/A

## Additional documentation resources

N/A

## Testing the role and the ISO

At the moment the role is tested by generating a new ISO and using to install a new VM from scratch.  
The generation of the ISO file has been done manually on:

- Ubuntu 18.04.x workstation
- Fedora 30 workstation
- Fedora 31 workstation
- RHEL7 server

Installing a new VM from scratch using the ISO file has been tested on:

- Libvirt
- Red Hat Virtualization
- VMware

## Useful links

- GitHub repository: <https://github.com/Caseraw/ansible_role_chrony>
- Ansible Galaxy role: <https://galaxy.ansible.com/caseraw/ansible_role_chrony>
