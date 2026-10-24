# Differences between RHEL 5, 6, 7, 8, 9, and 10

Comparison of Red Hat Enterprise Linux major versions for system administration. Component versions are the general-availability baseline unless a later minor release is named. The kernel version in a RHEL major is a long-lived fork: later minors add features without moving that version number to upstream mainline.

Dates below follow Red Hat’s published life cycle. Future dates are approximate and Red Hat can change them.

## Life cycle

| | RHEL 5 | RHEL 6 | RHEL 7 | RHEL 8 | RHEL 9 | RHEL 10 |
| --- | --- | --- | --- | --- | --- | --- |
| General availability | 15 Mar 2007 | 10 Nov 2010 | 10 Jun 2014 | 7 May 2019 | 18 May 2022 | 20 May 2025 |
| Upstream base | Fedora Core 6 | Fedora 12/13 | Fedora 19 | Fedora 28 | CentOS Stream 9 (Fedora 34) | CentOS Stream 10 |
| Full support ends | 8 Jan 2013 | 10 May 2016 | 6 Aug 2019 | 31 May 2024 | 31 May 2027 | 31 May 2030 |
| Maintenance (errata) ends | 31 Mar 2017 | 30 Nov 2020 | 30 Jun 2024 | 31 May 2029 | 31 May 2032 | 31 May 2035 |
| Status in Oct 2026 | Retired | Retired | Extended Life Phase | Maintenance Support | Full Support | Full Support |
| Optional longer fixes | ELS ended 30 Nov 2020 | ELS ended 30 Jun 2024 | ELS through 31 May 2029 (7.9 only) | Extended Life Cycle through 31 May 2033, then Long-Life add-on | Extended Life Cycle through 31 May 2036, then Long-Life add-on | Extended Life Cycle through 31 May 2039, then Long-Life add-on |

RHEL 5–7 used Maintenance Support 1 and 2, then an Extended Life Phase. The ELS add-on was required for a narrow set of new security fixes after maintenance ended. RHEL 8–10 use a 5-year Full Support phase and a 5-year Maintenance Support phase. The Extended Life Cycle that follows does not add new errata; continued fixes need a separate add-on.

## Platform

| Area | RHEL 5 | RHEL 6 | RHEL 7 | RHEL 8 | RHEL 9 | RHEL 10 |
| --- | --- | --- | --- | --- | --- | --- |
| Kernel | 2.6.18 | 2.6.32 | 3.10 | 4.18 | 5.14 | 6.12 |
| CPU / architectures | i386, x86_64, IA-64, Power, IBM Z | i386, x86_64, Power, IBM Z | x86_64, ARM (from 7.4), Power, IBM Z. No 32-bit install | x86_64, aarch64, ppc64le, s390x | x86-64-v2, ARMv8.0-A, POWER9, z14 | x86-64-v3, ARMv8.0-A, POWER10, z15 |
| 32-bit userspace | Full i386 install | Full i386 install | i686 multilib only | i686 multilib only | i686 multilib only | i686 multilib removed |
| glibc | 2.5 | 2.12 | 2.17 | 2.28 | 2.34 | 2.39 |
| GCC | 4.1 | 4.4 | 4.8 | 8.2, plus GCC Toolset | 11.2 | 14.2 (builds for x86-64-v3) |
| Python | 2.4 | 2.6 | 2.7 (3.x only via Software Collections) | 3.6. `python` is not on PATH unless added. Python 2 is optional | 3.9. `python` and `python3` point at 3.9. Python 2 is not shipped | 3.12 |
| OpenSSL | 0.9.8 | 1.0.0 (1.0.1 in later minors) | 1.0.1 (1.0.2 in later minors) | 1.1.1 (TLS 1.3) | 3.0 (providers; engines deprecated) | 3.x providers. Rebased to 3.5 in 10.1, including post-quantum algorithms. OpenSSL 1.1 compatibility library removed |
| systemd | Not present | Not present | systemd | systemd | systemd | systemd 257 |
| Default filesystem | ext3 | ext4 (XFS supported; `/boot` stays ext4) | XFS | XFS | XFS | XFS |
| Disk encryption | LUKS | LUKS | LUKS1 | LUKS2 by default | LUKS2 | LUKS2 |
| Bootloader | GRUB Legacy | GRUB Legacy | GRUB2 | GRUB2 with Boot Loader Specification | GRUB2 with Boot Loader Specification | GRUB2 with Boot Loader Specification |
| Desktop | GNOME 2 | GNOME 2 | GNOME 3 Classic on X.Org | GNOME 3.28, Wayland by default, X.Org session available | GNOME 40, Wayland by default. X.Org deprecated. PipeWire replaces PulseAudio | Wayland only. X.Org server removed. X11 clients run through Xwayland |
| Default database | MySQL 5.0 | MySQL 5.1 | MariaDB 5.5 | MariaDB 10.3 | MariaDB 10.5 (Valkey replaces Redis in later minors) | MariaDB 10.11, MySQL 8.4, PostgreSQL 16, Valkey 8 |
| Web server | Apache 2.2 | Apache 2.2 | Apache 2.4 | Apache 2.4, nginx via AppStream | Apache 2.4, nginx 1.20 | Apache 2.4, nginx 1.26 |

