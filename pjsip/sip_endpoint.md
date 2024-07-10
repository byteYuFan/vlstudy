## 1.endpoint退出回调列表

```c
/* List of SIP endpoint exit callback. */
typedef struct exit_cb
{
    PJ_DECL_LIST_MEMBER             (struct exit_cb);
    pjsip_endpt_exit_callback       func;
} exit_cb;
typedef void (*pjsip_endpt_exit_callback)(pjsip_endpoint *endpt);
```

- **PJ_DECL_LIST_MEMBER(struct exit_cb)**：这是一个宏，用于将结构体作为链表成员。它允许将多个 `exit_cb` 结构体链接在一起，形成一个链表。这在需要处理多个回调函数时非常有用。
- **pjsip_endpt_exit_callback func**：这是一个函数指针，指向实际的退出回调函数。当SIP端点退出时，将调用该函数来执行清理或其他必要的操作。

## 2.sip_endpoint

```c
struct pjsip_endpoint
{
    /** 为端点分配内存的内存池。 */
    pj_pool_t           *pool;  // 内存池

    /** 用于内存池、哈希表和事件列表/队列的互斥锁。 */
    pj_mutex_t          *mutex;  // 互斥锁

    /** 内存池工厂。 */
    pj_pool_factory     *pf;  // 内存池工厂

    /** 名称。 */
    pj_str_t             name;  // 名称

    /** 定时器堆。 */
    pj_timer_heap_t     *timer_heap;  // 定时器堆

    /** 传输管理器。 */
    pjsip_tpmgr         *transport_mgr;  // 传输管理器

    /** IO队列。 */
    pj_ioqueue_t        *ioqueue;  // IO队列

    /** 最近一次IO队列的错误。 */
    pj_status_t          ioq_last_err;  // 最后一次IO队列错误

    /** DNS解析器。 */
    pjsip_resolver_t    *resolver;  // DNS解析器

    /** 模块锁。 */
    pj_rwmutex_t        *mod_mutex;  // 模块读写锁

    /** 模块数组。 */
    pjsip_module        *modules[PJSIP_MAX_MODULE];  // 模块数组

    /** 按优先级排序的模块列表。 */
    pjsip_module         module_list;  // 模块列表，按优先级排序

    /** 能力头列表。 */
    pjsip_hdr            cap_hdr;  // 能力头列表

    /** 附加请求头。 */
    pjsip_hdr            req_hdr;  // 附加请求头

    /** 退出回调列表。 */
    exit_cb              exit_cb_list;  // 退出回调列表
};

```

1. **pj_pool_t \*pool**：指向内存池的指针，用于为端点分配内存。
2. **pj_mutex_t \*mutex**：指向互斥锁的指针，用于保护内存池、哈希表和事件列表/队列的并发访问。
3. **pj_pool_factory \*pf**：指向内存池工厂的指针，用于创建内存池。
4. **pj_str_t name**：端点的名称。
5. **pj_timer_heap_t \*timer_heap**：指向定时器堆的指针，用于管理定时器事件。
6. **pjsip_tpmgr \*transport_mgr**：指向传输管理器的指针，用于管理传输层。
7. **pj_ioqueue_t \*ioqueue**：指向IO队列的指针，用于管理IO事件。
8. **pj_status_t ioq_last_err**：最近一次IO队列操作的错误状态。
9. **pjsip_resolver_t \*resolver**：指向DNS解析器的指针，用于解析域名。
10. **pj_rwmutex_t \*mod_mutex**：指向读写锁的指针，用于保护模块列表的并发访问。
11. **pjsip_module \*modules[PJSIP_MAX_MODULE]**：模块数组，包含所有注册的模块。
12. **pjsip_module module_list**：模块链表，按优先级排序。
13. **pjsip_hdr cap_hdr**：能力头列表，包含端点的能力描述。
14. **pjsip_hdr req_hdr**：附加请求头列表，包含额外的SIP请求头。
15. **exit_cb exit_cb_list**：退出回调链表，包含在端点退出时要调用的回调函数。

## 3.API操作

### 3.1.pjsip_endpt_register_module

```c
PJ_DEF(pj_status_t) pjsip_endpt_register_module(pjsip_endpoint *endpt, pjsip_module *mod);
```

### 功能

`pjsip_endpt_register_module` 函数用于向指定的SIP端点 (`pjsip_endpoint`) 注册一个新的模块 (`pjsip_module`)。

### 作用

1. **注册模块**：将模块添加到端点的模块列表中。
2. 初始化模块：
   - 调用模块的 `load` 函数进行加载。
   - 调用模块的 `start` 函数进行启动。
3. **分配模块ID**：为模块分配一个唯一的ID，用于标识该模块。

### 参数

- `pjsip_endpoint *endpt`：指向SIP端点的指针。
- `pjsip_module *mod`：指向要注册的模块的指针。

### 返回值

- `pj_status_t`：返回操作的状态码。成功时返回 `PJ_SUCCESS`，否则返回相应的错误码。

### 具体操作

1. **检查参数**：验证输入参数是否有效。
2. **加载模块**：调用模块的 `load` 回调函数，如果提供的话。
3. **启动模块**：调用模块的 `start` 回调函数，如果提供的话。
4. **分配ID**：为模块分配一个唯一ID。
5. **插入模块列表**：将模块插入到端点的模块列表中。

该函数确保在SIP端点中正确注册和初始化模块，从而使模块能够参与SIP消息的处理和其他相关操作。

