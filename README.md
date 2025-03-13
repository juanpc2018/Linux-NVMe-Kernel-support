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

It’s hard to document everything, and reading this table may be more than you need! If you want to view it differently we created this PDF slideshow for you here:

https://communities.intel.com/docs/DOC-24868

The advancement and maturity of NVMe as a platform capability for scale is also well represented in this presentation from Keith Busch. Intel has produced a 30 million IOPS computer with NVMe devices.

http://events.linuxfoundation.org/sites/events/files/slides/LinuxVault2015_KeithBusch_PCIeMPath.pdf

Given this history, you probably live in a supported server distribution world, so let’s move on to a few notes about Linux server distributions. We want to highlight the great work of our distribution partners – note that Intel’s visibility is not perfect, so our partners are the final authorities here.

Red Hat Enterprise Linux (RHEL):

NVMe SSDs have been supported since RHEL 6.5. With the introduction of kernel 3.10 in RHEL 7, support for booting from NVMe was added. RHEL 7.1 becomes even more capable with hot plug features that were added in by Red Hat.

Oracle Linux (OL)

Oracle Linux includes inbox support for NVMe SSDs beginning with Unbreakable Enterprise Kernel (UEK) Release 3 in October 2013. UEK R3 is supported in Oracle Linux 6.4 (or later) and Oracle Linux 7 (or later), and is enhanced to provide NVMe feature support consistent with mainline kernel 3.19, including hot-plug support (although without the block_mq feature).

Oracle was the first vendor to support NVMe hot-plug in a Linux distribution and today hot-plug is supported in all Oracle Linux releases beginning with Oracle Linux 6.4.
Oracle Linux with UEK R3 	Release date 	Kernel (as of April 2015)
Oracle Linux 6.4 	October 2013 	

    3.8 with nvme feature support consistent with 3.19

Oracle Linux 6.5 	November 2013 	

    3.8 with nvme feature support consistent with 3.19

Oracle Linux 7 GA 	July 2014 	

    3.8 with nvme feature support consistent with 3.19

Oracle Linux 6.6 	October 2014 	

    3.8 with nvme feature support consistent with 3.19

Oracle Linux 7 Update 1 	March 2015 	

    3.8 with nvme feature support consistent with 3.19

Oracle Linux 6.7 	TBA 	TBA
Oracle Linux 7 Update 2 	TBA 	TBA

Ubuntu:
Ubuntu has a process of providing long term support releases, the most pertinent one being 14.04 (released in April 2014).  The three releases of 14.04, and their release date and included kernels, all of which support NVMe are shown in the following table.
Ubuntu LTS Release Number 	Release date 	Kernel
14.04 	April 2014 	3.13
14.04.1 	July 2014 	3.13
14.04.2 	February 2015 	3.16

SUSE Linux Enterprise Server (SLES 11 and 12)

SLES 11 SP3 which released in July 2014 has a kit for installing the high performing NVMe driver. Please use this link to gain access to that driver.

http://drivers.suse.com/intel/Intel_SSD_DC_P3500_P3700/sle11-sp3-x86_64/1.0/install-readme.html

Intel recommends the upcoming SLES 11 SP4 as soon as it comes out. Hot plug support and boot support will be available in this release.

SLES 12 which released in October 2014 has the latest NVMe driver updates, including hot plug support for orderly removal.

https://www.suse.com/releasenotes/x86_64/SUSE-SLES/11-SP4/

https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12/

As you can see, NVMe enjoys broad, mature and hardened support for Linux. Check it out!

Here are links to blogs that cover other data center operating systems we work with here in the Non-Volatile Memory Solutions Group:

VMware ESXi

Microsoft Windows https://communities.intel.com/community/itpeernetwork/blog/2015/06/30/nvm-express-windows-driver-support-decoded

![Linux-storage-stack-diagram_v4 10-e1575939041721](https://github.com/user-attachments/assets/1580c03f-3170-458f-997a-f7226be43938)
