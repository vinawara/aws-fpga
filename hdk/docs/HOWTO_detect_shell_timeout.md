
# AXI Timeouts
 
* The Shell provides a timeout mechanism which terminates any outstanding AXI transactions after 2.5 uS. There is a separate timeout per interface. Upon the first timeout, metrics registers are updated with the offending address and a counter is incremented. Upon further timeouts the counter is incremented. These metrics registers can be read via the fpga-describe-local-image found in [Amazon FPGA Image Management Tools README](../../sdk//userspace/fpga_mgmt_tools/README.md)
 
* Timeouts can occur for three reasons:
  1. The CL doesn’t respond to the address (reserved address space)
  2. The CL has a protocol violation on AXI which hangs the bus
  3. The address is going to F1 card’s DDR memory and the CL design’s latency is exceeding timeout value.

* Best practice is to ensure addresses to reserved address space are fully decoded in your CL design.  
* DMA accesses to DDR will accumulate which can sometimes lead to timeouts.  
* CL designs which have multiple masters to DDR will also incur arbitration delays.
* If you suspect a timeout, debug by reading the metrics registers. The saved offending address should help narrow whether this is to DDR or registers/RAMs inside the FPGA. If it’s inside the FPGA the developer should investigate protocol violations.

# How to detect a shell timeout has occured

* Shell-CL interface timeouts can be detected by checking for non-zero timeout counters.  These metrics can be read using this command:  
```
$sudo fpga-describe-local-image -S 0 --metrics
AFI          0       agfi-0f0e045f919413242  loaded            0        ok               0       0x04151701
AFIDEVICE    0       0x1d0f      0xf000      0000:00:1d.0
sdacl-slave-timeout=0
virtual-jtag-slave-timeout=0
ocl-slave-timeout=0
bar1-slave-timeout=0
dma-pcis-timeout=0
pcim-range-error=0
pcim-axi-protocol-error=0
pcim-axi-protocol-4K-cross-error=0
pcim-axi-protocol-bus-master-enable-error=0
pcim-axi-protocol-request-size-error=0
pcim-axi-protocol-write-incomplete-error=0
pcim-axi-protocol-first-byte-enable-error=0
pcim-axi-protocol-last-byte-enable-error=0
pcim-axi-protocol-bready-error=0
pcim-axi-protocol-rready-error=0
pcim-axi-protocol-wchannel-error=0
sdacl-slave-timeout-addr=0x0
sdacl-slave-timeout-count=0
virtual-jtag-slave-timeout-addr=0x0
virtual-jtag-slave-timeout-count=0
ocl-slave-timeout-addr=0x8001
ocl-slave-timeout-count=0
bar1-slave-timeout-addr=0x2001
bar1-slave-timeout-count=0
dma-pcis-timeout-addr=0x0
dma-pcis-timeout-count=0
pcim-range-error-addr=0x0
pcim-range-error-count=0
pcim-axi-protocol-error-addr=0x0
pcim-axi-protocol-error-count=0
pcim-write-count=0
pcim-read-count=0
DDR0
   write-count=0
   read-count=0
DDR1
   write-count=0
   read-count=0
DDR2
   write-count=29797854199
   read-count=4
DDR3
   write-count=0
   read-count=0
```
* For detailed infomation on metrics, see [Amazon FPGA Image Management Tools README](../../sdk//userspace/fpga_mgmt_tools/README.md)
