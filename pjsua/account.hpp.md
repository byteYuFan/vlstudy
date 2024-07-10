## 1.配置账号注册

```c
/**
 * Account registration config. This will be specified in AccountConfig.
 */
struct AccountRegConfig : public PersistentObject
{
  
    string              registrarUri;//注册请求的 URI，格式类似于 "sip:8080@soc100.cn:7070"。如果该值为空，则不会执行账号注册。

   
    bool                registerOnAdd;//指定账号是否在添加到 UA 后立即注册。如果设置为 PJ_FALSE，应用程序可以使pjsua_acc_set_registration() 手动控制注册。

    bool                disableRegOnModify;//指定在使用 Account::modify() 修改账号时是否自动更新注册。例如在账号凭据更改时，如果不希望立即注册，可以禁用此选项。

    /** Array of strings */	
	typedef std::vector<SipHeader> SipHeaderVector;
    SipHeaderVector     headers;//可选的自定义 SIP 头，将添加到注册请求中。
    
    string              contactParams;//附加参数，将附加到注册请求的 Contact 头后面。参数应以分号开头，所有字符串必须正确转义。例如：";my-param=X;another-param=Hi%20there"

    string              contactUriParams; //附加参数，将附加到注册请求的 Contact URI 后面。参数应以分号开头，所有字符串必须正确转义。例如：";my-param=X;another-param=Hi%20there"

 	// 可选的注册间隔（秒）。如果值为零，则使用默认间隔（PJSUA_REG_INTERVAL，300 秒）。
    unsigned            timeoutSec;

    unsigned            retryIntervalSec;

    /**
    指定注册失败（包括传输问题导致的失败）后的自动重新注册间隔（秒）。设置为 0 表示禁用自动重新注册。需要注意的是，如果由于传输失败导致重新注册，首次重试将在 firstRetryIntervalSec 秒后进行。
    首次注册重试的间隔时间。此值也会被随机化一些秒数，以避免所有客户端同时重新注册。
     */
    unsigned            firstRetryIntervalSec;

  /*定要添加/减去的最大随机值，以秒为单位，用于随机化注册重试间隔。此设置有助于避免所有客户端同时重新注册。例如，如果注册重试间隔设置为 100 秒，该值设置为 10 秒，则实际注册重试间隔将在 90 到 110 秒之间。
默认值：10*/
    unsigned            randomRetryIntervalSec;

   /*：指定在注册到期前刷新客户端注册的秒数。
默认值：PJSIP_REGISTER_CLIENT_DELAY_BEFORE_REFRESH, 5 秒*/
    unsigned            delayBeforeRefreshSec;

    /**
    指定在注册失败且重新注册尝试也失败后，是否应放弃已配置账号的通话。
默认值：False（禁用）
     */
    bool                dropCallsOnFail;

    /**
     定在库关闭过程中等待注销请求完成的最长时间。
默认值：PJSUA_UNREG_TIMEOUT
     */
    unsigned            unregWaitMsec;

    /**
     指定注册使用出站代理和账号代理的方式。这控制了此账号的 REGISTER 请求中 Route 头的出现。值是 PJSUA_REG_USE_OUTBOUND_PROXY 和 PJSUA_REG_USE_ACC_PROXY 位的组合。如果值设置为 0，REGISTER 请求将不使用任何代理（即没有任何 Route 头）。
     */
    unsigned            proxyUse;

public:
    /**
    readObject：从容器节点读取对象。
    参数：node 容器节点
    writeObject：将对象写入容器节点。
    参数：node 容器节点
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

 
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);

};

```

## 2.账号配置结构

```c
/**
 * Account SIP 配置结构体。这将在 AccountConfig 中指定。
 */
struct AccountSipConfig : public PersistentObject
{
    /**
     * 账号的 SIP URI，通常格式为 "sip:username@domain"。
     * 这是账号的标识 URI。
     */
    string idUri;

    /**
     * 传出呼叫的默认显示名称。
     * 这是在发起呼叫时显示的用户名。
     */
    string displayName;

    /**
     * 传出呼叫的默认 Contact 参数。
     * 这些参数将附加到传出呼叫的 Contact 头后面。
     * 参数应该以分号开头，所有字符串必须正确转义。
     * 例如: ";my-param=X;another-param=Hi%20there"
     */
    string contactParams;

    /**
     * 传出呼叫的默认 Contact URI 参数。
     * 这些参数将附加到传出呼叫的 Contact URI 后面。
     * 参数应该以分号开头，所有字符串必须正确转义。
     * 例如: ";my-param=X;another-param=Hi%20there"
     */
    string contactUriParams;

    /**
     * 传出呼叫的默认 Route 头设置。
     * 这是一个包含路由的字符串列表，用于控制呼叫路径。
     */
    StringVector routeSet;

    /**
     * 传出呼叫的默认 Proxy 服务器设置。
     * 这是一个包含代理服务器的字符串列表，用于指定代理服务器。
     */
    StringVector proxy;

    /**
     * 默认注册 Contact 头中的附加参数。
     * 这些参数将附加到注册请求的 Contact 头后面。
     * 参数应该以分号开头，所有字符串必须正确转义。
     * 例如: ";my-param=X;another-param=Hi%20there"
     */
    string regContactParams;

    /**
     * 默认注册 Contact URI 中的附加参数。
     * 这些参数将附加到注册请求的 Contact URI 后面。
     * 参数应该以分号开头，所有字符串必须正确转义。
     * 例如: ";my-param=X;another-param=Hi%20there"
     */
    string regContactUriParams;

    /**
     * 通知服务器的 SIP URI，用于订阅通知。
     * 如果应用程序需要接收特定的 SIP 通知，则需要设置这个 URI。
     */
    string mwiUri;

public:
    /**
     * 从容器节点读取该对象。
     * @param node 容器节点。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将该对象写入容器节点。
     * @param node 容器节点。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
};

```

- **idUri**：账号的 SIP URI，这是账号在 SIP 网络中的标识。
- **displayName**：在传出呼叫中显示的名称，这通常是用户可读的姓名或昵称。
- **contactParams**：传出呼叫中 Contact 头附加的参数，用于指定额外的联系信息。
- **contactUriParams**：传出呼叫中 Contact URI 附加的参数，用于指定联系 URI 的附加信息。
- **routeSet**：指定传出呼叫的默认路由设置，用于控制呼叫的路径。
- **proxy**：指定传出呼叫的默认代理服务器，用于在呼叫过程中通过代理服务器进行路由。
- **regContactParams**：注册请求中 Contact 头附加的参数，用于指定额外的联系信息。
- **regContactUriParams**：注册请求中 Contact URI 附加的参数，用于指定联系 URI 的附加信息。
- **mwiUri**：通知服务器的 SIP URI，用于订阅消息等待指示（MWI）通知。

## 3.账号通话配置结构体

```c
/**
 * 账号通话配置结构体。这将在 AccountConfig 中指定。
 */
struct AccountCallConfig : public PersistentObject
{
    /**
     * 指定如何向远程端提供通话保持功能。
     * 请参阅 pjsua_call_hold_type 的文档了解更多信息。
     *
     * 默认值: PJSUA_CALL_HOLD_TYPE_DEFAULT
     */
    pjsua_call_hold_type holdType;

    /**
     * 指定如何使用可靠的临时响应 (100rel/PRACK) 支持，适用于此账号的所有会话。
     * 请参阅 pjsua_100rel_use 枚举的文档了解更多信息。
     *
     * 默认值: PJSUA_100REL_NOT_USED
     */
    pjsua_100rel_use    prackUse;

    /**
     * 指定会话计时器在所有会话中的使用情况。
     * 请参阅 pjsua_sip_timer_use 了解可能的值。
     *
     * 默认值: PJSUA_SIP_TIMER_OPTIONAL
     */
    pjsua_sip_timer_use timerUse;

    /**
     * 指定最小会话计时器过期时间，以秒为单位。
     * 不能低于 90 秒。默认值为 90。
     */
    unsigned            timerMinSESec;

    /**
     * 指定会话计时器过期时间，以秒为单位。
     * 不能低于 timerMinSESec。默认值为 1800。
     */
    unsigned            timerSessExpiresSec;

public:
    /**
     * 默认构造函数
     */
    AccountCallConfig() : holdType(PJSUA_CALL_HOLD_TYPE_DEFAULT),
                          prackUse(PJSUA_100REL_NOT_USED),
                          timerUse(PJSUA_SIP_TIMER_OPTIONAL),
                          timerMinSESec(90),
                          timerSessExpiresSec(PJSIP_SESS_TIMER_DEF_SE)
    {}

    /**
     * 从容器节点读取该对象。
     *
     * @param node 容器节点。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将该对象写入容器节点。
     *
     * @param node 容器节点。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
};

```

