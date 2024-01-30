# SKB_BUFF
struct sk_buff
{
// 所有的sk_buff 都由一个双向链表，将所有的sk_buff穿起来
struct sk_buff *next;
struct sk_buff *prev;

struct sk_buff_head *list; // 指向链表头

// 指向拥有整个缓冲区的sock， 一般在本地进程或L4中使用
struct sock *sk;

uint len;// 从head开始，包括报文头部和对齐和数据本身。
uint data_len; // just data 大小
uint mac_len; // mac报头大小

atomic_t users; //引用计数

uint truesize; //缓冲区总的大小，包括sk_buff本身。 size + sizeof(sk_buff)

// 已分配的空间
uchar *head; 
uchar *end;
// 实际使用的空间， 随着sk_buff在LX的传递，变化
uchar *data;
uchar *tail;

void (*destructor)(...); 析构函数

struct timeval stamp; //netif_rx 记录接收时的时间

struct net_device *dev; // interface to or from

// 由于data指针一直指向头部，随着在各层之间传递，data指针一直在变
// 而 h 一直指向L4报文的头部。as so on, 
union{}h;  //L4
union{}nh;  //L3
union{}mac; //L2

char cb[40];  可user自定义

}

// 链表头， 大小很小
struct sk_buff_head
{
struct sk_buff *next;
struct sk_buff *prev;
__u32 qlen; // 链表的长度
spinlock_t lock; // 防止对表并发访问
}

function:

# net_device
变量可分为以下几个类型：
* 配置
* 统计数据
* 设备状态
* 列表管理
* 流量管理
* 功能专用
* 通用
* 函数指针
  ## 标识符
```
  ifindex/iflink/dev_id
```
## 配置
* name(e.g. eht0)
* mem_start/mem_end: 与内核沟通所使用的共享内存
* base_addr: 加载时用户配置
* irq: 与内核对话用的中断编号
* if_port: 端口类型
* flags/gflags/priv_flags: 表示开启的功能，状态， ifconfig可进行查看，
* mtu: 最大传输单元
* type: (e.g. ethernet/frame relay)
* promiscuity： 混杂模式，可以监听所有包， 类似sniffer
## 统计数据
本身没有统计字段，而是提供您一个priv指针，供驱动程序自己定义数据结构。
## 设备状态
* state: network queue state, e.g. __LINK_STATE_XOFF means stop queue
* trans_start: 表示最近一个帧传输启动的时间
* last_rx: 最后一个封包到达的时间
## 列表管理
net_device 被放到一个全局列表 和两个hash表中
* next： 指向全局列表中的下一个
* name_hlist:
* index_hlist:
## 链路层多播
在L2层头部加入特定的信息或使用特定的地址，表示多播的传送
* mc_list: 设备会为其监听的每个链路层多播地址保存一个dev_mc_list,
* mc_count: mc_list的长度
## 流量管理
QOS or fair queueing
* net_device *next_sched
* Qdisc *qdisc
* qdisc_sleeping
* qdisc_ingress
* qdisc_list: 用于管理入口和出口的封包队列，以及不通cpu对此设别的访问
## 通用
* refcnt: 引用计数
* watchdog_timer
* poll/poll_list/quota/weight: NAPI功能使用
* 