x86-64-v2 (RHEL 9) needs a CPU from about 2009 onward (SSE4.2, POPCNT, SSSE3): Intel Nehalem and later, or comparable AMD. x86-64-v3 (RHEL 10) needs AVX, AVX2, BMI1, BMI2, and FMA: Intel Haswell (4th generation) and later, or comparable AMD. Older virtual machines that boot RHEL 9 can fail on RHEL 10.

## Installation, packages, and upgrades

| Area | RHEL 5 | RHEL 6 | RHEL 7 | RHEL 8 | RHEL 9 | RHEL 10 |
| --- | --- | --- | --- | --- | --- | --- |
| Package tool | `yum` | `yum` | `yum` (`dnf` was a late Technology Preview) | `dnf`. The `yum` command calls DNF | `dnf`. The `yum` command calls DNF | `dnf`. Modularity is deprecated and new Application Streams are normal RPMs |
| Repository layout | RHN channels | RHN channels | Base channels on the CDN | BaseOS and AppStream. Modular `dnf module` streams | BaseOS and AppStream. Most streams are non-modular RPMs | BaseOS and AppStream. No modular Application Streams |
| RPM database | `/var/lib/rpm` | `/var/lib/rpm` | `/var/lib/rpm` | `/var/lib/rpm` | `/var/lib/rpm` | `/usr/lib/sysimage/rpm` |
| Content delivery | RHN Classic | RHN Classic, then `subscription-manager` | `subscription-manager`, CDN, Satellite | `subscription-manager`, CDN, Satellite | `subscription-manager`, CDN, Satellite | `subscription-manager`, CDN, Satellite |
| In-place upgrade to this version | Fresh install | Fresh install | `preupgrade-assistant` and `redhat-upgrade-tool` from RHEL 6 | Leapp from RHEL 7 | Leapp from RHEL 8 | Leapp from RHEL 9.6. No direct path from RHEL 8; go 8 → 9 → 10 |
| Image build | Kickstart | Kickstart | Kickstart | Image Builder (`composer`) and UBI | Image Builder. Image mode (`bootc`) is a Technology Preview in later minors | Image mode is a supported install path, including RHEL for Edge. `dnf --transient` for package changes that reset on reboot |

## Services, network, and security

| Area | RHEL 5 | RHEL 6 | RHEL 7 | RHEL 8 | RHEL 9 | RHEL 10 |
| --- | --- | --- | --- | --- | --- | --- |
| Init | SysV init | Upstart (SysV scripts still used) | systemd | systemd | systemd | systemd. cgroup v1 cannot be selected. SysV init scripts are deprecated |
| Service commands | `service`, `chkconfig` | `service`, `chkconfig` | `systemctl` | `systemctl` | `systemctl` | `systemctl` |
| Boot targets | Runlevels 0–6 | Runlevels 0–6 | systemd targets. Runlevels are compatibility aliases | systemd targets | systemd targets | systemd targets |
| Default network stack | `network` service, `ifcfg` files, `eth0` | `network` service and `ifcfg`. Optional consistent names (`em1`) via biosdevname | NetworkManager, predictable names (`ens*`, `enp*`). `ifcfg` and the `network` service still available | NetworkManager and `ifcfg`. `network-scripts` is a deprecated separate package | NetworkManager. New profiles are keyfiles in `/etc/NetworkManager/system-connections/`. Existing `ifcfg` profiles still work. `teamd` is removed; use bonding | Keyfiles only. `ifcfg` support is removed. `dhclient` is removed; NetworkManager’s internal DHCP client is the only client |
| Firewall | `iptables` | `iptables` | `firewalld` on an iptables backend. `iptables` service still available | `firewalld` on nftables | `firewalld` on nftables | `firewalld` on nftables |
| Time sync | `ntpd` | `ntpd` | `chronyd` by default. `ntpd` still packaged | `chronyd` only | `chronyd` only | `chronyd` only |
| Logs | syslog / rsyslog | rsyslog | `journald` plus rsyslog | `journald` plus rsyslog | `journald` plus rsyslog | `journald` plus rsyslog |
| Auth configuration | `authconfig` | `authconfig` | `authconfig` | `authselect` replaces `authconfig` | `authselect` | `authselect` is required by PAM and cannot be removed |
| Crypto policy | Per-application settings | Per-application settings | Per-application settings | `update-crypto-policies` (DEFAULT, LEGACY, FUTURE, FIPS) | Same policies. DEFAULT rejects SHA-1 signatures | DEFAULT also rejects TLS ciphers that use RSA key exchange. The `SHA1` subpolicy is gone |
| OpenSSH root login | Password login allowed | Password login allowed | Password login allowed on a typical server install | Password login allowed on a typical server install | Root password login disabled by default (`prohibit-password`: keys still work) | Root password login disabled by default |
| FIPS mode | Manual configuration | Manual configuration | `fips=1` | `fips=1` or `fips-mode-setup` | `fips=1` or `fips-mode-setup` | `fips-mode-setup` and `/etc/system-fips` are removed. Enable FIPS at install with `fips=1` |
| cgroups | Not an admin interface | v1 (`cgconfig`, `cgclassify`) | v1, managed by systemd | v1 by default. v2 available | v2 by default. v1 still possible with kernel arguments | v2 only |
| Containers | Not provided | Not provided | Docker | Podman, Buildah, Skopeo. Docker is not in the repositories | Podman 4. Default network is netavark and aardvark-dns | Podman 5. Runtime is `crun` (`runc` removed). CNI removed. Rootless networking uses pasta |
| Virtual machines | Xen. KVM added in 5.4 | KVM. Xen as a host is dropped | KVM, `virt-manager` | KVM. `virt-manager` deprecated in favor of Cockpit | KVM. `virt-manager` removed. Use Cockpit, `virsh`, or `virt-install` | KVM. Cockpit, `virsh`, or `virt-install` |
| Web console | Not provided | Not provided | Cockpit optional | Cockpit | Cockpit | Cockpit |
| SELinux | Targeted policy available | Targeted, enforcing by default | Targeted, enforcing by default | Targeted, enforcing by default | Targeted, enforcing by default | Targeted, enforcing by default |