**holdType**：指定如何向远程端提供通话保持功能。

- 这决定了当用户想要暂时中止通话时，该功能是如何被实现和通知给远程端的。

**prackUse**：指定如何使用可靠的临时响应 (100rel/PRACK) 支持。

- 这影响了是否以及如何使用 PRACK 机制来确认临时响应，以提高通话建立的可靠性。

**timerUse**：指定会话计时器在所有会话中的使用情况。

- 会话计时器用于确保通话保持活跃并在必要时刷新会话。

**timerMinSESec**：指定最小会话计时器过期时间，以秒为单位。

- 这是会话计时器可以设置的最短时间间隔，以确保会话在指定时间内保持活跃。

**timerSessExpiresSec**：指定会话计时器过期时间，以秒为单位。

- 这是会话计时器的标准时间间隔，超过此时间间隔后会话将被认为是过期的，需要重新刷新。

## 4.订阅发布结构体

```c
/**
 * 账号在订阅/发布服务中的配置结构体。这将在 AccountConfig 中指定。
 */
struct AccountPresConfig : public PersistentObject
{
    /**
     * 可选的自定义 SIP 头字段，将添加到订阅请求中。
     */
    SipHeaderVector     headers;

    /**
     * 如果设置了此标志，该账号的在线状态信息将 PUBLISH 到服务器。
     *
     * 默认值: PJ_FALSE
     */
    bool                publishEnabled;

    /**
     * 指定客户端发布会话是否应在有其他 PUBLISH 事务仍在等待时排队 PUBLISH 请求。
     * 如果设置为 false，则在有其他 PUBLISH 事务仍在进行时，客户端将返回 PUBLISH 请求的错误。
     *
     * 默认值: PJSIP_PUBLISHC_QUEUE_REQUEST (TRUE)
     */
    bool                publishQueue;

    /**
     * 在关闭进程期间等待未完成的发布事务完成的最长时间，然后发送取消注册请求。
     * 库在关闭过程中尝试等待未完成的发布（un-PUBLISH）完成，然后发送 REGISTER 请求以注销账号。
     * 如果此值设置得太短，则可能在未完成发布完成之前发送注销请求，从而导致未完成发布请求失败。
     *
     * 值的单位是毫秒。
     *
     * 默认值: PJSUA_UNPUBLISH_MAX_WAIT_TIME_MSEC (2000)
     */
    unsigned            publishShutdownWaitMsec;

    /**
     * 可选的 PIDF 元组 ID，用于发送 PUBLISH 和 NOTIFY 请求。
     * 如果未指定此值，将使用随机字符串。
     */
    string              pidfTupleId;

public:
    /**
     * 从容器节点读取该对象。
     *
     * @param node 容器节点。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将该对象写入容器节点。
     *
     * @param node 容器节点。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
};

```

**headers**：可选的自定义 SIP 头字段，将添加到订阅请求中。

- 这允许应用程序在发送订阅请求时添加额外的自定义信息。

**publishEnabled**：如果设置了此标志，该账号的在线状态信息将 PUBLISH 到服务器。

- 这决定了账号是否会向服务器发布其在线状态信息。

**publishQueue**：指定客户端发布会话是否应在有其他 PUBLISH 事务仍在等待时排队 PUBLISH 请求。

- 这控制了在已有未完成的 PUBLISH 事务时，新的 PUBLISH 请求是排队还是返回错误。

**publishShutdownWaitMsec**：在关闭进程期间等待未完成的发布事务完成的最长时间，然后发送取消注册请求。

- 这确保在关闭过程中，未完成的发布事务有足够的时间完成，从而避免注销请求发送过早导致发布请求失败。

**pidfTupleId**：可选的 PIDF 元组 ID，用于发送 PUBLISH 和 NOTIFY 请求。

- 这允许指定一个唯一的元组 ID，用于标识发布的在线状态信息。如果未指定，将使用随机生成的字符串。

## 5.（MWI）中的配置结构体

```c
/**
 * 账号在消息等待指示（MWI）中的配置结构体。这将在 AccountConfig 中指定。
 */
struct AccountMwiConfig : public PersistentObject
{
    /**
     * 订阅消息等待指示事件（RFC 3842）。
     *
     * 另请参见 UaConfig.mwiUnsolicitedEnabled 设置。
     *
     * 默认值: FALSE
     */
    bool                enabled;

    /**
     * 指定消息等待指示事件订阅的默认过期时间（秒）。此值不能为零。
     *
     * 默认值: PJSIP_MWI_DEFAULT_EXPIRES (3600)
     */
    unsigned            expirationSec;

public:
    /**
     * 从容器节点读取该对象。
     *
     * @param node 容器节点。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将该对象写入容器节点。
     *
     * @param node 容器节点。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
};

```

## 6.NAT配置结构体

