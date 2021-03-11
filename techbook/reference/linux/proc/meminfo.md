| 名称 | 说明 | 
| --- | --- | 
| MemTotal | 可用RAM大小，物理内存减去预留位和内核使用 </br> 对应内核全局变量：totalram_pages, 该变量表示系统中Linux内核可以管理的所有的page frame的数量 | 
| MemFree | 系统未使用内存 </br> freeram = global_page_state(NR_FREE_PAGES) </br> MemUsed=MemTotal-MemFree | 
| MemAvailable | 应用程序可用内存 </br> 不需要额外磁盘操作（开销较大）就可以使用的空闲内存的数量 </br> MemAvailable≈MemFree+Buffers+Cached </br> 即便是free的，也不是每一个page都可以用于内存分配。例如buddy system会设定一个水位，一旦free memory低于这个水位，系统就会启动page reclaim，从而可能引起swapping操作。因此，我们需要从MemFree这个数值中去掉各个节点、各个zone的预留的内存（WMARK_LOW）数目。当然，也是不是说那些不是空闲的页面我们就不能使用，例如page cache，虽然不是空闲的，但是它也不过是为了加快性能而使用，其实也可以回收使用。当然，也不是所有的page cache都可以计算进入MemAvailable，有些page cache在回收的时候会引起swapping，这些page cache就不能算数了。此外，reclaimable slab中也有一些可以使用的内存，MemAvailable也会考虑这部分内存的情况。| 
| Buffers | 块设备的page cache </br> 记录文件系统的metadata以及 tracking in-flight pages，就是说buffers是用来存储目录里面有什么内容，权限等等| 
| Cached | 普通文件IO的page cache大小 </br> cached = global_page_state(NR_FILE_PAGES) - total_swapcache_pages() - i.bufferram </br> |
| SwapCached | 已经被交换出来的内存。与 I/O 相关 | 


