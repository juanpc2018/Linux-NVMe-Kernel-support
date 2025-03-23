# Linux NVMe Kernel support </br>

https://web.archive.org/web/20200807033507/https://itpeernetwork.intel.com/nvm-express-linux-driver-support-decoded/#gs.i7y08b </br>
https://backports.docs.kernel.org/releases.html </br>
https://cdn.kernel.org/pub/linux/kernel/projects/backports/stable/ </br>
https://lwn.net/Articles/driver-porting/ </br>
https://backports.docs.kernel.org/ </br>
https://github.com/torvalds/linux/tags?after=v2.6.15-rc5 </br>

---------------------------
![Tux1](https://github.com/user-attachments/assets/0980e18e-3c56-40e2-9176-e6f6758e29db)
![NVMexpress21](https://github.com/user-attachments/assets/57871623-adb2-4fea-bf27-224574f6af76)

NVM Express has enjoyed Linux kernel support since early 2011. However, it is a complex landscape to understand all of the Linux Server OS and kernel choices, including when particular features have been adopted. This blog is intended to decode your options for NVMe on Linux. </br>

NVMe has been supported in the mainline upstream release of Linux since kernel 3.3. Check out the commit log message for NVMe, and you can pages of updates going back to 2011. The first distributions to support NVM Express focused on the add-in card form factor (without support for hot plug). A flavor of initial distribution support included: </br>

    Ubuntu 12.10 October 2012 with 3.5 kernel,
    Oracle Linux 6.4 October 2013
    Red Hat Enterprise Linux 6.5 November 2013

To learn more about the initial Red Hat support, refer to: http://www.redhat.com/en/about/press-releases/red-hat-launches-latest-version-of-red-hat-enterprise-linux-6. In the past two years, Red Hat has matured and hardened their driver. Red Hat Enterprise Linux 6.7 Beta is fully featured, including hot plug support and hot boot capability. </br>

Linux kernel support and distributions have matured and hardened over the past two years. As an example, Red Hat Enterprise Linux 6.7 Beta is fully featured, including hot plug support and hot boot capability. Let’s look at the upstream kernel features in the NVMe driver and software stack that may be pertinent to your production deployment. </br>

Quick Reference Table: NVMe highlights and mainline kernel release support (*through May 2015) </br>

![Chart](https://github.com/user-attachments/assets/de3435e2-b499-4cf7-9a9a-26551b5762d3)

``` </br>
┌─────────────────────────────────────┬─────┬─────┬─────┬──────┬──────┬──────┬──────┬──────┬──────┬─────┬─────┐
│ Feature/Kernel:                     │ 3.3 │ 3.6 │ 3.9 │ 3.10 │ 3.12 │ 3.14 │ 3.15 │ 3.16 │ 3.19 │ 4.0 │ 4.1 │ 
├─────────────────────────────────────┼─────┼─────┼─────┼──────┼──────┼──────┼──────┼──────┼──────┼─────┼─────┤▒▒▒
│ High performance NVMe 1.0 spec      │   √ │   √ │   √ │    √ │    √ │    √ │    √ │    √ │    √ │   √ │   √ │▒▒▒
├─────────────────────────────────────┼─────┼─────┼─────┼──────┼──────┼──────┼──────┼──────┼──────┼─────┼─────┤▒▒▒
│ Greater than 512byte LBA            │     │   √ │   √ │    √ │    √ │    √ │    √ │    √ │    √ │   √ │   √ │▒▒▒
├─────────────────────────────────────┼─────┼─────┼─────┼──────┼──────┼──────┼──────┼──────┼──────┼─────┼─────┤▒▒▒
│ Discard / TRIM                      │     │     │   √ │    √ │    √ │    √ │    √ │    √ │    √ │   √ │   √ │▒▒▒
├─────────────────────────────────────┼─────┼─────┼─────┼──────┼──────┼──────┼──────┼──────┼──────┼─────┼─────┤▒▒▒
│ Disk stats – iostat                 │     │     │     │    √ │    √ │    √ │    √ │    √ │    √ │   √ │   √ │▒▒▒
│ BIO splitting                       │     │     │     │    √ │    √ │    √ │    √ │    √ │    √ │   √ │   √ │▒▒▒
├─────────────────────────────────────┼─────┼─────┼─────┼──────┼──────┼──────┼──────┼──────┼──────┼─────┼─────┤▒▒▒
│ Power Management: Suspend / Resume  │     │     │     │      │    √ │    √ │    √ │    √ │    √ │   √ │   √ │▒▒▒
├─────────────────────────────────────┼─────┼─────┼─────┼──────┼──────┼──────┼──────┼──────┼──────┼─────┼─────┤▒▒▒
│ Dynamic partitions                  │     │     │     │      │      │    √ │    √ │    √ │    √ │   √ │   √ │▒▒▒
│ Surprise removal                    │     │     │     │      │      │    √ │    √ │    √ │    √ │   √ │   √ │▒▒▒
│ Command Abort                       │     │     │     │      │      │    √ │    √ │    √ │    √ │   √ │   √ │▒▒▒
├─────────────────────────────────────┼─────┼─────┼─────┼──────┼──────┼──────┼──────┼──────┼──────┼─────┼─────┤▒▒▒
│ Surprise removal                    │     │     │     │      │      │      │    √ │    √ │    √ │   √ │   √ │▒▒▒
│ Per-CPU and Optimizations           │     │     │     │      │      │      │    √ │    √ │    √ │   √ │   √ │▒▒▒
├─────────────────────────────────────┼─────┼─────┼─────┼──────┼──────┼──────┼──────┼──────┼──────┼─────┼─────┤▒▒▒
│ Trace Events                        │     │     │     │      │      │      │      │    √ │    √ │   √ │   √ │▒▒▒
├─────────────────────────────────────┼─────┼─────┼─────┼──────┼──────┼──────┼──────┼──────┼──────┼─────┼─────┤▒▒▒
│ Block-mq, and hot-plug finalization │     │     │     │      │      │      │      │      │    √ │   √ │   √ │▒▒▒
├─────────────────────────────────────┼─────┼─────┼─────┼──────┼──────┼──────┼──────┼──────┼──────┼─────┼─────┤▒▒▒
│ Multipath NVMe                      │     │     │     │      │      │      │      │      │      │   √ │   √ │▒▒▒
├─────────────────────────────────────┼─────┼─────┼─────┼──────┼──────┼──────┼──────┼──────┼──────┼─────┼─────┤▒▒▒
│ DIF/DIX & Metadata, hot-cpu         │     │     │     │      │      │      │      │      │      │     │   √ │▒▒▒
└─────────────────────────────────────┴─────┴─────┴─────┴──────┴──────┴──────┴──────┴──────┴──────┴─────┴─────┘▒▒▒
   ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
```
</br>

It’s hard to document everything, and reading this table may be more than you need! If you want to view it differently we created this PDF slideshow for you here: </br>
https://communities.intel.com/docs/DOC-24868

The advancement and maturity of NVMe as a platform capability for scale is also well represented in this presentation from Keith Busch. Intel has produced a 30 million IOPS computer with NVMe devices. </br>
http://events.linuxfoundation.org/sites/events/files/slides/LinuxVault2015_KeithBusch_PCIeMPath.pdf </br>

Given this history, you probably live in a supported server distribution world, so let’s move on to a few notes about Linux server distributions.  </br>
We want to highlight the great work of our distribution partners – note that Intel’s visibility is not perfect, so our partners are the final authorities here. </br>

Red Hat Enterprise Linux (RHEL): </br>
NVMe SSDs have been supported since RHEL 6.5. With the introduction of kernel 3.10 in RHEL 7, support for booting from NVMe was added. RHEL 7.1 becomes even more capable with hot plug features that were added in by Red Hat. </br>

Oracle Linux (OL) </br>
Oracle Linux includes inbox support for NVMe SSDs beginning with Unbreakable Enterprise Kernel (UEK) Release 3 in October 2013.  </br>
UEK R3 is supported in Oracle Linux 6.4 (or later) and Oracle Linux 7 (or later),  </br>
and is enhanced to provide NVMe feature support consistent with mainline kernel 3.19, including hot-plug support (although without the block_mq feature). </br>

Oracle was the first vendor to support NVMe hot-plug in a Linux distribution and today hot-plug is supported in all Oracle Linux releases beginning with Oracle Linux 6.4. </br>
Oracle Linux with UEK R3 	Release date 	Kernel (as of April 2015) </br>
Oracle Linux 6.4 	October 2013 </br>

    3.8 with nvme feature support consistent with 3.19

Oracle Linux 6.5 	November 2013 </br>

    3.8 with nvme feature support consistent with 3.19

Oracle Linux 7 GA 	July 2014  </br>

    3.8 with nvme feature support consistent with 3.19

Oracle Linux 6.6 	October 2014 </br>

    3.8 with nvme feature support consistent with 3.19

Oracle Linux 7 Update 1 	March 2015 </br>

    3.8 with nvme feature support consistent with 3.19 

Oracle Linux 6.7 	TBA 	TBA </br>
Oracle Linux 7 Update 2 	TBA 	TBA </br>

----------------------------

Ubuntu: </br>
Releases, release date & included kernel  </br>
````
┌─────────────┬───────────────────┬────────────┬────────┐
│ Version:    │ Code Name:        │ Date:      │ Kernel │
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 4.10        │ Warty Warthog     │ 2004-10-20 │ 2.6.8  │▒▒▒
│ 5.04        │ Hoary Hedgehog    │ 2005-04-08 │ 2.6.10 │▒▒▒
│ 5.10        │ Breezy Badger     │ 2005-10-12 │ 2.6.12 │▒▒▒
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 6.06 LTS    │ Dapper Drake      │ 2006-06-01 │ 2.6.15 │▒▒▒
│ 6.10        │ Edgy Eft          │ 2006-10-26 │ 2.6.17 │▒▒▒
│ 7.04        │ Feisty Fawn       │ 2007-04-19 │ 2.6.20 │▒▒▒
│ 7.10        │ Gutsy Gibbon      │ 2007-10-18 │ 2.6.22 │▒▒▒
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 8.04 LTS    │Hardy Heron        │ 2008-04-24 │ 2.6.24 │▒▒▒
│ 8.10        │ Intrepid Ibex     │ 2008-10-30 │ 2.6.27 │▒▒▒
│ 9.04        │ Jaunty Jackalope  │ 2009-04-23 │ 2.6.28 │▒▒▒
│ 9.10        │ Karmic Koala      │ 2009-10-29 │ 2.6.31 │▒▒▒
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 10.04 LTS   │ Lucid Lynx        │ 2010-04-29 │ 2.6.32 │▒▒▒
│ 10.10       │ Maverick Meerkat  │ 2010-10-10 │ 2.6.35 │▒▒▒
│ 11.04       │ Natty Narwhal     │ 2011-04-28 │ 2.6.38 │▒▒▒
│ 11.10       │ Oneiric Ocelot    │ 2011-10-13 │  3.0   │▒▒▒
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 12.04 LTS   │ Precise Pangolin  │ 2012-04-26 │  3.2   │▒▒▒
│ 12.10       │ Quantal Quetzal   │ 2012-10-18 │  3.5   │▒▒▒
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 13.04       │ Raring Ringtail   │ 2013-04-25 │  3.8   │▒▒▒
│ 13.10       │ Saucy Salamander  │ 2013-10-17 │  3.11  │▒▒▒
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 14.04 LTS   │ Trusty Tahr       │ April 2014 │  3.13  │▒▒▒
│ 14.04.1     │                   │ July  2014 │  3.13  │▒▒▒
│ 14.04.2     │                   │ Feb   2015 │  3.16  │▒▒▒
│ 14.10       │ Utopic Unicorn    │ 2014-10-23 │  3.16  │▒▒▒
│ 15.04       │ Vivid Vervet      │ 2015-04-23 │  3.19  │▒▒▒
│ 15.10       │ Wily Werewolf     │ 2015-10-22 │  4.2   │▒▒▒
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 16.04 LTS   │ Xenial Xerus      │ 2016-04-21 │  4.4   │▒▒▒
│ 16.10       │ Yakkety Yak       │ 2016-10-13 │  4.8   │▒▒▒
│ 17.04       │ Zesty Zapus       │ 2017-04-13 │  4.10  │▒▒▒
│ 17.10       │ Artful Aardvark   │ 2017-10-19 │  4.13  │▒▒▒
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 18.04 LTS   │ Bionic Beaver     │ 2018-04-26 │  4.15  │▒▒▒
│ 18.10       │ Cosmic Cuttlefish │ 2018-10-18 │  4.18  │▒▒▒
│ 19.04       │ Disco Dingo       │ 2019-04-18 │  5.0   │▒▒▒
│ 19.10       │ Eoan Ermine       │ 2019-10-17 │  5.3   │▒▒▒
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 20.04 LTS   │ Focal Fossa       │ 2020-04-23 │  5.4   │▒▒▒
│ 20.10       │ Groovy Gorilla    │ 2020-10-22 │  5.8   │▒▒▒
│ 21.04       │ Hirsute Hippo     │ 2021-04-22 │  5.11  │▒▒▒
│ 21.10       │ Impish Indri      │ 2021-10-14 │  5.13  │▒▒▒
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 22.04 LTS   │ Jammy Jellyfish   │ 2022-04-21 │  5.15/7│▒▒▒
│ 22.10       │ Kinetic Kudu      │ 2022-10-20 │  5.19  │▒▒▒
│ 23.04       │ Lunar Lobster     │ 2023-04-20 │  6.2   │▒▒▒
│ 23.10       │ Mantic Minotaur   │ 2023-10-12 │  6.5   │▒▒▒
├─────────────┼───────────────────┼────────────┼────────┤▒▒▒
│ 24.04 LTS   │ Noble Numbat      │ 2024-04-25 │  6.8   │▒▒▒
│ 24.10       │ Oracular Oriole   │ 2024-10-10 │  6.11  │▒▒▒
│ 25.04       │ Plucky Puffin     │ 2025-04-17 │        │▒▒▒
└─────────────┴───────────────────┴────────────┴────────┘▒▒▒
   ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
````
[Ubuntu version history Table of versions](https://en.wikipedia.org/wiki/Ubuntu_version_history#Table_of_versions)

Ubuntu 13 has the [13th Curse](https://en.wikipedia.org/wiki/Triskaidekaphobia) </br>
source code has small [cosmic Ray bit-flip type bugs](https://en.wikipedia.org/wiki/Soft_error) never fixed 12 years later </br>
does Not stop working, but has issues, bugs seem to accumulate in following versions. </br>
how many small bugs are required to crash a system? </br>
incredible similar to [DNA Damage](https://en.wikipedia.org/wiki/DNA_damage_theory_of_aging) </br>

Ubuntu 17 has a unique libc.so.6 tied to kerner version, Not available in 16 Nor 18. </br>
software designed for 17 does Not compile in any other, unless modified. </br>

Ubuntu 18 was the last 32-Bit i386 .iso installer. </br>

Ubuntu 24 installers are "Wayland" have visual bugs on GTX 1050 Ti. </br>
also has problems with Nvidia propietary drivers, removing/change driver also removes all other Realtek propietary drivers. </br>
making the system off-line. </br>

Ext4 v1.0 was changed to 64-bit "v2.0" around Ubuntu 16 making it backward incompatible. </br>
XFS tools v6 in Ubuntu 24 changed, making it partially backward incompatible with xfs toools v5.x </br>

Awesome vintage wallpapers / art are Not included in Newer versions. </br>

Kernel 6.7 has Focusrite USB drivers activated by Default. </br>

-------------------------------------


SUSE Linux Enterprise Server (SLES 11 and 12) </br>
SLES 11 SP3 which released in July 2014 has a kit for installing the high performing NVMe driver. Please use this link to gain access to that driver. </br>
http://drivers.suse.com/intel/Intel_SSD_DC_P3500_P3700/sle11-sp3-x86_64/1.0/install-readme.html </br>
Intel recommends the upcoming SLES 11 SP4 as soon as it comes out. Hot plug support and boot support will be available in this release. </br>
SLES 12 which released in October 2014 has the latest NVMe driver updates, including hot plug support for orderly removal. </br>
https://www.suse.com/releasenotes/x86_64/SUSE-SLES/11-SP4/ </br>
https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12/ </br>

As you can see, NVMe enjoys broad, mature and hardened support for Linux. Check it out! </br>
Here are links to blogs that cover other data center operating systems we work with here in the Non-Volatile Memory Solutions Group: </br>

VMware ESXi
Microsoft Windows https://communities.intel.com/community/itpeernetwork/blog/2015/06/30/nvm-express-windows-driver-support-decoded </br>

[(2019)](https://www.youtube.com/watch?v=NtkKHhXf3V4) </br>
![Linux-storage-stack-diagram_v4 10-e1575939041721](https://github.com/user-attachments/assets/1580c03f-3170-458f-997a-f7226be43938) 
