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
