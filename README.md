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

    Ubuntu version 12.10 in October 2012 with 3.5 kernel, long term support release February 2013
    Oracle Linux 6.4 in October 2013
    Red Hat Enterprise Linux 6.5 in November 2013

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
Ubuntu has a process of providing Long Term Support releases,  </br>
Releases and their release date, included kernels  </br>
````
┌─────────────┬──────────────────┬────────────────┬────────┐
│ Version:    │ Code Name:       │  Release date: │ Kernel │
├─────────────┼──────────────────┼────────────────┼────────┤▒▒▒
10.04 LTS	Lucid Lynx	2010-04-29
10.10	Maverick Meerkat	2010-10-10
11.04	Natty Narwhal	2011-04-28
11.10	Oneiric Ocelot	2011-10-13
│ 12.04 LTS   │ Precise Pangolin │     2012-04-26 │        │▒▒▒
│ 12.10       │ Quantal Quetzal  │     2012-10-18 │        │▒▒▒
│ 13.04       │ Raring Ringtail  │     2013-04-25 │        │▒▒▒
│ 13.10       │ Saucy Salamander │     2013-10-17 │        │▒▒▒
│ 14.04 LTS   │ Trusty Tahr      │     April 2014 │  3.13  │▒▒▒
├─────────────┼──────────────────┼────────────────┼────────┤▒▒▒
│ 14.04.1     │                  │      July 2014 │   3.13 │▒▒▒
├─────────────┼──────────────────┼────────────────┼────────┤▒▒▒
│ 14.04.2     │                  │  February 2015 │   3.16 │▒▒▒
│ 14.10       │ Utopic Unicorn   │ 2014-10-23     │   3.16 │▒▒▒
15.04	Vivid Vervet	2015-04-23
15.10	Wily Werewolf	2015-10-22
16.04 LTS	Xenial Xerus	2016-04-21
16.10	Yakkety Yak	2016-10-13
17.04	Zesty Zapus	2017-04-13
17.10	Artful Aardvark	2017-10-19
18.04 LTS	Bionic Beaver	2018-04-26
18.10	Cosmic Cuttlefish	2018-10-18
19.04	Disco Dingo	2019-04-18
19.10	Eoan Ermine	2019-10-17
20.04 LTS	Focal Fossa	2020-04-23
20.10	Groovy Gorilla	2020-10-22
21.04	Hirsute Hippo	2021-04-22
21.10	Impish Indri	2021-10-14
22.04 LTS	Jammy Jellyfish	2022-04-21
22.10	Kinetic Kudu	2022-10-20
23.04	Lunar Lobster	2023-04-20
23.10	Mantic Minotaur	2023-10-12
24.04 LTS	Noble Numbat	2024-04-25
24.10	Oracular Oriole	2024-10-10
25.04	Plucky Puffin	2025-04-17
└─────────────┴──────────────────┴────────────────┴────────┘▒▒▒
   ▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒▒
````
[Ubuntu version history Table of versions](https://en.wikipedia.org/wiki/Ubuntu_version_history#Table_of_versions)

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