```c
/**
 * 账号在NAT配置中的配置结构体。这将在 AccountConfig 中指定。
 */
struct AccountNatConfig : public PersistentObject
{
    /**
     * 控制SIP信令中使用STUN的方式。
     *
     * 默认值: PJSUA_STUN_USE_DEFAULT
     */
    pjsua_stun_use      sipStunUse;

    /**
     * 控制媒体传输中使用STUN的方式。
     *
     * 默认值: PJSUA_STUN_USE_DEFAULT
     */
    pjsua_stun_use      mediaStunUse;

    /**
     * 控制SIP信令中使用UPnP的方式。
     *
     * 默认值: PJSUA_UPNP_USE_DEFAULT
     */
    pjsua_upnp_use      sipUpnpUse;

    /**
     * 控制媒体传输中使用UPnP的方式。
     *
     * 默认值: PJSUA_UPNP_USE_DEFAULT
     */
    pjsua_upnp_use      mediaUpnpUse;

    /**
     * 指定NAT64选项。
     *
     * 默认值: PJSUA_NAT64_DISABLED
     */
    pjsua_nat64_opt     nat64Opt;

    /**
     * 启用媒体传输的ICE。
     *
     * 默认值: False
     */
    bool                iceEnabled;

    /**
     * 为ICE媒体传输设置trickle ICE模式。
     *
     * 默认值: PJ_ICE_SESS_TRICKLE_DISABLED
     */
    pj_ice_sess_trickle iceTrickle;

    /**
     * 设置ICE主机候选的最大数量。
     *
     * 默认值: -1（未设置最大值）
     */
    int                 iceMaxHostCands;

    /**
     * 指定是否使用激进提名。
     *
     * 默认值: True
     */
    bool                iceAggressiveNomination;

    /**
     * 对于使用常规提名的代理，指定在所有组件都有一个有效对之后进行提名检查的延迟。
     *
     * 默认值: PJ_ICE_NOMINATED_CHECK_DELAY
     */
    unsigned            iceNominatedCheckDelayMsec;

    /**
     * 对于受控代理，指定在找到所有组件的有效检查后等待提名超时的时间（毫秒）。
     *
     * 默认值: ICE_CONTROLLED_AGENT_WAIT_NOMINATION_TIMEOUT
     * 指定-1以禁用此计时器。
     */
    int                 iceWaitNominationTimeoutMsec;

    /**
     * 禁用RTCP组件。
     *
     * 默认值: False
     */
    bool                iceNoRtcp;

    /**
     * 无论ICE传输地址是否更改，始终在ICE协商后发送re-INVITE/UPDATE。
     *
     * 默认值: yes
     */
    bool                iceAlwaysUpdate;

    /**
     * 启用ICE中的TURN候选。
     */
    bool                turnEnabled;

    /**
     * 指定TURN域名或主机名，格式为“DOMAIN:PORT”或“HOST:PORT”。
     */
    string              turnServer;

    /**
     * 指定用于连接TURN服务器的连接类型。有效值为PJ_TURN_TP_UDP或PJ_TURN_TP_TCP。
     *
     * 默认值: PJ_TURN_TP_UDP
     */
    pj_turn_tp_type     turnConnType;

    /**
     * 指定用于验证TURN服务器的用户名。
     */
    string              turnUserName;

    /**
     * 指定密码的类型。目前必须为零以表示将使用明文密码。
     */
    int                 turnPasswordType;

    /**
     * 指定用于验证TURN服务器的密码。
     */
    string              turnPassword;

    /**
     * 更新REGISTER请求的传输地址和Contact头。启用此选项时，库将跟踪REGISTER响应中的公共IP地址。
     * 一旦检测到地址发生变化，将注销当前Contact，更新从Via头学到的传输地址，并向注册服务器注册新Contact。
     * 这还将更新UDP传输的公共名称（如果配置了STUN）。
     *
     * 可能的值：
     * * 0（禁用）。
     * * 1（启用）。如果Contact和服务器的IP地址均为公共但响应包含私有IP，则不更新。
     * * 2（启用）。无条件更新。
     *
     * 另请参见contactRewriteMethod字段。
     *
     * 默认值: 1
     */
    int                 contactRewriteUse;

    /**
     * 指定在注册时如何进行Contact更新（如果启用了contactRewriteEnabled）。
     * 该值是pjsua_contact_rewrite_method的位掩码组合。
     *
     * 值PJSUA_CONTACT_REWRITE_UNREGISTER(1)是旧行为。
     *
     * 默认值: PJSUA_CONTACT_REWRITE_METHOD
     *   (PJSUA_CONTACT_REWRITE_NO_UNREG | PJSUA_CONTACT_REWRITE_ALWAYS_UPDATE)
     */
    int                 contactRewriteMethod;

    /**
     * 指定在使用TCP/TLS传输时，是否将源TCP端口用作初始Contact地址。
     * 请注意，当配置了名称服务器时，此功能将自动关闭，因为DNS SRV解析可能会生成不同的目标地址。
     * 在某些平台上，当TCP套接字仍在连接时，无法报告其本地地址。在这些情况下，此功能也将关闭。
     *
     * 默认值: 1 (PJ_TRUE / yes)
     */
    int                 contactUseSrcPort;

    /**
     * 用于覆盖传出的消息Via头的“sent-by”字段，使其与REGISTER请求中的接口地址相同，
     * 只要请求使用的传输实例与之前的REGISTER请求相同。
     *
     * 默认值: 1 (PJ_TRUE / yes)
     */
    int                 viaRewriteUse;

    /**
     * 控制在未使用STUN和ICE时，是否将SDP中的IP地址替换为REGISTER响应中Via头的IP地址。
     * 如果值为FALSE（原始行为），则使用本地IP地址。如果为TRUE，并且禁用STUN和ICE，
     * 则使用注册响应中找到的IP地址。
     *
     * 默认值: PJ_FALSE (no)
     */
    int                 sdpNatRewriteUse;

    /**
     * 控制SIP outbound功能的使用。SIP outbound在RFC 5626中描述，
     * 用于使代理或注册服务器能够使用UA发起的连接向UA发送入站请求。
     * 此功能在NAT部署中非常有用，因此默认启用。
     *
     * 注意：当前SIP outbound只能与TCP和TLS传输一起使用。如果注册时使用UDP，SIP outbound功能将被忽略。
     *
     * 默认值: 1 (PJ_TRUE / yes)
     */
    int                 sipOutboundUse;

    /**
     * 指定SIP outbound（RFC 5626）实例ID。如果为空，将根据该代理的主机名生成一个实例ID。
     * 如果应用程序指定了此参数，值将类似于"<urn:uuid:00000000-0000-1000-8000-AABBCCDDEEFF>"（不带双引号）。
     *
     * 默认值: 空
     */
    string              sipOutboundInstanceId;

    /**
     * 指定SIP outbound（RFC 5626）注册ID。默认值为空，这将导致库自动生成一个适当的值。
     *
     * 默认值: 空
     */
    string              sipOutboundRegId;

    /**
     * 为该账号设置定期发送的保持活动传输的间隔。如果此值为零，则禁用保持活动传输。
     * 保持活动传输将在成功注册后发送到注册服务器的地址。
     *
     * 默认值: 15（秒）
     */
    unsigned            udpKaIntervalSec;

    /**
     * 指定作为保持活动数据包发送的数据。
     *
     * 默认值: CR-LF
     */
    string              udpKaData;

public:
    /**
     * 默认构造函数
     */
    AccountNatConfig() : sipStunUse(PJSUA_STUN_USE_DEFAULT),
      mediaStunUse(PJSUA_STUN_USE_DEFAULT),
      sipUpnpUse(PJSUA_UPNP_USE_DEFAULT),
      mediaUpnpUse(PJSUA_UPNP_USE_DEFAULT),
      nat64Opt(PJSUA_NAT64_DISABLED),
      iceEnabled(false),
      iceTrickle(PJ_ICE_SESS_TRICKLE_DISABLED),
      iceMaxHostCands(-1),
      iceAggressiveNomination(true),
      iceNominatedCheckDelayMsec(PJ_ICE_NOMINATED_CHECK_DELAY),
      iceWaitNominationTimeoutMsec(ICE_CONTROLLED_AGENT_WAIT_NOMINATION_TIMEOUT),
      iceNoRtcp(false),
      iceAlwaysUpdate(true),
      turnEnabled(false),
      turnConnType(PJ_TURN_TP_UDP),
      turnPasswordType(0),
      contactRewriteUse(PJ_TRUE),
      contactRewriteMethod(PJSUA_CONTACT_REWRITE_METHOD),
      contactUseSrcPort(PJ_TRUE),
      viaRewriteUse(PJ_TRUE),
      sdpNatRewriteUse(PJ_FALSE),
      sipOutboundUse(PJ_TRUE),
      udpKaIntervalSec(15),
      udpKaData("\r\n")
    {}

    /**
     * 从容器节点读取对象。
     *
     * @param node 容器节点。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将对象写入容器节点。
     *
     * @param node 容器节点。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
};

```

## 7.SRTP加密选项

```c
struct SrtpCrypto
{
    /**
     * 可选的密钥。如果为空，将自动生成一个随机密钥。
     */
    string      key;

    /**
     * 加密名称。
     */
    string      name;

    /**
     * 标志，来自 #pjmedia_srtp_crypto_option 的位掩码。
     */
    unsigned    flags;

public:
    /**
     * 从pjsip结构体转换。
     */
    void fromPj(const pjmedia_srtp_crypto &prm);

    /**
     * 转换为pjsip结构体。
     */
    pjmedia_srtp_crypto toPj() const;
};

```

```c
struct SrtpOpt : public PersistentObject
{
    /**
     * 指定SRTP加密选项。如果为空，将启用所有加密选项。
     * 可用的加密选项可以使用Endpoint::srtpCryptoEnum()枚举。
     *
     * 默认值：空。
     */
    SrtpCryptoVector            cryptos;

    /**
     * 指定SRTP密钥方法，有效的密钥方法定义在pjmedia_srtp_keying_method中。
     * 如果为空，将启用所有密钥方法，优先顺序为：SDES, DTLS-SRTP。
     *
     * 默认值：空。
     */
    IntVector                   keyings;

public:
    /**
     * 默认构造函数，使用默认值初始化。
     */
    SrtpOpt();

    /**
     * 从pjsip结构体转换。
     */
    void fromPj(const pjsua_srtp_opt &prm);

    /**
     * 转换为pjsip结构体。
     */
    pjsua_srtp_opt toPj() const;

public:
    /**
     * 从容器节点读取此对象。
     *
     * @param node              从中读取值的容器。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将此对象写入容器节点。
     *
     * @param node              要写入值的容器。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
};

```

