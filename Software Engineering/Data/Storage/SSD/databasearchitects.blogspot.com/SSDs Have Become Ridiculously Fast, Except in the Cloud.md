# [SSDs Have Become Ridiculously Fast, Except in the Cloud](http://databasearchitects.blogspot.com/2024/02/ssds-have-become-ridiculously-fast.html)

* Flash-based SSDs have replaced disks
* Each SSD consists of many independent flash chips that can be accessed in parallel
* Throughput of an SSD is primarily dependent on the interface speed to the host
  * From 2017-2024, there was an increase from ~0.5 GB/s read bandwith (SATA) to ~14GB/s read bandwith (PCIe 5)
* From 2017-2024, there was also an increase in capacity per dollar
  * 2017: ~2 GB/$ -> 2024: ~6 GB/$

## AWS

* AWS launched instances with physically attached NVMe SSDs in early 2017
* Performance has not increased from that point in time - stills tuck with 2 GB/s per SSD
* Performance between state-of-the-art SSDs and those offered via cloud vendors is nearing an order of magnitude
* Advances in other areas (like EC2 network bandwidth)

### Theories

* EC2 intentionally caps write speed at 1 GB/s to avoid frequent device failure given that the total number of writes per SSD in its lifetime is limited
* Little incentive to optimize faster as there are not that many systems that need to exploit tens of GB/s of I/O bandwidth
* EC2 having very fast and cheap NVMe instance storage would disrupt the cost structure of other areas of the business (like EBS)