## Same task, different command

| Task | RHEL 5 | RHEL 6 | RHEL 7 | RHEL 8 | RHEL 9 | RHEL 10 |
| --- | --- | --- | --- | --- | --- | --- |
| Install a package | `yum install pkg` | `yum install pkg` | `yum install pkg` | `dnf install pkg` | `dnf install pkg` | `dnf install pkg` |
| Start a service now | `service pkg start` | `service pkg start` | `systemctl start pkg` | `systemctl start pkg` | `systemctl start pkg` | `systemctl start pkg` |
| Start a service at boot | `chkconfig pkg on` | `chkconfig pkg on` | `systemctl enable pkg` | `systemctl enable pkg` | `systemctl enable pkg` | `systemctl enable pkg` |
| Change runlevel / target | `telinit 3` | `telinit 3` | `systemctl isolate multi-user.target` | `systemctl isolate multi-user.target` | `systemctl isolate multi-user.target` | `systemctl isolate multi-user.target` |
| Open a port | `iptables` rules | `iptables` rules | `firewall-cmd` | `firewall-cmd` | `firewall-cmd` | `firewall-cmd` |
| Set the hostname | edit `/etc/sysconfig/network` | edit `/etc/sysconfig/network` | `hostnamectl` | `hostnamectl` | `hostnamectl` | `hostnamectl` |
| Set the time zone | edit clock config, or `system-config-date` | edit clock config | `timedatectl` | `timedatectl` | `timedatectl` | `timedatectl` |
| Configure a NIC | edit `ifcfg-*`, `service network restart` | edit `ifcfg-*`, `service network restart` | `nmcli` or `ifcfg` | `nmcli` | `nmcli` (writes a keyfile) | `nmcli` (keyfile only) |
| Run Python | `python` | `python` | `python` | `python3` | `python` or `python3` | `python3` |

## What changes at each major version

| Upgrade | Changes you have to plan for |
| --- | --- |
| 5 → 6 | ext4 becomes the default filesystem. KVM replaces Xen as the supported host hypervisor. Upstart replaces SysV init, while `service` and `chkconfig` still work. cgroups v1, `dracut`, and `tuned` arrive. |
| 6 → 7 | systemd, GRUB2, XFS, firewalld, NetworkManager, and predictable NIC names become the defaults. `chronyd` replaces `ntpd` as the default time service. MariaDB replaces MySQL. GNOME 3 replaces GNOME 2. 32-bit installation media is dropped. |
| 7 → 8 | DNF replaces Yum 3. Software is split into BaseOS and AppStream. Podman replaces Docker. The firewalld backend becomes nftables. Wayland becomes the default display server. System-wide crypto policies and `authselect` arrive. Python 3 replaces Python 2 as the default. LUKS2 becomes the default. Leapp is the supported in-place path. |
| 8 → 9 | cgroup v2 is the default. OpenSSL 3 replaces OpenSSL 1.1. The CPU floor rises to x86-64-v2. New NetworkManager profiles use keyfiles. Network teaming is removed. Root SSH password login is disabled. Python 2 is gone. `virt-manager` is removed. PipeWire replaces PulseAudio. |
| 9 → 10 | The CPU floor rises to x86-64-v3, so Haswell-era instructions are required. The X.Org server is removed. cgroup v1 is removed. `ifcfg` and `dhclient` are removed. 32-bit multilib packages are removed. FIPS can only be enabled at install time. Podman 5 uses `crun` and drops CNI and `runc`. Image mode is a supported way to deploy the system. |