## 8.RTCP

```C
struct RtcpFbCap
{
    /**
     * 指定该能力适用的编解码器。编解码器ID使用与pjmedia_codec_mgr_find_codecs_by_id()
     * 和pjmedia_vid_codec_mgr_find_codecs_by_id()相同的格式，例如："L16/8000/1", "PCMU",
     * "H264"。这也可以是星号（"*"），表示所有编解码器。
     */
    string                  codecId;

    /**
     * 指定RTCP反馈类型。
     */
    pjmedia_rtcp_fb_type    type;

    /**
     * 如果RTCP反馈类型为PJMEDIA_RTCP_FB_OTHER，则指定类型名称。
     */
    string                  typeName;

    /**
     * 指定RTCP反馈参数。
     */
    string                  param;

public:
    /**
     * 构造函数。
     */
    RtcpFbCap() : type(PJMEDIA_RTCP_FB_OTHER)
    {}

    /**
     * 从pjsip结构体转换。
     */
    void fromPj(const pjmedia_rtcp_fb_cap &prm);

    /**
     * 转换为pjsip结构体。
     */
    pjmedia_rtcp_fb_cap toPj() const;
};

/** RTCP反馈能力数组。 */
typedef std::vector<RtcpFbCap> RtcpFbCapVector;


/**
 * RTCP反馈设置。
 */
struct RtcpFbConfig : public PersistentObject
{
    /**
     * 指定SDP媒体描述中的传输协议是否使用RTP/AVP而不是RTP/AVPF。
     * 注意，标准要求信号AVPF配置文件，但在与不支持RTCP反馈（包括旧版本PJSIP）
     * 的端点进行协商时可能会导致SDP协商失败。
     *
     * 默认值：false。
     */
    bool                    dontUseAvpf;

    /**
     * RTCP反馈能力。
     */
    RtcpFbCapVector         caps;

public:
    /**
     * 构造函数。
     */
    RtcpFbConfig();

    /**
     * 从pjsip结构体转换。
     */
    void fromPj(const pjmedia_rtcp_fb_setting &prm);

    /**
     * 转换为pjsip结构体。
     */
    pjmedia_rtcp_fb_setting toPj() const;

public:
    /**
     * 从容器节点读取此对象。
     *
     * @param node              从中读取值的容器。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将此对象写入容器节点。
     *
     * @param node              要写入值的容器。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
};

```

## 9.媒体传输安全配置

```c
/**
 * 帐户媒体配置（适用于音频和视频）。将在 AccountConfig 中指定。
 */
struct AccountMediaConfig : public PersistentObject
{
    /**
     * 媒体传输（RTP）配置。
     * 
     * 对于 \a port 和 \a portRange 设置，RTCP 端口选择为 RTP 端口号+1。
     * 示例：\a port=5000，\a portRange=4
     * - 可用端口：5000, 5002, 5004 (媒体/RTP 传输)
     *               5001, 5003, 5005 (媒体/RTCP 传输)
     */
    TransportConfig     transportConfig;

    /**
     * 如果远程发送的 SDP 答复包含多个格式或编解码器的媒体行，发送重新 INVITE 或 UPDATE 以锁定使用的编解码器。
     *
     * 默认：True（是）。
     */
    bool                lockCodecEnabled;

    /**
     * 指定是否为该帐户启用流保活和 NAT 穿透，使用非编解码器-VAD机制（参见 PJMEDIA_STREAM_ENABLE_KA）。
     *
     * 默认：False
     */
    bool                streamKaEnabled;

    /**
     * 指定是否应为该帐户使用安全的媒体传输（SRTP）。
     * 可选值包括 PJMEDIA_SRTP_DISABLED、PJMEDIA_SRTP_OPTIONAL 和 PJMEDIA_SRTP_MANDATORY。
     *
     * 默认：PJSUA_DEFAULT_USE_SRTP
     */
    pjmedia_srtp_use    srtpUse;

    /**
     * 指定是否 SRTP 需要使用安全信令。仅在上述 \a use_srtp 选项非零时使用。
     * 可选值包括：
     *  0：SRTP 不需要安全信令
     *  1：SRTP 需要像 TLS 这样的安全传输
     *  2：SRTP 需要端到端的安全传输（SIPS）
     *
     * 默认：PJSUA_DEFAULT_SRTP_SECURE_SIGNALING
     */
    int                 srtpSecureSignaling;

    /**
     * 指定 SRTP 设置，如加密算法和密钥管理方法。
     */
    SrtpOpt             srtpOpt;

    /**
     * 指定是否在媒体上使用 IPv6。
     *
     * 默认：PJSUA_IPV6_ENABLED_PREFER_IPV4
     * （双栈媒体，优先使用 IPv4。
     * 出站呼叫将优先使用 IPv4）
     */
    pjsua_ipv6_use      ipv6Use;

    /**
     * 启用 RTP 和 RTCP 多路复用。
     *
     * 默认：False
     */
    bool                rtcpMuxEnabled;

    /**
     * RTCP 反馈设置。
     */
    RtcpFbConfig        rtcpFbConfig;

    /**
     * 启用 RTCP 扩展报告（RTCP XR）。
     *
     * 默认：PJMEDIA_STREAM_ENABLE_XR
     */
    bool                rtcpXrEnabled;

    /**
     * 使用回环媒体传输。这在应用程序不希望 PJSUA2 创建真实的媒体传输/套接字时可能很有用，例如使用第三方媒体时。
     *
     * 默认：False
     */
    bool                useLoopMedTp;

    /**
     * 当 \a useLoopMedTp 设置为 TRUE 时，启用本地回环。如果启用，发送到传输的数据包将被发送回附加到传输的流。
     *
     * 默认：False
     */
    bool                enableLoopback;

public:
    /**
     * 默认构造函数
     */
    AccountMediaConfig() 
    : lockCodecEnabled(true),
      streamKaEnabled(false),
      srtpUse(PJSUA_DEFAULT_USE_SRTP),
      srtpSecureSignaling(PJSUA_DEFAULT_SRTP_SECURE_SIGNALING),
      ipv6Use(PJSUA_IPV6_ENABLED_PREFER_IPV4),
      rtcpMuxEnabled(false),
      rtcpXrEnabled(PJMEDIA_STREAM_ENABLE_XR),
      useLoopMedTp(false),
      enableLoopback(false)
    {}

    /**
     * 从容器节点读取该对象的值。
     *
     * @param node              从中读取值的容器。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将该对象的值写入容器节点。
     *
     * @param node              要写入值的容器。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
};

```

## 10.视频通话配置

