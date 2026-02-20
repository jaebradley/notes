# [Ep #77: Why S3 is Faster Than Your Hard Drive (The Architecture of Distribution)](https://thearchitectsnotebook.substack.com/p/ep-77-why-s3-is-faster-than-your-0c4)
* AWS Region consists of multiple Availability Zones
* AZs are physically distinct data centers
* When you `PUT` an object, S3 acts as a transaction coordinator where a "success" acknowledgement only occurs when the data has been durably committed to multiple AZs
* S3 divides uploaded file into blocks and stores these blocks across different hard drives, across different racks, across different availability zones
* Single hard-drive disks read at 100 MB/s, so reading a 5 GB file would take ~50 seconds
* Distributing the 5 GB file into fifty 100 MB files across fifty disks means that the total time to read the entire file would take 1 second
