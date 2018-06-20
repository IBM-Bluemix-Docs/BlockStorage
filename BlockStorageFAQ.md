---

copyright:
  years: 2014, 2018
lastupdated: "2018-05-17"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}

# {{site.data.keyword.blockstorageshort}} Frequently Asked Questions

## How many instances can share the use of a {{site.data.keyword.blockstorageshort}} volume?
The default limit for number of authorizations per block volume is 8. To increase the limit, contact your sales representative.

## Is the allocated IOPS enforced by instance or by volume?
IOPS is enforced at the volume level. Said differently, two hosts connected to a volume with 6000 IOPS share that 6000 IOPS.

## How is IOPS measured?
IOPS is measured based on a load profile of 16 KB blocks with random 50 percent read and 50 percent writes. Workloads that differ from this profile may experience lower performance.

## What happens when I use a smaller block size when measuring performance?
Maximum IOPS can still be obtained when using smaller block sizes. However, throughput will be lower. For example; a volume with 6000 IOPS would have the following throughput at various block sizes:

- 16 KB * 6000 IOPS == ~93.75 MB/sec 
- 8 KB * 6000 IOPS == ~46.88 MB/sec
- 4 KB * 6000 IOPS == ~23.44 MB/sec

## Does the volume need to be pre-warmed to achieve expected throughput?
There's no need for pre-warming. You'll observe specified throughput immediately upon provisioning the volume.

## Why can I provision {{site.data.keyword.blockstorageshort}} with Endurance 10 IOPS/GB tier in some data centers and not in others?
The 10 IOPS/GB tier of Endurance type {{site.data.keyword.blockstorageshort}} is only available in select data centers, with new data centers being added gradually. You can find a full list of upgraded data centers and available features [here](new-ibm-block-and-file-storage-location-and-features.html).

## How can I tell which of my {{site.data.keyword.blockstorageshort}} LUNs/volumes are encrypted?
When viewing your list of {{site.data.keyword.blockstorageshort}} in the [{{site.data.keyword.slportal}}](https://control.softlayer.com/){:new_window}, you'll see a lock icon to the right of LUN/volume name for those that are encrypted.

## How do I know when I am provisioning {{site.data.keyword.blockstorageshort}} in an upgraded data center?
When provisioning {{site.data.keyword.blockstorageshort}}, all upgraded data centers will be denoted with an asterisk (`*`) in the order form and an indication that you'll be provisioning storage with encryption. When the storage is provisioned, you'll see an icon in the storage list that shows that storage as encrypted. All encrypted volumes and LUNs are provisioned in upgraded data centers only. You can find a full list of upgraded data centers and available features [here](new-ibm-block-and-file-storage-location-and-features.html).

## Why can I provision {{site.data.keyword.blockstorageshort}} with an Endurance 10 IOPS tier in some data centers and not in others?
The Endurance type 10 IOPS/GB tier is only available in select data centers, with new data centers being added soon. You can find a full list of upgraded data centers and available features [here](new-ibm-block-and-file-storage-location-and-features.html).

## If I have non-encrypted {{site.data.keyword.blockstorageshort}} provisioned in a data center that has been upgraded, can I encrypt my {{site.data.keyword.blockstorageshort}}?

{{site.data.keyword.blockstorageshort}} that is provisioned before the data center upgrade can't be encrypted. 
New {{site.data.keyword.blockstorageshort}} provisioned in upgraded data centers is automatically encrypted. There's no encrypt setting to choose from, it’s automatic. 
Data on non-encrypted storage in an upgraded data center can be encrypted by creating a new Block LUN, then copying the data to the new encrypted LUN with host-based migration. Read this [article](migrate-block-storage-encrypted-block-storage.html) for instructions.

## How many volumes can I provision?

By default, you can provision a combined total of 250 {{site.data.keyword.blockstorageshort}} volumes. To increase your volume, contact your sales representative to increase your volumes.

## If I use a faster Ethernet connection, can I achieve more throughput?

Throughput limits are set at a per-volume/LUN level so using a faster Ethernet connection won't increase that set limit. However, with a slower Ethernet connection, your bandwidth can be a potential bottleneck.

## Will firewalls/security groups impact performance?

We recommend running storage traffic on a VLAN which bypasses the firewall as a best practice. Running storage traffic through software firewalls will increase latency and adversely affect storage performance.

## What performance latency can I expect from my {{site.data.keyword.blockstorageshort}}?   

Target latency within the storage is <1 ms. Our storage is connected to compute instances on a shared network, so the exact performance latency will depend on the network traffic within a given time frame.

## Does {{site.data.keyword.blockstorageshort}} support SCSI-3 Persistent Reserve to implement I/O fencing for Db2 pureScale?

Yes, {{site.data.keyword.blockstorageshort}} supports both SCSI-2 and SCSI-3 persistent reservations.

## What happens to my data when {{site.data.keyword.blockstorageshort}} LUNs are deleted?

When storage is deleted, any pointers to the data on that volume are removed thus the data becomes completely inaccessible. If the physical storage is reprovisioned to another account a new set of pointers are assigned. There’s no way for the new account to access any data that may have been on the physical storage, the new set of pointers shows all 0's. When new data is written to the volume/LUN, any inaccessible data that still exists gets overwritten.

## What happens to the drives that are decommissioned from the cloud data center?

When drives are decommissioned IBM destroys them before disposing of them, thus making them unusable. Any data that was written to that drive becomes inaccessible.