```c
/**
* 帐户视频配置。
 * 这个结构体定义了帐户视频相关的配置选项，用于控制视频的显示、传输和带宽控制等行为。
 * 它允许用户指定默认行为，例如是否自动显示传入视频、是否自动激活传出视频，
 * 以及默认的设备设置和流控制参数。
 * 这些选项可以根据用户需求进行配置，以确保视频通话行为符合预期并满足应用程序的特定需求。
 */
struct AccountVideoConfig : public PersistentObject
{
    /**
     * 指定是否默认显示传入视频到屏幕。
     * 这适用于传入呼叫（INVITE）、传入重新 INVITE 和传入 UPDATE 请求。
     *
     * 无论此设置如何，应用程序都可以通过实现 \a on_call_media_state() 回调函数
     * 并使用 pjsua_call_get_info() 枚举媒体流来检测传入视频。
     * 一旦识别传入视频，应用程序可以获取与传入视频关联的窗口，并使用
     * pjsua_vid_win_set_show() 显示或隐藏它。
     *
     * 默认：False
     */
    bool                        autoShowIncoming;

    /**
     * 指定是否在默认情况下激活传出视频，当进行传出呼叫和/或检测到传入视频时。
     * 这适用于传入和传出呼叫、传入重新 INVITE 和传入 UPDATE。
     * 如果设置为非零值，传出视频传输将在发送（或接收）对这些请求的响应时开始。
     *
     * 无论此设置的值如何，应用程序都可以使用 pjsua_call_set_vid_strm() 开始和停止传出视频传输。
     *
     * 默认：False
     */
    bool                        autoTransmitOutgoing;

    /**
     * 指定视频窗口的标志。该值是 pjmedia_vid_dev_wnd_flag 的位掩码组合。
     *
     * 默认：0
     */
    unsigned                    windowFlags;

    /**
     * 指定此帐户要使用的默认捕获设备。如果启用了 vidOutAutoTransmit，则将使用此设备进行视频捕获。
     *
     * 默认：PJMEDIA_VID_DEFAULT_CAPTURE_DEV
     */
    pjmedia_vid_dev_index       defaultCaptureDevice;

    /**
     * 指定此帐户要使用的默认渲染设备。
     *
     * 默认：PJMEDIA_VID_DEFAULT_RENDER_DEV
     */
    pjmedia_vid_dev_index       defaultRenderDevice;

    /**
     * 码率控制方法。
     *
     * 默认：PJMEDIA_VID_STREAM_RC_SIMPLE_BLOCKING
     */
    pjmedia_vid_stream_rc_method rateControlMethod;

    /**
     * 上行/传出带宽。如果设置为零，则视频流将使用编解码器的最大比特率设置。
     *
     * 默认：0（遵循编解码器的最大比特率）。
     */
    unsigned                    rateControlBandwidth;

    /**
     * 在创建流后发送的关键帧数。
     *
     * 默认：PJMEDIA_VID_STREAM_START_KEYFRAME_CNT
     */
    unsigned                    startKeyframeCount;

    /**
     * 创建流后的关键帧发送间隔。
     *
     * 默认：PJMEDIA_VID_STREAM_START_KEYFRAME_INTERVAL_MSEC
     */
    unsigned                    startKeyframeInterval;

public:
    /**
     * 默认构造函数
     */
    AccountVideoConfig() 
    : autoShowIncoming(false),
      autoTransmitOutgoing(false),
      windowFlags(0),
      defaultCaptureDevice(PJMEDIA_VID_DEFAULT_CAPTURE_DEV),
      defaultRenderDevice(PJMEDIA_VID_DEFAULT_RENDER_DEV),
      rateControlMethod(PJMEDIA_VID_STREAM_RC_SIMPLE_BLOCKING),
      rateControlBandwidth(0),
      startKeyframeCount(PJMEDIA_VID_STREAM_START_KEYFRAME_CNT),
      startKeyframeInterval(PJMEDIA_VID_STREAM_START_KEYFRAME_INTERVAL_MSEC)
    {}

    /**
     * 从容器节点读取该对象的值。
     *
     * @param node              从中读取值的容器。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将该对象的值写入容器节点。
     *
     * @param node              要写入值的容器。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
};

 */
struct AccountVideoConfig : public PersistentObject
{
    /**
     * 指定是否默认显示传入视频到屏幕。
     * 这适用于传入呼叫（INVITE）、传入重新 INVITE 和传入 UPDATE 请求。
     *
     * 无论此设置如何，应用程序都可以通过实现 \a on_call_media_state() 回调函数
     * 并使用 pjsua_call_get_info() 枚举媒体流来检测传入视频。
     * 一旦识别传入视频，应用程序可以获取与传入视频关联的窗口，并使用
     * pjsua_vid_win_set_show() 显示或隐藏它。
     *
     * 默认：False
     */
    bool                        autoShowIncoming;

    /**
     * 指定是否在默认情况下激活传出视频，当进行传出呼叫和/或检测到传入视频时。
     * 这适用于传入和传出呼叫、传入重新 INVITE 和传入 UPDATE。
     * 如果设置为非零值，传出视频传输将在发送（或接收）对这些请求的响应时开始。
     *
     * 无论此设置的值如何，应用程序都可以使用 pjsua_call_set_vid_strm() 开始和停止传出视频传输。
     *
     * 默认：False
     */
    bool                        autoTransmitOutgoing;

    /**
     * 指定视频窗口的标志。该值是 pjmedia_vid_dev_wnd_flag 的位掩码组合。
     *
     * 默认：0
     */
    unsigned                    windowFlags;

    /**
     * 指定此帐户要使用的默认捕获设备。如果启用了 vidOutAutoTransmit，则将使用此设备进行视频捕获。
     *
     * 默认：PJMEDIA_VID_DEFAULT_CAPTURE_DEV
     */
    pjmedia_vid_dev_index       defaultCaptureDevice;

    /**
     * 指定此帐户要使用的默认渲染设备。
     *
     * 默认：PJMEDIA_VID_DEFAULT_RENDER_DEV
     */
    pjmedia_vid_dev_index       defaultRenderDevice;

    /**
     * 码率控制方法。
     *
     * 默认：PJMEDIA_VID_STREAM_RC_SIMPLE_BLOCKING
     */
    pjmedia_vid_stream_rc_method rateControlMethod;

    /**
     * 上行/传出带宽。如果设置为零，则视频流将使用编解码器的最大比特率设置。
     *
     * 默认：0（遵循编解码器的最大比特率）。
     */
    unsigned                    rateControlBandwidth;

    /**
     * 在创建流后发送的关键帧数。
     *
     * 默认：PJMEDIA_VID_STREAM_START_KEYFRAME_CNT
     */
    unsigned                    startKeyframeCount;

    /**
     * 创建流后的关键帧发送间隔。
     *
     * 默认：PJMEDIA_VID_STREAM_START_KEYFRAME_INTERVAL_MSEC
     */
    unsigned                    startKeyframeInterval;

public:
    /**
     * 默认构造函数
     */
    AccountVideoConfig() 
    : autoShowIncoming(false),
      autoTransmitOutgoing(false),
      windowFlags(0),
      defaultCaptureDevice(PJMEDIA_VID_DEFAULT_CAPTURE_DEV),
      defaultRenderDevice(PJMEDIA_VID_DEFAULT_RENDER_DEV),
      rateControlMethod(PJMEDIA_VID_STREAM_RC_SIMPLE_BLOCKING),
      rateControlBandwidth(0),
      startKeyframeCount(PJMEDIA_VID_STREAM_START_KEYFRAME_CNT),
      startKeyframeInterval(PJMEDIA_VID_STREAM_START_KEYFRAME_INTERVAL_MSEC)
    {}

    /**
     * 从容器节点读取该对象的值。
     *
     * @param node              从中读取值的容器。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将该对象的值写入容器节点。
     *
     * @param node              要写入值的容器。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
};

```

## 11.IP变更配置

