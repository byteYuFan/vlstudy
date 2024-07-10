## 1.pjsip_model结构

```c
struct pjsip_module
{
    PJ_DECL_LIST_MEMBER(struct pjsip_module);  // 链表成员，用于模块链接

    pj_str_t name;  //模块名称，用于标识模块。在注册模块之前，必须初始化此字段。

    int id;  // 在将模块注册到PJSIP之前，应用程序必须将此字段初始化为-1  模块ID
     */
    int priority;  // 整数值，用于识别模块初始化和启动顺序，相对于其他模块。数字越大，模块的初始化越晚。在注册模块之前，必须初始化此字段。 模块优先级


    pj_status_t (*load)(pjsip_endpoint *endpt);  // 初始化模块的函数指针

    pj_status_t (*start)(void);  // 启动模块的函数指针

    pj_status_t (*stop)(void);  // 停止模块的函数指针

    pj_status_t (*unload)(void);  // 卸载模块的函数指针

    pj_bool_t (*on_rx_request)(pjsip_rx_data *rdata);  // 处理传入请求消息的函数指针

    pj_bool_t (*on_rx_response)(pjsip_rx_data *rdata);  // 处理传入响应消息的函数指针
    
    pj_status_t (*on_tx_request)(pjsip_tx_data *tdata);  // 处理传出请求消息的函数指针

    pj_status_t (*on_tx_response)(pjsip_tx_data *tdata);  // 处理传出响应消息的函数指针

    void (*on_tsx_state)(pjsip_transaction *tsx, pjsip_event *event);  // 处理事务状态变化的函数指针
};

```

## 2.模块优先级

```c
enum pjsip_module_priority
{
 
    PJSIP_MOD_PRIORITY_TRANSPORT_LAYER  = 8,  // 传输层优先级


    PJSIP_MOD_PRIORITY_TSX_LAYER        = 16, // 事务层优先级


    PJSIP_MOD_PRIORITY_UA_PROXY_LAYER   = 32, // 用户代理和代理层优先级


    PJSIP_MOD_PRIORITY_DIALOG_USAGE     = 48, // 对话使用优先级


    PJSIP_MOD_PRIORITY_APPLICATION      = 64  // 应用程序优先级
};

```

**PJSIP_MOD_PRIORITY_TRANSPORT_LAYER (8)**

- **传输层优先级**：传输层模块处理底层网络传输相关的操作，例如建立和管理TCP/UDP连接。这些模块需要先于其他层初始化，以确保传输通道可用。

**PJSIP_MOD_PRIORITY_TSX_LAYER (16)**

- **事务层优先级**：事务层模块管理SIP事务，确保SIP请求和响应的正确匹配和处理。这些模块在传输层之后、用户代理和代理层之前初始化。

**PJSIP_MOD_PRIORITY_UA_PROXY_LAYER (32)**

- **用户代理和代理层优先级**：用户代理和代理层模块处理具体的SIP操作，例如注册、邀请等。这些模块在事务层之后初始化，以利用事务层提供的功能。

**PJSIP_MOD_PRIORITY_DIALOG_USAGE (48)**

- **对话使用优先级**：对话使用模块管理SIP对话的使用和生命周期，例如会话的创建、维护和终止。这些模块在用户代理和代理层之后初始化。

**PJSIP_MOD_PRIORITY_APPLICATION (64)**

- **应用程序优先级**：应用程序模块由最终用户应用定义，处理特定的应用逻辑。这些模块通常在所有PJSIP核心模块之后初始化，以利用所有底层功能。