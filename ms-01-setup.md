# DISCLAIMER
This is just to document my personal setup. I'm not a Proxmox expert, just a lowly devops nerd who messes with my homelab in my free time. But I work in Linux for my day job and get to work on lots of cool tech professionally, so I think I'm beyond knowing "enough to be dangerous" and hopefully I know enough to be careful now :)

This guide isn't meant to be full, comprehensive, etc. It's just enough for me to rebuild, rework, etc. as needed.

# BIOS Setup
- Boot into the BIOS and click through all the options, disabling ASPM anywhere it is mentioned (Active State Power Management). If you're running this as a server, you don't want devices going into low power states which can hypothetically cause performance or stability issues with said devices.
- Optionally configure boot devices and boot order.

# Hardware Setup
- Install RAM and NVMe disks
- Optionally remove the Wifi card, especially if you are using a WIFI to NVMe adapter: https://www.amazon.com/Sintech-NGFF-NVME-WiFi-Cable/dp/B07DZF1W55/
  - Can report that this works well. I installed a Patriot low power NVMe disk and am using that as a boot disk for Proxmox, leaving the 3 NVMe slots available for future expansion.
  - I secured the board down using one of the long screws from the fan assembly through the corner of the NVMe board so it's not flopping around. I routed the ribbon cable around the outside of the fan assembly (not blocking the airway) and just need to be super careful sliding the assembly in/out of the case.

# First Steps
- If there is a BIOS update, make sure you are on the latest version. At this time, the latest version is 1.22.
- Install Proxmox. I'm not explaining this one. Read the docs and decide what disk(s), etc. you want to use for this.
- IMPORTANT! For the MS-01 13900H version, currently you need to run the latest Intel microcode to solve stability issues.
  - Check current running version of Intel microcode: `grep -E 'stepping|model|microcode' /proc/cpuinfo`
  - Add non-free-firmware repo: `echo "deb http://ftp.debian.org/debian bookworm main contrib non-free-firmware" > /etc/apt/sources.list.d/non-free-firmware.list`
  - Update apt: `apt update && apt install -y intel-microcode`
  - Check current running version of Intel microcode: `grep -E 'stepping|model|microcode' /proc/cpuinfo`
    - At this time, mine shows 0x4121. I have never had a single occurance of freezing or locking up, and I've been running Proxmox 24/7 for several months.
  - NOTE: This method applies during OS load, so if you reinstall Proxmox you will need to redo these steps. Once a BIOS update is available, this should be applied at the BIOS level and we can skip these steps (hopefully, eventually).
- Optionally, if you want to make sure ASPM is disabled after loading the OS. You could also add this into crontab as a post boot task.
  - `grep -l -F '' /sys/bus/pci/devices/*/link/*pm | xargs -I {} sh -c "echo '0' > {}"`
- If you're like me and don't have an Enterprise subscription to Proxmox, do the steps to disable the Enterprise apt repo and enable the no-subscription repo. CLI or UI, your choice :)
  - Samesies for the Ceph no-subscription repo if you are going to install Ceph.
- Set up any disks you are going to use for local storage. If you're going to be clustering, you may want to hold off and do shared storage a bit later, after getting networking and clustering done.