```c
/*
 * 帐户IP变更配置。
 * 这个结构体定义了在帐户IP地址变更时的配置选项，用于控制帐户的行为，包括是否关闭注册使用的传输、
 * 是否挂断关联的活动呼叫，以及在重新注册完成后发送重新邀请的标志设置。
 * 这些选项允许用户根据需求配置帐户在IP地址变更时的行为，以确保通话和注册的稳定性和一致性。
*/
typedef struct AccountIpChangeConfig
{    
    /**
     * 关闭用于账号注册的传输通道。如果设置为 PJ_TRUE，则会关闭传输通道，
     * 即使它被多个账号使用。关闭传输通道后，如果 AccountConfig.natConfig.contactRewriteUse 启用，
     * 将会跟随重新注册。
     *
     * 默认值：true
     */
    bool                shutdownTp;

    /**
     * 挂断与账号关联的活动呼叫。如果设置为 true，则会挂断呼叫。
     *
     * 默认值：false
     */
    bool                hangupCalls;

    /**
     * 指定在重新注册完成后，当 hangupCalls 设置为 false 时，发送 re-INVITE 所使用的呼叫标志。
     * 如果设置为 0，则不会发送 re-INVITE。重新注册完成后将会发送 re-INVITE。
     *
     * 默认值：PJSUA_CALL_REINIT_MEDIA | PJSUA_CALL_UPDATE_CONTACT | PJSUA_CALL_UPDATE_VIA
     */
    unsigned            reinviteFlags;

    /**
     * 刷新呼叫时，使用 SIP UPDATE 而不是 re-INVITE，如果远程支持（通过在 Allow 头中发布）。
     * 如果远程不支持 UPDATE 方法或某种原因导致 UPDATE 尝试失败，则会回退到使用 re-INVITE。
     * 无论发送 re-INVITE 还是 UPDATE，都将使用 reinviteFlags。
     *
     * 默认值：PJ_FALSE（使用 re-INVITE）。
     */
    unsigned            reinvUseUpdate;

public:
    /**
     * 虚析构函数
     */
    virtual ~AccountIpChangeConfig()
    {}

    /**
     * 从容器节点中读取此对象的值。
     *
     * @param node 容器节点，从中读取值。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将此对象的值写入容器节点。
     *
     * @param node 容器节点，将值写入其中。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
    
} AccountIpChangeConfig;

```

## 12.账号配置

```c
/**
 * 账号配置。
 * 
 * 该结构体定义了一个账号的配置信息，包括优先级、地址或AOR、注册设置、SIP设置、呼叫设置、
 * Presence设置、MWI设置、NAT设置、媒体设置、视频设置和IP变更设置。
 * 这些配置用于管理账号在SIP通信中的行为和功能。
 */
struct AccountConfig : public PersistentObject
{
    /**
     * 账号优先级，用于控制匹配传入/传出请求的顺序。数字越高，优先级越高，
     * 账号将首先匹配。
     */
    int                 priority;

    /**
     * 记录地址或AOR（Address of Record），是完整的SIP URL，用于标识账号。
     * 可以是名称地址或URL格式，类似于 "sip:account@serviceprovider"。
     *
     * 此字段为必填项。
     */
    string              idUri;

    /**
     * 注册设置。
     */
    AccountRegConfig    regConfig;

    /**
     * SIP设置。
     */
    AccountSipConfig    sipConfig;

    /**
     * 呼叫设置。
     */
    AccountCallConfig   callConfig;

    /**
     * Presence设置。
     */
    AccountPresConfig   presConfig;

    /**
     * MWI（消息等待指示）设置。
     */
    AccountMwiConfig    mwiConfig;

    /**
     * NAT设置。
     */
    AccountNatConfig    natConfig;

    /**
     * 媒体设置（适用于音频和视频）。
     */
    AccountMediaConfig  mediaConfig;

    /**
     * 视频设置。
     */
    AccountVideoConfig  videoConfig;

    /**
     * IP变更设置。
     */
    AccountIpChangeConfig ipChangeConfig;

public:
    /**
     * 默认构造函数将使用默认值进行初始化。
     */
    AccountConfig();

    /**
     * 返回一个临时的 pjsua_acc_config 实例，其内容仅在此 AccountConfig 结构有效的
     * 情况下有效，并且没有对其进行修改，并且没有进行进一步的 toPj() 函数调用。
     * 对 toPj() 函数的任何调用将使前一个调用返回的临时 pjsua_acc_config 的内容无效。
     */
    void toPj(pjsua_acc_config &cfg) const;

    /**
     * 从 pjsip 初始化。
     */
    void fromPj(const pjsua_acc_config &prm, const pjsua_media_config *mcfg);

    /**
     * 从容器节点中读取此对象的值。
     *
     * @param node 从中读取值的容器。
     */
    virtual void readObject(const ContainerNode &node) PJSUA2_THROW(Error);

    /**
     * 将此对象的值写入容器节点。
     *
     * @param node 将值写入其中的容器。
     */
    virtual void writeObject(ContainerNode &node) const PJSUA2_THROW(Error);
};

```

## 13.用户信息

```c
/**
 * 账号信息。应用程序可以通过调用 Account::getInfo() 查询账号信息。
 */
struct AccountInfo
{
    /**
     * 账号ID。
     */
    pjsua_acc_id        id;

    /**
     * 指示是否为默认账号的标志。
     */
    bool                isDefault;

    /**
     * 账号URI。
     */
    string              uri;

    /**
     * 指示此账号是否具有注册设置（reg_uri 不为空）的标志。
     */
    bool                regIsConfigured;

    /**
     * 指示此账号当前是否已注册（具有活动的注册会话）的标志。
     */
    bool                regIsActive;

    /**
     * 账号注册会话的最新过期间隔。
     */
    unsigned            regExpiresSec;

    /**
     * 最后的注册状态码。如果状态码为零，则表示账号当前未注册。任何其他值表示注册的 SIP
     * 状态码。
     */
    pjsip_status_code   regStatus;

    /**
     * 描述注册状态的字符串。
     */
    string              regStatusText;

    /**
     * 最后的注册错误码。当状态字段包含指示注册失败的 SIP 状态码时，最后的注册错误码包含导致
     * 失败的错误码。在任何其他情况下，其值为零。
     */
    pj_status_t         regLastErr;

    /**
     * 此账号的在线状态。
     */
    bool                onlineStatus;

    /**
     * 在线状态的文本描述。
     */
    string              onlineStatusText;

public:
    /**
     * 默认构造函数。
     */
    AccountInfo() : id(PJSUA_INVALID_ID), 
                    isDefault(false),
                    regIsConfigured(false),
                    regIsActive(false),
                    regExpiresSec(0),
                    regStatus(PJSIP_SC_NULL),
                    regLastErr(-1),
                    onlineStatus(false)
    {}

    /** 从 pjsip 数据导入 */
    void fromPj(const pjsua_acc_info &pai);
};

```

## 14.回调函数参数

