# 概论
* system call
* procfs: 虚拟文件系统，没有实际存在磁盘中，只是允许内核以文件的形式向用户空间输出内部信息
* sysctl(proc/sys): 允许用户读取或修改内核变量的值
* sysfs(/sys): 由于以上两种滥用，独立出来。当前sysctl部分信息可以移植到sysfs中
* ioctl 系统调用：操作对象是一个文件， ifconfig/route 使用这种方式
* netlink
# procfs 与sysctl
都是输出内核信息，prcfs主要输出只读，而大多数sysctl是可以写入的。就只读数据而言，简单的变量和数据结构可以用sysctl输出
，但复杂的就需要procfs，例如缓存和统计数据
## procfs
* proc_mkdir： 创建目录
* proc_net_fops_create/remove： 创建/删除文件
```
static struct file_operations arp_seq_fops = {
	.owner   = THIS_MODULE,
	.open    = arp_seq_open
	.read    = seq_read
	.llseek  = seq_lseek
	.release = seq_release_private
}
static int __inti arp_proc_init(void)
{
  if(!proc_net_fops_create("arp", S_IRUGO,&arp_swq_fops))
			return -ENOMEM;
	reutrn 0;
}

static struct seq_operations arp_seq_ops = {
	.start    = clip_seq_start
	.next			= neigh_seq_next
	.stop			= neigh_seq_stop
	.show			= clip_seq_show
}

static int arp_seq_open(struct inode *inode, struct file *file)
{
	rc = seq_open(file, &arp_seq_ops);
}
```
sysctl: 目录/proc/sys
* 看到的文件实际上是一个内核变量
* * 以ctl_table结构定义一个文件和目录
  ![image](https://github.com/surprpeng/linux_network_note/assets/54309576/86e85567-d2a9-49f0-ba4f-16d11c7e317e)
* procname 是


  