```c
/**
 * 用于传递来电参数的结构体。
 */
struct OnIncomingCallParam
{
    /**
     * 为新呼叫分配的库呼叫ID。
     */
    int                 callId;

    /**
     * 接收到的INVITE请求。
     */
    SipRxData           rdata;
};
/**
 * 用于 onRegStarted() 账户回调的参数结构体。
 */
struct OnRegStartedParam
{
    /**
     * 是否是注册操作。True表示注册，False表示注销。
     */
    bool renew;
};

/**
 * 用于 onRegState() 账户回调的参数结构体。
 */
struct OnRegStateParam
{
    /**
     * 注册操作状态。
     */
    pj_status_t         status;

    /**
     * 收到的SIP状态码。
     */
    pjsip_status_code   code;

    /**
     * 收到的SIP原因短语。
     */
    string              reason;

    /**
     * 收到的消息。
     */
    SipRxData           rdata;

    /**
     * 下一个过期间隔。
     */
    unsigned            expiration;
};

/**
 * 用于 onIncomingSubscribe() 回调的参数结构体。
 */
struct OnIncomingSubscribeParam
{
    /**
     * 服务器存在订阅实例。如果应用程序延迟接受请求，则需要在调用 Account::presNotify() 时指定此对象。
     */
    void               *srvPres;

    /**
     * 发送者URI。
     */
    string              fromUri;

    /**
     * 收到的消息。
     */
    SipRxData           rdata;

    /**
     * 响应请求的状态码。默认值为200。应用程序可以将其设置为其他最终状态码以接受或拒绝请求。
     */
    pjsip_status_code   code;

    /**
     * 响应请求的原因短语。
     */
    string              reason;

    /**
     * 响应中要发送的附加数据（如果有）。
     */
    SipTxOption         txOption;
};

/**
 * 用于 onInstantMessage() 账户回调的参数结构体。
 */
struct OnInstantMessageParam
{
    /**
     * 发送者From URI。
     */
    string              fromUri;

    /**
     * 请求的To URI。
     */
    string              toUri;

    /**
     * 发送者的联系URI。
     */
    string              contactUri;

    /**
     * 消息体的MIME类型。
     */
    string              contentType;

    /**
     * 消息体。
     */
    string              msgBody;

    /**
     * 完整的消息。
     */
    SipRxData           rdata;
};

/**
 * 用于 onInstantMessageStatus() 账户回调的参数结构体。
 */
struct OnInstantMessageStatusParam
{
    /**
     * 与传呼机传输相关联的令牌或用户数据。
     */
    Token               userData;

    /**
     * 目标URI。
     */
    string              toUri;

    /**
     * 消息体。
     */
    string              msgBody;

    /**
     * 事务的SIP状态码。
     */
    pjsip_status_code   code;

    /**
     * 事务的原因短语。
     */
    string              reason;

    /**
     * 引起此回调被调用的传入响应。如果由于超时或传输错误导致事务失败，则内容将为空。
     */
    SipRxData           rdata;
};

/**
 * 用于 onTypingIndication() 账户回调的参数结构体。
 */
struct OnTypingIndicationParam
{
    /**
     * 发送者/From URI。
     */
    string              fromUri;

    /**
     * To URI。
     */
    string              toUri;

    /**
     * 联系URI。
     */
    string              contactUri;

    /**
     * 表示发送者是否正在输入的布尔值。
     */
    bool                isTyping;

    /**
     * 完整的消息缓冲区。
     */
    SipRxData           rdata;
};

/**
 * 用于 onMwiInfo() 账户回调的参数结构体。
 */
struct OnMwiInfoParam
{
    /**
     * MWI订阅状态。
     */
    pjsip_evsub_state   state;

    /**
     * 完整的消息缓冲区。
     */
    SipRxData           rdata;
};

/**
 * 用于 presNotify() 账户方法的参数结构体。
 */
struct PresNotifyParam
{
    /**
     * 服务器存在的状态订阅实例。
     */
    void               *srvPres;

    /**
     * 要设置的服务器存在订阅状态。
     */
    pjsip_evsub_state   state;
    
    /**
     * 如果状态不是“active”、“pending”或“terminated”，可选地指定状态字符串名称。
     */
    string              stateStr;

    /**
     * 如果新状态为PJSIP_EVSUB_STATE_TERMINATED，则可选地指定终止原因。
     */
    string              reason;

    /**
     * 如果新状态为PJSIP_EVSUB_STATE_TERMINATED，则指定NOTIFY请求是否应包含包含账户存在信息的消息体。
     */
    bool                withBody;

    /**
     * 要与NOTIFY请求一起发送的可选标头列表。
     */
    SipTxOption         txOption;
};

```

## 15.发现好友

```c
class FindBuddyMatch
{
public:
    /**
     * 默认算法实现。
     *
     * @param token 查找的令牌。
     * @param buddy 要匹配的好友。
     * @return 如果好友信息中的URI包含令牌，则返回true；否则返回false。
     */
    virtual bool match(const string &token, const Buddy &buddy)
    {
        BuddyInfo bi = buddy.getInfo();
        return bi.uri.find(token) != string::npos;
    }

    /**
     * 析构函数。
     */
    virtual ~FindBuddyMatch() {}
};

```

## 16.Account

```c
/**
 * 账户。
 */
class Account
{
public:
    /**
     * 构造函数。
     */
    Account();

    /**
     * 析构函数。请注意，如果删除账户，将同时删除PJSUA-LIB中对应的账户。
     * 如果应用程序实现了派生类，则派生类的析构函数应在开始阶段调用shutdown()，
     * 或者应用程序在删除派生类实例之前调用shutdown()。这是为了避免派生类析构函数
     * 与Account回调之间的竞态条件。
     */
    virtual ~Account();

    /**
     * 创建账户。
     *
     * 如果应用程序实现了派生类，则派生类应在开始阶段调用shutdown()，
     * 或者应用程序在删除派生类实例之前调用shutdown()。这是为了避免派生类析构函数
     * 与Account回调之间的竞态条件。
     *
     * @param cfg               账户配置。
     * @param make_default      是否将此账户设置为默认账户。
     */
    void create(const AccountConfig &cfg,
                bool make_default=false) PJSUA2_THROW(Error);

    /**
     * 关闭账户。这将启动注销过程（如果需要），并删除PJSUA-LIB中对应的账户。
     *
     * 请注意，应用程序必须在关闭账户之前删除所有属于该账户的Buddy实例。
     *
     * 如果应用程序实现了派生类，则派生类的析构函数应在开始阶段调用此方法，
     * 或者应用程序在删除派生类实例之前调用此方法。这是为了避免派生类析构函数
     * 与Account回调之间的竞态条件。
     */
    void shutdown();

    /**
     * 修改账户以使用指定的账户配置。根据更改的内容，这可能导致重新注册或重新注册
     * 账户。
     *
     * @param cfg               要应用于账户的新账户配置。
     */
    void modify(const AccountConfig &cfg) PJSUA2_THROW(Error);

    /**
     * 检查此账户是否仍然有效。
     *
     * @return                  如果有效则返回true。
     */
    bool isValid() const;

    /**
     * 将此账户设置为默认账户，用于当传入和传出请求不匹配任何账户时使用。
     */
    void setDefault() PJSUA2_THROW(Error);

    /**
     * 检查此账户是否为默认账户。默认账户将用于不匹配任何其他账户的传入和传出请求。
     *
     * @return                  如果此账户为默认账户则返回true。
     */
    bool isDefault() const;

    /**
     * 获取与此账户关联的PJSUA-LIB账户ID或索引。
     *
     * @return                  大于或等于零的整数。
     */
    int getId() const;

    /**
     * 根据指定的账户ID获取Account类。
     *
     * @param acc_id            要查找的账户ID。
     *
     * @return                  Account实例，如果未找到则返回NULL。
     */
    static Account *lookup(int acc_id);

    /**
     * 获取账户信息。
     *
     * @return                  账户信息。
     */
    AccountInfo getInfo() const PJSUA2_THROW(Error);

    /**
     * 更新注册或执行注销。通常，应用程序只需要在手动更新注册或从服务器注销时调用此函数。
     *
     * @param renew             如果为False，则会启动注销过程。
     */
    void setRegistration(bool renew) PJSUA2_THROW(Error);

    /**
     * 将账户的在线状态设置为广告给远程/出席订阅者。如果此账户的服务器端存在出席订阅，
     * 或者此账户启用了出席发布，则会触发发送出站NOTIFY请求。
     *
     * @param pres_st           在线状态。
     */
    void setOnlineStatus(const PresenceStatus &pres_st) PJSUA2_THROW(Error);

    /**
     * 锁定/绑定此账户到特定的传输/侦听器。通常，应用程序不需要这样做，因为传输将根据目的地
     * 自动选择库。当账户被锁定/绑定到特定传输时，此账户的所有传出请求将使用指定的传输
     * （包括SIP注册、对话（呼叫和事件订阅）以及消息之类的出对话请求）。
     *
     * 请注意，传输ID也可以在AccountConfig中指定。
     *
     * @param tp_id             传输ID。
     */
    void setTransport(TransportId tp_id) PJSUA2_THROW(Error);

    /**
     * 发送NOTIFY通知账户的在线状态，或终止服务器端出席订阅。如果应用程序想要拒绝传入请求，
     * 应将参数prm.PresNotifyParam.state设置为PJSIP_EVSUB_STATE_TERMINATED。
     *
     * @param prm               发送NOTIFY的参数。
     */
    void presNotify(const PresNotifyParam &prm) PJSUA2_THROW(Error);
    
#if !DEPRECATED_FOR_TICKET_2232
    /**
     * 警告：已弃用，请改用enumBuddies2()。此函数在多线程环境中不安全。
     *
     * 枚举账户的所有Buddy。
     *
     * @return                  Buddy列表。
     */
    const BuddyVector& enumBuddies() const PJSUA2_THROW(Error);
#endif

    /**
     * 枚举账户的所有Buddy。
     *
     * @return                  Buddy列表。
     */
    BuddyVector2 enumBuddies2() const PJSUA2_THROW(Error);

#if !DEPRECATED_FOR_TICKET_2232
    /**
     * 警告：已弃用，请改用findBuddy2()。此函数在多线程环境中不安全。
     *
     * 查找账户Buddy列表中具有指定URI的Buddy。
     *
     * 异常：如果未找到Buddy，则会抛出PJ_ENOTFOUND。
     *
     * @param uri               Buddy URI。
     * @param buddy_match       Buddy匹配算法。
     *
     * @return                  指向Buddy的指针。
     */
    Buddy* findBuddy(string uri, FindBuddyMatch *buddy_match = NULL) const
                    PJSUA2_THROW(Error);
#endif

    /**
     * 查找账户Buddy列表中具有指定URI的Buddy。
     *
     * 异常：如果未找到Buddy，则会抛出PJ_ENOTFOUND。
     *
     * @param uri               Buddy URI。
     *
     * @return                  Buddy对象。
     */
    Buddy findBuddy2(string uri) const PJSUA2_THROW(Error);

public:
    /*
     * 回调函数
     */
    /**
     * 通知应用程序有传入呼叫。
     *
     * @param prm       回调参数。
     */
    virtual void onIncomingCall(OnIncomingCallParam &prm)
    { PJ_UNUSED_ARG(prm); }

    /**
     * 通知应用程序已启动注册或注销。请注意，这仅通知初始注册和注销。一旦注册会话
     * 激活，后续的刷新将不会导致调用此回调。
     *
     * @param prm           回调参数。
     */
    virtual void onRegStarted(OnRegStartedParam &prm)
    { PJ_UNUSED_ARG(prm); }

    /**
     * 通知应用程序注册状态已更改。应用程序可以查询账户信息以获取注册详细信息。
     *
     * @param prm           回调参数。
     */
    virtual void onRegState(OnRegStateParam &prm)
    { PJ_UNUSED_ARG(prm); }

    /**
     * 当收到传入SUBSCRIBE请求时通知应用程序。应用程序可以使用此回调来授权传入订阅
     * 请求（例如，询问用户是否应授予请求）。
     *
     * 如果未实现此回调，则将接受所有传入的出席订阅请求。
     *
     * 如果实现了此回调，应用程序对传入请求的处理有几种选择：
     *  - 可以通过在IncomingSubscribeParam.code参数中指定非200类最终响应来立即拒绝请求。
     *  - 可以通过在IncomingSubscribeParam.code参数中指定200来立即接受请求。如果
     *    应用程序没有为IncomingSubscribeParam.code参数设置任何值，则默认值为200。
     *    在这种情况下，库将在从此回调返回后自动发送NOTIFY请求。
     *  - 可以延迟处理请求，例如请求用户是否接受或拒绝请求。在这种情况下，应用程序
     *    必须将IncomingSubscribeParam.code参数设置为202，然后立即调用presNotify()
     *    发送PJSIP_EVSUB_STATE_PENDING状态，并稍后再次调用presNotify()接受或拒绝
     *    订阅请求。
     *
     * 任何非200和202的IncomingSubscribeParam.code都将被视为200。
     *
     * 应用程序必须立即从此回调返回（例如，不能在等待用户确认时在此回调中阻塞）。
     *
     * @param prm           回调参数。
     */
    virtual void onIncomingSubscribe(OnIncomingSubscribeParam &prm)
    { PJ_UNUSED_ARG(prm); }

    /**
     * 当接收到外部呼叫上的即时消息或传呼机（即MESSAGE请求）时通知应用程序。
     *
     * @param prm           回调参数。
     */
    virtual void onInstantMessage(OnInstantMessageParam &prm)
    { PJ_UNUSED_ARG(prm); }

    /**
     * 通知应用程序有传呼机/即时消息（即MESSAGE）请求的传送状态。
     *
     * @param prm           回调参数。
     */
    virtual void onInstantMessageStatus(OnInstantMessageStatusParam &prm)
    { PJ_UNUSED_ARG(prm); }

    /**
     * 通知应用程序有输入指示。
     *
     * @param prm           回调参数。
     */
    virtual void onTypingIndication(OnTypingIndicationParam &prm)
    { PJ_UNUSED_ARG(prm); }

    /**
     * 关于MWI（消息等待指示）状态更改的通知。可以在SUBSCRIBE请求的状态更改（例如，
     * 收到202/Accepted以接收SUBSCRIBE）时调用此回调，或者在收到NOTIFY请求时调用。
     *
     * @param prm           回调参数。
     */
    virtual void onMwiInfo(OnMwiInfoParam &prm)
    { PJ_UNUSED_ARG(prm); }

private:
    friend class Endpoint;
    friend class Buddy;

    /**
     * 内部函数，将Buddy添加到Account的Buddy列表中。此方法由Buddy::create()使用。
     */
    void addBuddy(Buddy *buddy);

    /**
     * 内部函数，从Account的Buddy列表中移除Buddy。此方法由Buddy::~Buddy()使用。
     */
    void removeBuddy(Buddy *buddy);

private:
    pjsua_acc_id         id;
    string               tmpReason;     // 用于保存响应的原因
#if !DEPRECATED_FOR_TICKET_2232
    BuddyVector          buddyList;
#endif
};

```

**Constructor 和 Destructor**:

- **Constructor**: 用于创建 `Account` 对象。
- **Destructor**: 如果销毁 `Account` 对象，会同时删除对应的 PJSUA-LIB 中的账户。建议在派生类的析构函数中调用 `shutdown()`，或者在删除 `Account` 对象实例之前调用 `shutdown()`，以避免派生类析构函数与 `Account` 的回调之间的竞争条件。

**create 方法**:

- 创建一个账户，根据提供的 `AccountConfig` 配置。
- 可选地将该账户设置为默认账户。

**shutdown 方法**:

- 关闭账户，如果需要，会启动注销过程，并在 PJSUA-LIB 中删除对应的账户。
- 在关闭账户之前，应确保删除属于该账户的所有 `Buddy` 实例。

**modify 方法**:

- 修改账户的配置，可能会导致重新注册或重新注销账户。

**isValid 方法**:

- 检查该账户是否仍然有效。

**setDefault 和 isDefault 方法**:

- **setDefault**: 将该账户设置为默认账户，用于未匹配到其他账户的传入和传出请求。
- **isDefault**: 检查该账户是否为默认账户。

**getId 方法**:

- 获取与该账户关联的 PJSUA-LIB 账户 ID 或索引。

**lookup 静态方法**:

- 根据账户 ID 查找并返回对应的 `Account` 实例，如果未找到则返回 NULL。

**getInfo 方法**:

- 获取账户的详细信息，包括 ID、URI、注册状态等。

**setRegistration 方法**:

- 手动更新账户的注册状态或执行注销操作。

**setOnlineStatus 方法**:

- 设置或修改账户的在线状态，用于广告到远程或者出席订阅者。

**setTransport 方法**:

- 锁定/绑定该账户到特定的传输/监听器。

**presNotify 方法**:

- 发送 NOTIFY 请求，通知账户的出席状态或终止服务器端出席订阅。

**enumBuddies2 和 findBuddy2 方法**:

- **enumBuddies2**: 枚举该账户的所有好友列表。
- **findBuddy2**: 根据指定的 URI 在好友列表中查找并返回对应的 `Buddy` 对象。

**Callbacks**:

- 这些是虚函数，用于处理账户的各种事件和回调通知，例如来电、注册状态变化、即时消息等。
- 每个回调函数的参数类型不同，适用于不同类型的事件处理。