# 一、前置知识

## 1.媒体类型

```c
/**
 * pjmedia_type 枚举类型用于定义媒体的类型。
 */
typedef enum pjmedia_type
{
    /** 未指定类型。 */
    PJMEDIA_TYPE_NONE,

    /** 媒体类型为音频。 */
    PJMEDIA_TYPE_AUDIO,

    /** 媒体类型为视频。 */
    PJMEDIA_TYPE_VIDEO,

    /** 媒体类型为应用程序。 */
    PJMEDIA_TYPE_APPLICATION,

    /** 媒体类型未知或不支持。 */
    PJMEDIA_TYPE_UNKNOWN

} pjmedia_type;

```

```c

/** Array of MediaFormatAudio */
typedef std::vector<MediaFormatAudio> MediaFormatAudioVector;

/** Array of MediaFormatVideo */
typedef std::vector<MediaFormatVideo> MediaFormatVideoVector;

/** 
 * Warning: deprecated, use AudioMediaVector2 instead.
 *
 * Array of Audio Media.
 */
typedef std::vector<AudioMedia*> AudioMediaVector;


/** Array of Audio Media */
typedef std::vector<AudioMedia> AudioMediaVector2;
```

## 2.媒体事件类型

```c
/**
 * 此枚举描述了媒体事件的列表。
 */
typedef enum pjmedia_event_type
{
    /**
     * 没有事件。
     */
    PJMEDIA_EVENT_NONE,

    /**
     * 媒体格式已更改事件。
     */
    PJMEDIA_EVENT_FMT_CHANGED   = PJMEDIA_FOURCC('F', 'M', 'C', 'H'),

    /**
     * 视频窗口正在关闭事件。
     */
    PJMEDIA_EVENT_WND_CLOSING   = PJMEDIA_FOURCC('W', 'N', 'C', 'L'),

    /**
     * 视频窗口已关闭事件。
     */
    PJMEDIA_EVENT_WND_CLOSED    = PJMEDIA_FOURCC('W', 'N', 'C', 'O'),

    /**
     * 视频窗口已调整大小事件。
     */
    PJMEDIA_EVENT_WND_RESIZED   = PJMEDIA_FOURCC('W', 'N', 'R', 'Z'),

    /**
     * 鼠标按钮按下事件。
     */
    PJMEDIA_EVENT_MOUSE_BTN_DOWN = PJMEDIA_FOURCC('M', 'S', 'D', 'N'),

    /**
     * 视频关键帧刚刚被解码事件。
     */
    PJMEDIA_EVENT_KEYFRAME_FOUND = PJMEDIA_FOURCC('I', 'F', 'R', 'F'),

    /**
     * 视频解码错误，缺少关键帧事件。
     */
    PJMEDIA_EVENT_KEYFRAME_MISSING = PJMEDIA_FOURCC('I', 'F', 'R', 'M'),

    /**
     * 视频方向已更改事件。
     */
    PJMEDIA_EVENT_ORIENT_CHANGED = PJMEDIA_FOURCC('O', 'R', 'N', 'T'),

    /**
     * 收到了 RTCP-FB。
     */
    PJMEDIA_EVENT_RX_RTCP_FB = PJMEDIA_FOURCC('R', 'T', 'F', 'B'),

    /**
     * 音频设备发生停止错误事件。
     */
    PJMEDIA_EVENT_AUD_DEV_ERROR = PJMEDIA_FOURCC('A', 'E', 'R', 'R'),

    /**
     * 视频设备发生停止错误事件。
     */
    PJMEDIA_EVENT_VID_DEV_ERROR = PJMEDIA_FOURCC('V', 'E', 'R', 'R'),

    /**
     * 传输媒体错误事件。
     */
    PJMEDIA_EVENT_MEDIA_TP_ERR = PJMEDIA_FOURCC('T', 'E', 'R', 'R'),

    /**
     * 回调事件。目前仅供内部使用。
     */
    PJMEDIA_EVENT_CALLBACK = PJMEDIA_FOURCC('C', 'B', ' ', ' ')

} pjmedia_event_type;

```



# 二、媒体类

## 1.MediaFormat

```c
/**
 * 该结构体包含了描述一个媒体所需的全部信息。
 */
struct MediaFormat
{
    /**
     * 格式ID，用于指定音频采样或视频像素格式。
     * 一些常用的格式ID在 pjmedia_format_id 枚举中声明。
     *
     * @see pjmedia_format_id
     */
    pj_uint32_t id;

    /**
     * 媒体的最高级类型，用作信息标识。
     */
    pjmedia_type type;

public:
    /**
     * 默认构造函数
     * 初始化格式ID为0，媒体类型为 PJMEDIA_TYPE_NONE。
     */
    MediaFormat() : id(0), type(PJMEDIA_TYPE_NONE)
    {}
};

```



## 2.MediaFormatAudio音频媒体格式

```c
/**
 * MediaFormatAudio 结构体用于描述音频媒体的格式。
 * 它继承自 MediaFormat 基类，并包含了音频特有的参数。
 */
struct MediaFormatAudio : public MediaFormat
{
    unsigned    clockRate;      /**< 音频采样率，以样本/秒或 Hz 为单位。 */
    unsigned    channelCount;   /**< 声道数。 */
    unsigned    frameTimeUsec;  /**< 帧间隔，以微秒为单位。 */
    unsigned    bitsPerSample;  /**< 每个样本的位数。 */
    pj_uint32_t avgBps;         /**< 平均比特率。 */
    pj_uint32_t maxBps;         /**< 最大比特率。 */

    /**
     * 从 pjmedia_format 结构体构造。
     *
     * @param format            pjmedia_format 结构体。
     */
    void fromPj(const pjmedia_format &format);

    /**
     * 导出到 pjmedia_format 结构体。
     *
     * @return                  pjmedia_format 结构体。
     */
    pjmedia_format toPj() const;

public:
    /**
     * 默认构造函数
     * 初始化所有成员变量为零。
     */
    MediaFormatAudio() : clockRate(0), channelCount(0), frameTimeUsec(0),
                         bitsPerSample(0), avgBps(0), maxBps(0)
    {}

    /**
     * 初始化函数
     *
     * @param formatId          媒体格式 ID。
     * @param clockRate         音频采样率，以样本/秒或 Hz 为单位。
     * @param channelCount      声道数。
     * @param frameTimeUsec     帧间隔，以微秒为单位。
     * @param bitsPerSample     每个样本的位数。
     * @param avgBps            平均比特率，默认为0。
     * @param maxBps            最大比特率，默认为0。
     */
    void init(pj_uint32_t formatId, unsigned clockRate, unsigned channelCount,
              int frameTimeUsec, int bitsPerSample,
              pj_uint32_t avgBps=0, pj_uint32_t maxBps=0);
};

```

- `MediaFormatAudio` 结构体用于描述音频媒体的格式，包含了音频特有的参数。
- 它继承自 `MediaFormat` 基类，添加了音频特有的成员变量，如采样率、声道数、帧间隔、每个样本的位数、平均比特率和最大比特率。
- 通过 `fromPj` 函数，可以从 `pjmedia_format` 结构体构造 `MediaFormatAudio` 对象。
- 通过 `toPj` 函数，可以将 `MediaFormatAudio` 对象导出为 `pjmedia_format` 结构体。
- `init` 函数用于初始化 `MediaFormatAudio` 对象的成员变量，提供了一个简便的方法来设置音频格式的各个参数。

## 3. MediaFormatVideo视频媒体格式

```c
/**
 * 该结构体描述视频媒体的详细信息。
 */
struct MediaFormatVideo : public MediaFormat
{
    unsigned            width;      /**< 视频宽度。                      */
    unsigned            height;     /**< 视频高度。                      */
    int                 fpsNum;     /**< 每秒帧数的分子。                */
    int                 fpsDenum;   /**< 每秒帧数的分母。                */
    pj_uint32_t         avgBps;     /**< 平均比特率。                    */
    pj_uint32_t         maxBps;     /**< 最大比特率。                    */

    /**
     * 从 pjmedia_format 结构体构造。
     *
     * @param format            pjmedia_format 结构体。
     */
    void fromPj(const pjmedia_format &format);

    /**
     * 导出到 pjmedia_format 结构体。
     *
     * @return                  pjmedia_format 结构体。
     */
    pjmedia_format toPj() const;

public:
    /**
     * 默认构造函数
     * 初始化所有成员变量为零。
     */
    MediaFormatVideo() : width(0), height(0), fpsNum(0),
                         fpsDenum(1), avgBps(0), maxBps(0)
    {}

    /**
     * 初始化函数
     *
     * @param formatId          媒体格式 ID。
     * @param width             视频宽度。
     * @param height            视频高度。
     * @param fpsNum            每秒帧数的分子。
     * @param fpsDenum          每秒帧数的分母，默认为1。
     * @param avgBps            平均比特率，默认为0。
     * @param maxBps            最大比特率，默认为0。
     */
    void init(pj_uint32_t formatId, unsigned width, unsigned height,
              int fpsNum, int fpsDenum=1,
              pj_uint32_t avgBps=0, pj_uint32_t maxBps=0);
};

```

1. **width**:
   - 视频宽度。
   - 表示视频帧的像素宽度，例如 1920 表示视频的宽度为 1920 像素。
2. **height**:
   - 视频高度。
   - 表示视频帧的像素高度，例如 1080 表示视频的高度为 1080 像素。
3. **fpsNum**:
   - 每秒帧数的分子。
   - 表示视频的帧率，例如 30 表示视频每秒钟有 30 帧。
4. **fpsDenum**:
   - 每秒帧数的分母。
   - 通常设置为 1，如果需要表示非整数帧率，可以使用不同的分母值。
5. **avgBps**:
   - 平均比特率。
   - 表示视频数据的平均比特率，通常用于表示视频压缩后的数据速率。
6. **maxBps**:
   - 最大比特率。
   - 表示视频数据的最大比特率，用于描述视频数据的峰值速率。

**功能作用**：

- `MediaFormatVideo` 结构体用于描述视频媒体的格式，包含了视频特有的参数。
- 它继承自 `MediaFormat` 基类，添加了视频特有的成员变量，如视频宽度、高度、帧率、平均比特率和最大比特率。
- 通过 `fromPj` 函数，可以从 `pjmedia_format` 结构体构造 `MediaFormatVideo` 对象。
- 通过 `toPj` 函数，可以将 `MediaFormatVideo` 对象导出为 `pjmedia_format` 结构体。
- `init` 函数用于初始化 `MediaFormatVideo` 对象的成员变量，提供了一个简便的方法来设置视频格式的各个参数。

## 4.ConfPortInfo

```c
/**
 * 该结构体描述了注册到会议桥的特定媒体端口的信息。
 */
struct ConfPortInfo
{
    /**
     * 会议端口号。
     * 用于唯一标识会议桥中的媒体端口。
     */
    int portId;

    /**
     * 端口名称。
     * 用于描述和标识会议桥中的媒体端口。
     */
    string name;

    /**
     * 音频媒体格式信息。
     * 描述了媒体端口所使用的音频格式，包括采样率、通道数等。
     */
    MediaFormatAudio format;

    /**
     * 发送音量调整。值为1.0表示没有调整，值为0表示端口静音，
     * 值为2.0表示音量放大两倍。
     * 用于调整媒体端口发送音频的音量。
     */
    float txLevelAdj;

    /**
     * 接收音量调整。值为1.0表示没有调整，值为0表示端口静音，
     * 值为2.0表示音量放大两倍。
     * 用于调整媒体端口接收音频的音量。
     */
    float rxLevelAdj;

    /**
     * 监听者数组（换句话说，此端口正在向其传输的端口）。
     * 表示该端口的音频流传输到的其他端口。
     */
    IntVector listeners;

public:
    /**
     * 从 pjsua_conf_port_info 结构体构造。
     *
     * @param port_info pjsua_conf_port_info 结构体。
     */
    void fromPj(const pjsua_conf_port_info &port_info);
};

```

- `ConfPortInfo` 结构体用于描述在会议桥中注册的特定媒体端口的信息。
- 通过 `portId` 和 `name` 来唯一标识和描述端口。
- 使用 `format` 成员变量存储音频媒体的格式信息。
- 通过 `txLevelAdj` 和 `rxLevelAdj` 来调整端口的发送和接收音量。
- `listeners` 数组存储了该端口的音频流传输到的其他端口。
- `fromPj` 方法用于从 `pjsua_conf_port_info` 结构体构造 `ConfPortInfo` 对象，实现从底层数据结构到高层数据结构的转换。

## 5.MeidaPort

```c
/**
 * Media port, corresponds to pjmedia_port
 * 媒体端口，对应于 pjmedia_port
 *
 * MediaPort 是一个指向媒体端口的指针，在实际使用中它对应于 pjmedia_port 结构体。
 * pjmedia_port 是 PJMEDIA 库中的一个核心结构体，表示媒体流的端点，可以是音频或视频端口。
 * MediaPort 作为 void 指针，使其可以指向任何类型的媒体端口，从而实现灵活性和通用性。
 */
typedef void *MediaPort;
```

**详细注释**：

1. MediaPort
   - **类型**：`typedef void *`
   - **描述**：`MediaPort` 是一个指向媒体端口的指针类型，实际对应于 `pjmedia_port` 结构体。
   - **用途**：`MediaPort` 用于表示媒体流的端点，可以是音频端口或视频端口。由于定义为 `void` 指针，`MediaPort` 可以指向任何类型的媒体端口，增加了灵活性和通用性。

**功能作用**：

- `MediaPort` 是 PJMEDIA 库中用于表示媒体端口的抽象类型。
- 通过将 `MediaPort` 定义为 `void` 指针，可以使其兼容不同类型的媒体端口（例如音频和视频端口）。
- 在实际使用中，`MediaPort` 会被转换为具体的媒体端口类型（如 `pjmedia_port`），以便进行具体的媒体操作。

**应用场景**：

- 在媒体应用程序中，通过 `MediaPort` 来管理和操作媒体端口。
- 通过 `MediaPort` 可以方便地进行媒体流的创建、连接和控制操作，而不需要关心具体的媒体端口类型。
- 适用于需要灵活处理不同类型媒体端口的场景，例如在音视频会议系统中处理音频和视频流。

总结来说，`MediaPort` 作为一个指向媒体端口的通用指针类型，为媒体应用程序提供了灵活和通用的接口，用于管理和操作媒体流的端点。

## 6.Media类

```c
/**
 * Media.
 * 媒体类，表示媒体对象的基类。
 */
class Media
{
public:
    /**
     * Virtual destructor.
     * 虚析构函数。
     * 
     * 用于确保在删除派生类对象时，正确调用派生类的析构函数，防止内存泄漏。
     */
    virtual ~Media();

    /**
     * Get type of the media.
     * 获取媒体类型。
     *
     * @return          The media type.
     *                  返回媒体类型。
     */
    pjmedia_type getType() const;

protected:
    /**
     * Constructor.
     * 构造函数。
     * 
     * @param med_type  The type of the media.
     *                  媒体类型。
     * 
     * 用于初始化媒体类型。
     */
    Media(pjmedia_type med_type);

private:
    /**
     * Media type.
     * 媒体类型。
     * 
     * 表示媒体的类型，如音频或视频。
     */
    pjmedia_type        type;
};

```

## 7.AudioMediaTransmitParam音频传递参数

```c
/**
 * Parameters for AudioMedia::startTransmit2() method.
 * AudioMedia::startTransmit2() 方法的参数。
 */
struct AudioMediaTransmitParam
{
    /**
     * Signal level adjustment. Value 1.0 means no level adjustment,
     * while value 0 means to mute the port.
     * 
     * 信号电平调整。值为 1.0 表示不进行电平调整，而值为 0 表示静音端口。
     *
     * Default: 1.0
     * 默认值：1.0
     */
    float               level;

public:
    /**
     * Default constructor
     * 默认构造函数
     *
     * 初始化成员变量 level 为默认值 1.0。
     */
    AudioMediaTransmitParam()
        : level(1.0f) // 初始化列表，将 level 设置为默认值 1.0
    {}
};

```

**功能作用**：

- `AudioMediaTransmitParam` 结构体用于传递音频传输参数，特别是信号电平调整参数。
- `level` 参数允许用户调整音频传输的信号电平，提供灵活的音频控制。

**应用场景**：

- 在使用 `AudioMedia::startTransmit2()` 方法启动音频传输时，可以使用 `AudioMediaTransmitParam` 结构体设置传输参数，特别是调整信号电平。
- 适用于需要动态调整音频传输电平的应用场景，如音频会议系统、音频处理应用等。

## 8.AudioMedia音频类

```c
/**
 * 音频媒体。这是音频会议桥接口的轻量级包装类，
 * 即该类仅维护一个数据成员，即会议槽ID，并且方法仅是会议桥接口操作的代理。
 *
 * 应用程序可以创建派生类，并使用registerMediaPort2()/
 * unregisterMediaPort()方法将媒体端口注册到/从会议桥接口中注册/注销。
 *
 * 库不会维护AudioMedia实例列表，因此任何由应用程序实例化的AudioMedia（后代）实例
 * 必须由应用程序自身维护和销毁。
 *
 * 注意，任何返回AudioMedia实例的PJSUA2 API（如Endpoint::mediaEnumPorts2()或
 * Call::getAudioMedia()）只会返回生成的副本。所有AudioMedia方法在此生成的副本实例上
 * 应该正常工作。
 */
class AudioMedia : public Media
{
public:
    /**
     * 获取指定会议端口的信息。
     */
    ConfPortInfo getPortInfo() const PJSUA2_THROW(Error);

    /**
     * 获取端口ID。
     */
    int getPortId() const;

    /**
     * 根据特定端口ID获取信息。
     */
    static ConfPortInfo getPortInfoFromId(int port_id) PJSUA2_THROW(Error);

    /**
     * 向接收端建立单向媒体流。此媒体端口将作为源，
     * 可以传输到多个目的地/接收端。如果多个源传输到同一接收端，
     * 媒体将混合在一起。源和接收端可以指向同一媒体，实现媒体的环路。
     *
     * 如果需要双向媒体流，应用程序需要调用此方法两次，
     * 第二次调用从相反的源媒体进行。
     *
     * @param sink              目标媒体。
     */
    void startTransmit(const AudioMedia &sink) const PJSUA2_THROW(Error);

    /**
     * 向接收端建立单向媒体流。此媒体端口将作为源，
     * 可以传输到多个目的地/接收端。如果多个源传输到同一接收端，
     * 媒体将混合在一起。源和接收端可以指向同一媒体，实现媒体的环路。
     *
     * 可通过参数param调整此源到接收端的信号级别，使其变大或变小。
     * 级别调整仅适用于特定连接（即仅适用于从此源到接收端的信号），
     * 与adjustTxLevel()/adjustRxLevel()将适用于此媒体端口的所有信号不同。
     * 信号调整将是累积的，顺序如下：
     * 来自此源的信号将使用adjustTxLevel()中指定的级别进行调整，
     * 然后使用此API指定的级别进行调整，最后使用接收端的adjustRxLevel()指定的级别进行调整。
     *
     * 如果需要双向媒体流，应用程序需要调用此方法两次，
     * 第二次调用从相反的源媒体进行。
     *
     * @param sink              目标媒体。
     * @param param             参数。
     */
    void startTransmit2(const AudioMedia &sink, 
                        const AudioMediaTransmitParam &param) const
         PJSUA2_THROW(Error);

    /**
     * 停止向目标/接收端口传输媒体流。
     *
     * @param sink              目标媒体。
     *
     */
    void stopTransmit(const AudioMedia &sink) const PJSUA2_THROW(Error);

    /**
     * 调整从桥接到此媒体端口传输的信号级别，使其变大或变小。
     *
     * @param level             信号级别调整。值为1.0表示无级别调整，
     *                          值为0表示将端口静音。
     */
    void adjustRxLevel(float level) PJSUA2_THROW(Error);

    /**
     * 调整从此媒体端口接收的信号级别（发送到桥接），
     * 使其变大或变小。
     *
     * @param level             信号级别调整。值为1.0表示无级别调整，
     *                          值为0表示将端口静音。
     */
    void adjustTxLevel(float level) PJSUA2_THROW(Error);

    /**
     * 获取最后接收到的信号级别。
     *
     * @return                  信号级别百分比。
     */
    unsigned getRxLevel() const PJSUA2_THROW(Error);

    /**
     * 获取最后发送的信号级别。
     *
     * @return                  信号级别百分比。
     */
    unsigned getTxLevel() const PJSUA2_THROW(Error);

    /**
     * 警告：已弃用，将在将来的版本中删除。
     *
     * 从基类Media进行类型转换。这对于使用不支持向下转换的语言（如Python）的应用程序非常有用。
     *
     * @param media             要向下转换的对象
     *
     * @return                  作为AudioMedia实例的对象
     */
    static AudioMedia* typecastFromMedia(Media *media);

    /**
     * 默认构造函数。
     *
     * 通常应用程序不会直接创建AudioMedia对象，
     * 而是实例化一个派生类的AudioMedia。这是公共的，
     * 因为某些STL向量实现需要它。
     */
    AudioMedia();

    /**
     * 虚析构函数。
     */
    virtual ~AudioMedia();

protected:
    /**
     * 会议端口ID。
     */
    int                  id;

protected:
    /**
     * 警告：已弃用，将在将来的版本中删除，使用registerMediaPort2()代替。
     *
     * 此方法需要此类的后代调用，将创建的媒体端口注册到会议桥接口和Endpoint的媒体列表中。
     *
     * param port  要注册到会议桥接口的媒体端口。
     *
     */
    void registerMediaPort(MediaPort port) PJSUA2_THROW(Error);

    /**
     * 此方法需要此类的后代调用，将创建的媒体端口注册到会议桥接口和Endpoint的媒体列表中。
     *
     * param port  要注册到会议桥接口的媒体端口。
     * param pool  内存池。
     *
     */
    void registerMediaPort2(MediaPort port, pj_pool_t *pool)
                            PJSUA2_THROW(Error);

    /**
     * 此方法需要此类的后代调用，从会议桥接口和Endpoint的媒体列表中移除媒体端口。
     * 后代应仅在使用registerMediaPort()注册了媒体的情况下调用此方法。
     */
    void unregisterMediaPort() PJSUA2_THROW(Error);

private:
    /* 弃用registerMediaPort()的内存池 */
    pj_caching_pool      mediaCachingPool;
    pj_pool_t           *mediaPool;
};

```

## 9.MediaFrame

```c
/**
 * 这个结构描述了一个媒体帧。
 * 
 * 功能：
 * - type：指定帧的类型。
 * - buf：存储帧内容的缓冲区。
 * - size：记录帧的字节大小。
 * 
 * 作用：
 * 这个结构用于表示和存储从媒体流中提取的单个帧的信息和数据。在音视频处理中，帧是基本的数据单元，
 * 包含特定类型的媒体数据，例如音频样本或视频像素数据。通过这个结构，可以有效地管理和处理媒体
 * 数据的帧信息。
 */
struct MediaFrame
{
    pjmedia_frame_type   type;      /**< 帧类型。                           */
    ByteVector           buf;       /**< 帧缓冲区内容。                     */
    unsigned             size;      /**< 帧大小（字节）。                   */

public:
    /**
     * 默认构造函数
     */
    MediaFrame()
    : type(PJMEDIA_FRAME_TYPE_NONE),
      size(0)
    {}
};

```

## 10.AudioMediaPort

```c
/**
 * 音频媒体端口。
 */
class AudioMediaPort : public AudioMedia
{
public:
    /**
     * 构造函数。
     */
    AudioMediaPort();

    /**
     * 析构函数。这将从会议桥中注销音频媒体端口。
     */
    virtual ~AudioMediaPort();

    /**
     * 创建一个音频媒体端口并将其注册到会议桥中。
     *
     * @param name      端口名称。
     * @param fmt       音频格式。
     */
    void createPort(const string &name, MediaFormatAudio &fmt)
                    PJSUA2_THROW(Error);

    /*
     * 回调函数
     */
    /**
     * 当请求从该端口获取帧时调用此回调。在输入时，frame.size 表示帧缓冲区的容量，
     * frame.buf 将最初是一个空向量。应用程序可以设置帧类型并填充向量。
     *
     * @param frame       帧数据。
     */
    virtual void onFrameRequested(MediaFrame &frame)
    { PJ_UNUSED_ARG(frame); }

    /**
     * 当该端口接收到帧时调用此回调。帧内容将以 frame.buf 向量的形式提供，
     * 帧大小可以在 frame.size 或向量的大小中找到（两者的值相同）。
     *
     * @param frame       帧数据。
     */
    virtual void onFrameReceived(MediaFrame &frame)
    { PJ_UNUSED_ARG(frame); }

private:
    pj_pool_t *pool;
    pjmedia_port port;
};

```

## 11.AudioMediaPlayerInfo音频播放器信息

```c
/**
 * 这个结构包含了关于音频媒体播放器的额外信息。
 */
struct AudioMediaPlayerInfo
{
    /**
     * 负载的格式ID。
     */
    pjmedia_format_id   formatId;

    /**
     * 文件负载每样本的位数。例如，PCM WAV 文件为 16 位，Alaw/Ulas WAV 文件为 8 位。
     */
    unsigned            payloadBitsPerSample;

    /**
     * WAV 负载的大小（字节）。
     */
    pj_uint32_t         sizeBytes;

    /**
     * WAV 负载的大小（样本数）。
     */
    pj_uint32_t         sizeSamples;

public:
    /**
     * 默认构造函数。
     */
    AudioMediaPlayerInfo() 
    : formatId(PJMEDIA_FORMAT_L16),
      payloadBitsPerSample(0),
      sizeBytes(0),
      sizeSamples(0)
    {}
};

```

## 12.AudioMediaPlayer音频播放器

```c
/**
 * 音频媒体播放器。
 */
class AudioMediaPlayer : public AudioMedia
{
public:
    /** 
     * 构造函数。
     */
    AudioMediaPlayer();

    /**
     * 创建一个文件播放器，并自动将此播放器添加到会议桥中。
     *
     * @param file_name  要播放的文件名。当前仅支持 WAV 格式文件，
     *                   WAV 文件必须以 16 位 PCM 单声道格式（任何时钟率均支持）。
     * @param options    可选的选项标志。应用程序可以指定 PJMEDIA_FILE_NO_LOOP 防止循环播放。
     */
    void createPlayer(const string &file_name,
                      unsigned options=0) PJSUA2_THROW(Error);

    /**
     * 创建一个文件播放列表媒体端口，并自动将端口添加到会议桥中。
     *
     * @param file_names  要添加到播放列表的文件名数组。
     *                    注意，这些文件必须具有相同的时钟率、通道数和每样本位数。
     * @param label       可选的标签，用于设置媒体端口的标签。
     * @param options     可选的选项标志。应用程序可以指定 PJMEDIA_FILE_NO_LOOP 防止循环播放。
     */
    void createPlaylist(const StringVector &file_names,
                        const string &label="",
                        unsigned options=0) PJSUA2_THROW(Error);

    /**
     * 获取关于播放器的额外信息。对于播放列表，将抛出错误。
     *
     * @return          音频媒体播放器的信息。
     */
    AudioMediaPlayerInfo getInfo() const PJSUA2_THROW(Error);

    /**
     * 获取当前播放的样本位置。对于播放列表，此操作无效。
     *
     * @return             当前播放位置的样本数。
     */
    pj_uint32_t getPos() const PJSUA2_THROW(Error);

    /**
     * 设置播放的样本位置。对于播放列表，此操作无效。
     *
     * @param samples      所需的播放位置，以样本数表示。
     */
    void setPos(pj_uint32_t samples) PJSUA2_THROW(Error);

    /**
     * 警告：已弃用，并将在将来的版本中移除。
     *
     * 从基类 AudioMedia 进行类型转换。对于不支持向下转换的语言（如 Python）很有用。
     *
     * @param media             要转换的对象
     *
     * @return                  转换为 AudioMediaPlayer 实例的对象
     */
    static AudioMediaPlayer* typecastFromAudioMedia(AudioMedia *media);

    /**
     * 析构函数。这将从会议桥中注销播放器端口。
     */
    virtual ~AudioMediaPlayer();

public:
    /*
     * 回调函数
     */

    /**
     * 注册回调，当文件播放器达到文件末尾或播放列表的最后一个文件时调用。
     * 如果文件或播放列表设置为重复播放，则回调将多次调用。
     *
     * 如果应用程序希望停止播放，可以在回调中停止媒体传输，
     * 并且只有在所有传输都停止后，应用程序才能安全地销毁播放器。
     */
    virtual void onEof2()
    { }

private:
    /**
     * 播放器ID。
     */
    int playerId;

    /**
     * 低级别 PJMEDIA 回调。
     */
    static void eof_cb(pjmedia_port *port,
                       void *usr_data);
};

```

## 13.音频媒体录制器

```c
/**
 * 音频媒体录制器。
 */
class AudioMediaRecorder : public AudioMedia
{
public:
    /**
     * 构造函数。
     */
    AudioMediaRecorder();

    /**
     * 创建一个文件录制器，并自动将此录制器连接到会议桥中。目前支持录制 WAV 文件。
     * 录制器的类型由文件扩展名确定（例如 ".wav"）。
     *
     * @param file_name  输出文件名。函数将根据文件扩展名确定要使用的默认格式。
     *                   目前在所有平台上支持 ".wav"。
     * @param enc_type   可选地指定用于压缩媒体的编码器类型，如果文件支持不同的编码。目前此值必须为零。
     * @param max_size   最大文件大小。指定零或-1以移除大小限制。目前此值必须为零或-1。
     * @param options    可选选项，用于指定录制文件格式。支持的选项有 PJMEDIA_FILE_WRITE_PCM、
     *                   PJMEDIA_FILE_WRITE_ALAW 和 PJMEDIA_FILE_WRITE_ULAW。默认为零或 PJMEDIA_FILE_WRITE_PCM。
     */
    void createRecorder(const string &file_name,
                        unsigned enc_type=0,
                        long max_size=0,
                        unsigned options=0) PJSUA2_THROW(Error);

    /**
     * 警告：已弃用，并将在将来的版本中移除。
     *
     * 从基类 AudioMedia 进行类型转换。对于不支持向下转换的语言（如 Python）很有用。
     *
     * @param media             要转换的对象
     *
     * @return                  转换为 AudioMediaRecorder 实例的对象
     */
    static AudioMediaRecorder* typecastFromAudioMedia(AudioMedia *media);

    /**
     * 析构函数。这将从会议桥中注销录制器端口。
     */
    virtual ~AudioMediaRecorder();

private:
    /**
     * 录制器ID。
     */
    int recorderId;
};

```

## 14.ToneDesc音调描述

```c
/**
 * 音调描述符（pjmedia_tone_desc 的抽象）
 */
class ToneDesc : public pjmedia_tone_desc
{
public:
    /**
     * 默认构造函数。初始化对象内存为零。
     */
    ToneDesc()
    {
        pj_bzero(this, sizeof(*this));
    }

    /**
     * 析构函数。
     */
    ~ToneDesc() {}
};
/**
 * Array of tone descriptor.
 */
typedef std::vector<ToneDesc> ToneDescVector;
```

## 15.ToneDigit

```c
/**
 * 音调数字（pjmedia_tone_digit 的抽象）
 */
class ToneDigit : public pjmedia_tone_digit
{
public:
    /**
     * 默认构造函数。初始化对象内存为零。
     */
    ToneDigit()
    {
        pj_bzero(this, sizeof(*this));
    }

    /**
     * 析构函数。
     */
    ~ToneDigit() {}
};
/**
 * Array of tone digits.
 */
typedef std::vector<ToneDigit> ToneDigitVector;

```

## 16.ToneDigitMapDigit

```c
/**
 * 音调数字映射中的一个数字
 *
 * 用于描述音调映射表中的一个数字，包括其具体的数字值、对应的两个频率值。
 */
struct ToneDigitMapDigit
{
public:
    string      digit;  /**< 数字 */
    int         freq1;  /**< 第一个频率 */
    int         freq2;  /**< 第二个频率 */
};
/**
 * Tone digit map
 */
typedef std::vector<ToneDigitMapDigit> ToneDigitMapVector;
```

## 17.ToneGenerator音调生成器

```c
/**
 * 音调生成器。
 *
 * 用于生成音调并将其注册到会议桥接器中。
 */
class ToneGenerator : public AudioMedia
{
public:
    /**
     * 构造函数。
     */
    ToneGenerator();

    /**
     * 析构函数。将从会议桥接器中注销音调生成器端口。
     */
    ~ToneGenerator();

    /**
     * 创建音调生成器并将端口注册到会议桥接器中。
     *
     * @param clock_rate    时钟速率，默认为16000。
     * @param channel_count 通道数量，默认为1。
     */
    void createToneGenerator(unsigned clock_rate = 16000,
                             unsigned channel_count = 1) PJSUA2_THROW(Error);

    /**
     * 检查音调生成器是否仍在忙于生成音调。
     *
     * @return              如果忙于生成音调则返回非零值。
     */
    bool isBusy() const;

    /**
     * 停止当前处理。
     */
    void stop() PJSUA2_THROW(Error);

    /**
     * 倒带播放。将从播放列表中的第一个音调开始播放。
     */
    void rewind() PJSUA2_THROW(Error);

    /**
     * 指示音调生成器播放具有指定持续时间的单音或双音频音调。
     * 新的音调将附加到当前正在播放的音调中，除非在调用此函数之前调用了stop()。
     * 一旦音调生成器连接到其他媒体，播放将开始。
     *
     * @param tones         要播放的音调数组。
     * @param loop          是否循环播放音调。
     */
    void play(const ToneDescVector &tones,
              bool loop=false) PJSUA2_THROW(Error);

    /**
     * 指示音调生成器播放多个MF数字，每个数字具有单独的开/关持续时间。
     * 数组中的每个数字必须在数字映射中具有相应的描述符。
     * 新的音调将附加到当前正在播放的音调中，除非在调用此函数之前调用了stop()。
     * 一旦音调生成器连接到接收媒体，播放将开始。
     *
     * @param digits        MF数字数组。
     * @param loop          是否循环播放音调。
     */
    void playDigits(const ToneDigitVector &digits,
                    bool loop=false) PJSUA2_THROW(Error);

    /**
     * 获取当前音调生成器使用的数字映射。
     *
     * @return              当前音调生成器使用的数字映射。
     */
    ToneDigitMapVector getDigitMap() const PJSUA2_THROW(Error);

    /**
     * 设置音调生成器要使用的数字映射。
     *
     * @param digit_map     音调生成器要使用的数字映射。
     */
    void setDigitMap(const ToneDigitMapVector &digit_map) PJSUA2_THROW(Error);

private:
    pj_pool_t *pool;            /**< 内存池。 */
    pjmedia_port *tonegen;      /**< 音调生成器端口。 */
    pjmedia_tone_digit_map digitMap; /**< 数字映射。 */
};

```

## 18.AudioDevInfo音频设备信息

```c
/**
 * 音频设备信息结构体。
 */
struct AudioDevInfo
{
    /**
     * 设备ID。
     */
    pjmedia_aud_dev_index id;

    /**
     * 设备名称。/** 
 * Warning: deprecated, use AudioDevInfoVector2 instead.
 *
 * Array of audio device info.
 */
typedef std::vector<AudioDevInfo*> AudioDevInfoVector;

/** Array of audio device info */
typedef std::vector<AudioDevInfo> AudioDevInfoVector2;
     */
    string name;

    /**
     * 此设备支持的最大输入通道数。如果值为零，则设备不支持输入操作（即仅支持播放设备）。
     */
    unsigned inputCount;

    /**
     * 此设备支持的最大输出通道数。如果值为零，则设备不支持输出操作（即仅支持输入设备）。
     */
    unsigned outputCount;

    /**
     * 默认采样率。
     */
    unsigned defaultSamplesPerSec;

    /**
     * 底层驱动程序名称。
     */
    string driver;

    /**
     * 设备能力，作为 pjmedia_aud_dev_cap 的位掩码组合。
     */
    unsigned caps;

    /**
     * 支持的音频设备路由，作为 pjmedia_aud_dev_route 的位掩码组合。
     * 如果设备不支持音频路由，则值可能为零。
     */
    unsigned routes;

    /**
     * 支持的扩展音频格式数组。
     */
    MediaFormatAudioVector extFmt;

    /**
     * 从 pjmedia_aud_dev_info 构造。
     *
     * @param dev_info  pjmedia_aud_dev_info 结构体。
     */
    void fromPj(const pjmedia_aud_dev_info &dev_info);

    /**
     * 析构函数。
     */
    ~AudioDevInfo();
};
/** 
 * Warning: deprecated, use AudioDevInfoVector2 instead.
 *
 * Array of audio device info.
 */
typedef std::vector<AudioDevInfo*> AudioDevInfoVector;

/** Array of audio device info */
typedef std::vector<AudioDevInfo> AudioDevInfoVector2;
```

## 19.AudDevManager音频设备管理器

```c
/**
 * 音频设备管理器。
 */
class AudDevManager
{
public:
    /**
     * 获取当前活动的捕获声音设备的设备ID。如果声音设备尚未创建，
     * 可能返回-1作为设备ID。
     *
     * @return                  捕获设备的设备ID。
     */
    int getCaptureDev() const PJSUA2_THROW(Error);

    /**
     * 获取捕获音频设备的音频媒体。
     *
     * @return                  捕获设备的音频媒体。
     */
    AudioMedia &getCaptureDevMedia() PJSUA2_THROW(Error);

    /**
     * 获取当前活动的播放声音设备的设备ID。如果声音设备尚未创建，
     * 可能返回-1作为设备ID。
     *
     * @return                  播放设备的设备ID。
     */
    int getPlaybackDev() const PJSUA2_THROW(Error);

    /**
     * 获取播放音频设备的音频媒体。
     *
     * @return                  播放设备的音频媒体。
     */
    AudioMedia &getPlaybackDevMedia() PJSUA2_THROW(Error);

    /**
     * 选择或更改捕获声音设备。应用程序可以随时调用此函数来替换当前的声音设备。
     * 调用此方法不会更改声音设备的状态（已打开/已关闭）。
     *
     * @param capture_dev       捕获设备的设备ID。
     */
    void setCaptureDev(int capture_dev) const PJSUA2_THROW(Error);

    /**
     * 选择或更改播放声音设备。应用程序可以随时调用此函数来替换当前的声音设备。
     * 调用此方法不会更改声音设备的状态（已打开/已关闭）。
     *
     * @param playback_dev      播放设备的设备ID。
     */
    void setPlaybackDev(int playback_dev) const PJSUA2_THROW(Error);

#if !DEPRECATED_FOR_TICKET_2232
    /**
     * 警告：已弃用，请使用enumDev2代替。此函数在多线程环境中不安全。
     *
     * 枚举系统中安装的所有音频设备。此函数在多线程环境中不安全。
     *
     * @return                  音频设备信息的列表。
     */
    const AudioDevInfoVector &enumDev() PJSUA2_THROW(Error);
#endif

    /**
     * 枚举系统中安装的所有音频设备。
     *
     * @return                  音频设备信息的列表。
     */
    AudioDevInfoVector2 enumDev2() const PJSUA2_THROW(Error);

    /**
     * 设置pjsua使用空音频设备。空音频设备仅提供会议桥所需的定时，不会与任何硬件交互。
     *
     */
    void setNullDev() PJSUA2_THROW(Error);

    /**
     * 断开主会议桥与任何声音设备的连接，并让应用程序将会议桥连接到其自己的声音设备/主端口。
     *
     * @return                  会议桥的端口接口，以便应用程序可以将其连接到自己的声音设备或主端口。
     */
    MediaPort *setNoDev();

    /**
     * 设置声音设备模式。
     *
     * 注意，此方法将打开声音设备，使用通过setCaptureDev()或setPlaybackDev()设置的当前活动ID，
     * 如果未指定PJSUA_SND_DEV_NO_IMMEDIATE_OPEN标志。
     *
     * @param mode              声音设备模式，作为#pjsua_snd_dev_mode的位掩码组合。
     *
     */
    void setSndDevMode(unsigned mode) const PJSUA2_THROW(Error);

    /**
     * 更改回声消除设置。
     *
     * 此函数的行为取决于声音设备当前是否处于活动状态，如果是，则取决于设备或软件AEC是否正在使用。
     *
     * 如果声音设备当前处于活动状态，并且设备支持AEC，则此函数将请求转发到设备，并且设备将决定是否支持请求。
     * 如果正在使用软件AEC（如果设备不支持AEC，则将使用软件EC），则此函数将更改软件EC设置。在所有情况下，
     * 将为将来打开声音设备保存设置。
     *
     * 如果声音设备当前处于非活动状态，则仅更改默认AEC设置，并且将在下次打开声音设备时应用设置。
     *
     * @param tail_msec         尾长度，以毫秒为单位。设置为零以禁用AEC。
     * @param options           传递给pjmedia_echo_create()的选项。通常值应为零。
     *
     */
    void setEcOptions(unsigned tail_msec, unsigned options) PJSUA2_THROW(Error);

    /**
     * 获取当前回声消除器尾长度。
     *
     * @return                  EC尾长度（毫秒）。
     *                          如果已禁用AEC，则值为零。
     */
    unsigned getEcTail() const PJSUA2_THROW(Error);

    /**
     * 检查声音设备当前是否处于活动状态。如果应用程序将自动关闭功能设置为非零（MediaConfig中的sndAutoCloseTime设置），
     * 或者通过setNoDev()函数配置了空音频设备或无音频设备，则声音设备可能处于非活动状态。
     */
    bool sndIsActive() const;

    /**
     * 刷新系统中安装的声音设备列表。此方法仅刷新音频设备列表，因此所有活动音频流不会受到影响。
     * 在刷新设备列表后，应用程序必须确保在调用任何接受音频设备索引作为参数的方法之前更新所有索引引用。
     *
     */
    void refreshDevs() PJSUA2_THROW(Error);

    /**
     * 获取系统中安装的声音设备数量。
     *
     * @return                  系统中安装的声音设备数量。
     *
     */
    unsigned getDevCount() const;

    /**
     * 获取设备信息。
     *
     * @param id                音频设备ID。
     *
     * @return                  设备信息，该方法在成功返回时将填充此信息。
     */
    AudioDevInfo getDevInfo(int id) const PJSUA2_THROW(Error);

    /**
     * 根据驱动程序和设备名称查找设备索引。
     *
     * @param drv_name          驱动程序名称。
     * @param dev_name          设备名称。
     *
     * @return                  设备ID。如果未找到设备，将抛出错误。
     */
    int lookupDev(const string &drv_name,
                  const string &dev_name) const PJSUA2_THROW(Error);

    /**
     * 获取指定能力的字符串信息。
     *
     * @param cap               能力ID。
     *
     * @return                  能力名称。
     */
    string capName(pjmedia_aud_dev_cap cap) const;

    /**
     * 将音频格式能力（除PCM外）配置到正在使用的声音设备中。如果声音设备当前处于活动状态，
     * 方法将立即将设置转发到支持该设置的声音设备实例。
     *
     * 仅当设备在AudioDevInfo.caps标志中具有PJMEDIA_AUD_DEV_CAP_EXT_FORMAT能力时，此方法才有效，
     * 否则将抛出错误。
     *
     * 注意，在将设置保留以供将来使用时，将应用设置到任何设备，即使应用程序已更改要使用的声音设备。
     *
     * @param format            音频格式。
     * @param keep              指定是否将设置保留以供将来使用。
     *
     */
    void setExtFormat(const MediaFormatAudio &format, bool keep=true)
                      PJSUA2_THROW(Error);

    /**
     * 获取正在使用的声音设备的音频格式能力（除PCM外）。如果声音设备当前处于活动状态，
     * 方法将立即将设置转发到支持该设置的声音设备实例。
     *
     * 仅当设备在AudioDevInfo.caps标志中具有PJMEDIA_AUD_DEV_CAP_EXT_FORMAT能力时，此方法才有效，
     * 否则将抛出错误。
     *
     * 注意，在将设置保留以供将来使用时，将应用设置到任何设备，即使应用程序已更改要使用的声音设备。
     *
     * @return                  音频格式。
     */
    MediaFormatAudio getExtFormat() const PJSUA2_THROW(Error);

    /**
     * 根据需要，从当前设备音频流中获取控制信号。
     *
     * @return                  当前控制信号。
     */
    float getSignalLevel() const PJSUA2_THROW(Error);

    /**
     * 根据需求，从当前捕获设备音频流中获取控制信号。
     *
     * @return                  捕获设备的当前控制信号。
     */
    float getCaptureSignalLevel() const PJSUA2_THROW(Error);

    /**
     * 设置音频设备流延迟控制。
     *
     * @param playback_lat       播放流延迟控制。请参见pjmedia_snd_set_latency（）。
     * @param capture_lat        捕获流延迟控制。请参见pjmedia_snd_set_latency（）。
     */
    void setLatencyControl(int playback_lat, int capture_lat) PJSUA2_THROW(Error);

    /**
     * 获取音频设备流延迟控制。
     *
     * @param playback_lat       播放流延迟控制。请参见pjmedia_snd_set_latency（）。
     * @param capture_lat        捕获流延迟控制。请参见pjmedia_snd_set_latency（）。
     */
    void getLatencyControl(int &playback_lat, int &capture_lat) PJSUA2_THROW(Error);

    /**
     * 获取音频设备的当前音量级别。
     *
     * @return                  当前音量级别。
     */
    float getPlaybackVol() const PJSUA2_THROW(Error);

    /**
     * 设置音频设备的音量级别。
     *
     * @param vol               音量级别（0.0至1.0之间）。
     */
    void setPlaybackVol(float vol) PJSUA2_THROW(Error);

    /**
     * 获取音频设备的当前捕获增益级别。
     *
     * @return                  当前捕获增益级别。
     */
    float getCaptureGain() const PJSUA2_THROW(Error);

    /**
     * 设置音频设备的捕获增益级别。
     *
     * @param gain              增益级别（0.0至1.0之间）。
     */
    void setCaptureGain(float gain) PJSUA2_THROW(Error);

    /**
     * 设置音频设备回音消除的软限。
     *
     * @param echo_lim          回音消除软限。
     */
    void setEchoLimiter(float echo_lim) PJSUA2_THROW(Error);

    /**
     * 获取音频设备回音消除的软限。
     *
     * @return                  回音消除软限。
     */
    float getEchoLimiter() const PJSUA2_THROW(Error);

};

```

## 20ExtraAudioDevice.额外音频设备

```c
/**
 * 额外音频设备。此类允许应用程序同时拥有多个音频设备实例。
 *
 * 应用程序还可以使用此类来改善媒体时钟。通常情况下，媒体时钟由主端口的音频设备驱动，
 * 但不幸的是，某些音频设备可能会产生抖动的时钟。为了改善媒体时钟，应用程序可以安装
 * 空音频设备（例如使用AudDevManager::setNullDev()），它将充当主端口，并安装额外的
 * 音频设备。
 *
 * 注意，额外音频设备不会具有空闲时自动关闭的功能。此外，额外音频设备仅支持单声道。
 */
class ExtraAudioDevice : public AudioMedia
{
public:
    /**
     * 构造函数。
     *
     * @param playdev           播放设备ID。
     * @param recdev            录音设备ID。
     */
    ExtraAudioDevice(int playdev, int recdev);

    /**
     * 析构函数。
     */
    virtual ~ExtraAudioDevice();

    /**
     * 使用与会议桥格式匹配的格式（例如时钟率、每样本位数、每帧样本数），打开音频设备，
     * 但通道数将设置为单声道。这还将音频设备端口注册到会议桥。
     */
    void open();

    /**
     * 关闭音频设备，并从会议桥中注销音频设备端口。
     */
    void close() PJSUA2_THROW(Error);

    /**
     * 额外音频设备是否已打开？
     *
     * @return                  如果已打开，则为 'true'。
     */
    bool isOpened();

protected:
    int playDev;        /**< 播放设备ID */
    int recDev;         /**< 录音设备ID */
    void *ext_snd_dev;  /**< 额外音频设备指针 */
};

```

## 21.视频基础参数

```c
/*************************************************************************
 * 视频媒体
 */

/**
 * 媒体坐标的表示。
 */
struct MediaCoordinate
{
    int         x;          /**< 坐标的X位置 */
    int         y;          /**< 坐标的Y位置 */
};

/**
 * 媒体大小的表示。
 */
struct MediaSize
{
    unsigned    w;          /**< 宽度 */
    unsigned    h;          /**< 高度 */
};


/**
 * 描述已注册到会议桥的特定媒体端口的信息。
 */
struct VidConfPortInfo
{
    /**
     * 会议端口号。
     */
    int                 portId;

    /**
     * 端口名称。
     */
    string              name;

    /**
     * 媒体音频格式信息。
     */
    MediaFormatVideo    format;

    /**
     * 听众数组（即该端口正在传输到的端口）。
     */
    IntVector           listeners;

    /**
     * 发射器数组（即该端口正在监听的端口）。
     */
    IntVector           transmitters;

public:
    /**
     * 从pjsua_conf_port_info构造。
     */
    void fromPj(const pjsua_vid_conf_port_info &port_info);
};

/**
 * VideoMedia::startTransmit() 方法的参数。
 */
struct VideoMediaTransmitParam
{
};

```

## 22.VideoMedia视频媒体

```c
/**
 * 视频媒体。
 */
class VideoMedia : public Media
{
public:
    /**
     * 获取指定会议端口的信息。
     */
    VidConfPortInfo getPortInfo() const PJSUA2_THROW(Error);

    /**
     * 获取端口ID。
     */
    int getPortId() const;

    /**
     * 根据特定端口ID获取信息。
     */
    static VidConfPortInfo getPortInfoFromId(int port_id) PJSUA2_THROW(Error);

    /**
     * 建立向接收端的单向媒体流。该媒体端口将作为源，可以传输到多个目的地/接收端。
     * 如果多个源传输到同一接收端，则媒体将被混合在一起。源和接收端可以指同一媒体，
     * 有效地实现媒体的循环。
     *
     * 如果需要双向媒体流，应用程序需要调用此方法两次，第二次调用来自相反的源媒体。
     *
     * @param sink              目的地媒体。
     * @param param             参数。
     */
    void startTransmit(const VideoMedia &sink, 
                       const VideoMediaTransmitParam &param) const
         PJSUA2_THROW(Error);

    /**
     * 停止向目的地/接收端端口的媒体流。
     *
     * @param sink              目的地媒体。
     *
     */
    void stopTransmit(const VideoMedia &sink) const PJSUA2_THROW(Error);

    /**
     * 从视频端口信息中更新或刷新端口状态。某些端口可能会在会话中更改其端口信息，
     * 例如当视频流解码器了解到传入的视频大小或帧率发生更改时，视频会议需要被通知以更新其内部状态。
     *
     */
    void update() const PJSUA2_THROW(Error);

    /**
     * 默认构造函数。
     *
     * 通常应用程序不会直接创建 VideoMedia 对象，而是实例化一个 VideoMedia 派生类。
     * 这是公开的，因为一些STL向量实现需要它。
     */
    VideoMedia();

    /**
     * 虚析构函数。
     */
    virtual ~VideoMedia();

protected:
    /**
     * 会议端口ID。
     */
    int                  id;

protected:
    /**
     * 后代类需要调用此方法将创建的媒体端口注册到会议桥和端点的媒体列表中。
     *
     * @param port  要注册到会议桥的媒体端口。
     * @param pool  内存池。
     */
    void registerMediaPort(MediaPort port, pj_pool_t *pool) PJSUA2_THROW(Error);

    /**
     * 后代类需要调用此方法从会议桥和端点的媒体列表中移除媒体端口。
     * 后代应仅在通过调用 registerMediaPort() 注册了媒体后调用此方法。
     */
    void unregisterMediaPort();
};

```

## 23.视频播放

```c
typedef struct WindowHandle {
    void        *window;    /**< 窗口 */
    void        *display;   /**< 显示器 */
} WindowHandle;

/**
 * 视频窗口句柄。
 */
struct VideoWindowHandle
{
    /**
     * 窗口句柄类型。
     */
    pjmedia_vid_dev_hwnd_type   type;

    /**
     * 窗口句柄。
     */
    WindowHandle                handle;
};

/**
 * 此结构描述视频窗口信息。
 */
typedef struct VideoWindowInfo
{
    /**
     * 表示此窗口是否是原生窗口，例如由内置预览设备创建。如果该字段为true，
     * 则仅此结构的video window handle字段有效。
     */
    bool                isNative;

    /**
     * 视频窗口句柄。
     */
    VideoWindowHandle   winHandle;

    /**
     * 渲染设备ID。
     */
    int                 renderDeviceId;

    /**
     * 窗口显示状态。如果为false，则窗口被隐藏。
     */
    bool                show;

    /**
     * 窗口位置。
     */
    MediaCoordinate     pos;

    /**
     * 窗口大小。
     */
    MediaSize           size;

} VideoWindowInfo;

/**
 * 视频窗口。
 */
class VideoWindow
{
public:
    /**
     * 构造函数。
     *
     * @param win_id        窗口ID。
     */
    VideoWindow(int win_id);

    /**
     * 获取窗口信息。
     *
     * @return              视频窗口信息。
     */
    VideoWindowInfo getInfo() const PJSUA2_THROW(Error);

    /**
     * 获取此视频窗口的视频媒体或会议桥端口。
     *
     * @return              此渲染器窗口的视频媒体。
     */
    VideoMedia getVideoMedia() PJSUA2_THROW(Error);
    
    /**
     * 显示或隐藏窗口。此操作对于原生窗口（VideoWindowInfo.isNative=true）无效，
     * 必须使用本机窗口API。
     *
     * @param show          设置为true以显示窗口，false以隐藏窗口。
     */
    void Show(bool show) PJSUA2_THROW(Error);
    
    /**
     * 设置视频窗口位置。此操作对于原生窗口（VideoWindowInfo.isNative=true）无效，
     * 必须使用本机窗口API。
     *
     * @param pos           窗口位置。
     */
    void setPos(const MediaCoordinate &pos) PJSUA2_THROW(Error);
    
    /**
     * 调整窗口大小。此操作对于原生窗口（VideoWindowInfo.isNative=true）无效，
     * 必须使用本机窗口API。
     *
     * @param size          新的窗口大小。
     */
    void setSize(const MediaSize &size) PJSUA2_THROW(Error);
    
    /**
     * 旋转视频窗口。此函数将改变视频方向，并可能改变视频窗口大小（宽度和高度互换）。
     * 此操作对于原生窗口（VideoWindowInfo.isNative=true）无效，必须使用本机窗口API。
     *
     * @param angle         旋转角度（度数），必须是90的倍数。
     *                      正值表示顺时针旋转，负值表示逆时针旋转。
     */
    void rotate(int angle) PJSUA2_THROW(Error);

    /**
     * 设置输出窗口。仅当底层视频设备支持PJMEDIA_VIDEO_DEV_CAP_OUTPUT_WINDOW能力且允许
     * 在运行时更改输出窗口时，此操作有效，否则将抛出错误。目前仅在Android上受支持。
     *
     * @param win           新的输出窗口。
     */
    void setWindow(const VideoWindowHandle &win) PJSUA2_THROW(Error);

    /**
     * 设置视频窗口全屏显示。仅当底层视频设备支持PJMEDIA_VID_DEV_CAP_OUTPUT_FULLSCREEN能力时，
     * 此操作有效。目前仅在SDL后端上受支持。
     *
     * @param enabled       如果为true，则设置全屏显示，否则为false。
     */
    void setFullScreen(bool enabled) PJSUA2_THROW(Error);

    /**
     * 设置视频窗口全屏显示。仅当底层视频设备支持PJMEDIA_VID_DEV_CAP_OUTPUT_FULLSCREEN能力时，
     * 此操作有效。目前仅在SDL后端上受支持。
     *
     * @param mode          全屏模式，参见pjmedia_vid_dev_fullscreen_flag。
     */
    void setFullScreen2(pjmedia_vid_dev_fullscreen_flag mode) PJSUA2_THROW(Error);

private:
    pjsua_vid_win_id            winId;
};

```

## 24.视频预览

```c
/**
 * 此结构包含用于 VideoPreview::start() 的参数。
 */
struct VideoPreviewOpParam {
    /**
     * 用于渲染捕获流进行预览的视频渲染器的设备ID。如果使用原生预览，则此参数将被忽略。
     *
     * 默认值: PJMEDIA_VID_DEFAULT_RENDER_DEV
     */
    pjmedia_vid_dev_index   rendId;

    /**
     * 初始时是否显示窗口。
     *
     * 默认值: PJ_TRUE。
     */
    bool                    show;

    /**
     * 窗口标志位。该值是 \a pjmedia_vid_dev_wnd_flag 的位掩码组合。
     *
     * 默认值: 0。
     */
    unsigned                windowFlags;

    /**
     * 视频媒体格式。默认情况下，此参数未初始化且不会被使用。
     *
     * 若要初始化它，请使用 MediaFormatVideo::init()。
     * 如果未初始化，则捕获设备将使用 PJMEDIA 封装的默认格式，例如：
     * - Android：使用第一个支持的尺寸和15fps的 PJMEDIA_FORMAT_I420
     * - iOS：使用尺寸352x288和15fps的 PJMEDIA_FORMAT_BGRA
     * 注意，当预览已经打开时，此设置将被忽略。
     */
    MediaFormatVideo        format;

    /**
     * 可选的输出窗口，用于显示视频预览。仅当视频设备支持 PJMEDIA_VID_DEV_CAP_OUTPUT_WINDOW
     * 能力且该能力不是只读时，才会使用此参数。
     */
    VideoWindowHandle       window;

public:
    /**
     * 默认构造函数初始化默认值。
     */
    VideoPreviewOpParam();

    /** 
     * 从 pjsip 转换。
     */
    void fromPj(const pjsua_vid_preview_param &prm);

    /**
     * 转换为 pjsip。
     */
    pjsua_vid_preview_param toPj() const;
};

/**
 * 视频预览。
 */
class VideoPreview {
public:
    /**
     * 构造函数。
     *
     * @param dev_id    设备ID。
     */
    VideoPreview(int dev_id);

    /**
     * 确定指定的视频输入设备是否具有内置的原生预览能力。这是一个便利函数，等同于查询设备的
     * PJMEDIA_VID_DEV_CAP_INPUT_PREVIEW 能力。
     *
     * @return          如果具有，则返回true。
     */
    bool hasNative();

    /**
     * 启动指定捕获设备的视频预览窗口。
     *
     * @param param     视频预览参数。
     */
    void start(const VideoPreviewOpParam &param) PJSUA2_THROW(Error);

    /**
     * 停止视频预览。
     */
    void stop() PJSUA2_THROW(Error);

    /**
     * 获取与捕获设备关联的预览窗口句柄（如果有）。
     */
    VideoWindow getVideoWindow();

    /**
     * 获取视频捕获设备的视频媒体或会议桥端口。
     *
     * @return          视频捕获设备的视频媒体。
     */
    VideoMedia getVideoMedia() PJSUA2_THROW(Error);

private:
    pjmedia_vid_dev_index devId;
    pjsua_vid_win_id winId;
    void updateDevId();
};

```

## 25.视频设备信息

```c
/**
 * 视频设备信息结构体。
 */
struct VideoDevInfo
{
    /**
     * 设备ID。
     */
    pjmedia_vid_dev_index id;

    /**
     * 设备名称。
     */
    string name;

    /**
     * 底层驱动程序名称。
     */
    string driver;

    /**
     * 视频设备支持的方向，即它是否支持仅捕获、仅渲染或两者都支持。
     */
    pjmedia_dir dir;

    /** 
     * 设备能力，作为 #pjmedia_vid_dev_cap 的位掩码组合。
     */
    unsigned caps;

    /**
     * 支持的视频格式数组。每个支持的视频格式中的某些字段可能设置为零或“未知”值，以指示该值未知或应忽略。
     * 当这些值未设置为零时，表示正在使用确切的格式组合。
     */
    MediaFormatVideoVector fmt;

public:
    /**
     * 默认构造函数。
     */
    VideoDevInfo() : id(-1), dir(PJMEDIA_DIR_NONE), caps(0)
    {}

    /**
     * 从 pjmedia_vid_dev_info 构造。
     */
    void fromPj(const pjmedia_vid_dev_info &dev_info);

    /**
     * 析构函数。
     */
    ~VideoDevInfo();
};

/** 
 * 警告：已弃用，请使用 VideoDevInfoVector2 替代。
 *
 * 视频设备信息数组。
 */
typedef std::vector<VideoDevInfo*> VideoDevInfoVector;

/** 视频设备信息数组 */
typedef std::vector<VideoDevInfo> VideoDevInfoVector2;

```

## 26.视频设备切换

```c
/**
 * 用于具有 PJMEDIA_VID_DEV_CAP_SWITCH 能力的设备切换参数。
 */
struct VideoSwitchParam
{
    /**
     * 要切换到的目标设备ID。一旦切换成功，视频流将使用此设备，并关闭旧设备。
     */
    pjmedia_vid_dev_index target_id;
};

```

## 27.视频设备管理器

```c
/**
 * 视频设备管理器。
 */
class VidDevManager {
public:

    /**
     * 初始化视频设备子系统。这将注册所有支持的视频设备工厂到视频设备子系统。
     *
     * 默认情况下，库在初始化时会自动初始化视频设备子系统，因此应用程序不需要调用此函数。
     * 但是，当设置 PJSUA_DONT_INIT_VID_DEV_SUBSYS 为非零时，应用程序应在访问视频设备之前调用此函数。
     */
    void initSubsys() PJSUA2_THROW(Error);

    /**
     * 刷新系统中安装的视频设备列表。此函数仅刷新视频设备列表，所有活动的视频流不受影响。
     * 刷新设备列表后，应用程序必须确保在调用接受视频设备索引作为参数的任何函数之前更新所有索引引用。
     */
    void refreshDevs() PJSUA2_THROW(Error);

    /**
     * 获取系统中安装的视频设备数量。
     *
     * @return          设备数量。
     */
    unsigned getDevCount();

    /**
     * 检索指定设备索引的视频设备信息。
     *
     * @param dev_id    视频设备ID。
     * 
     * @return          视频设备信息。
     */
    VideoDevInfo getDevInfo(int dev_id) const PJSUA2_THROW(Error);

#if !DEPRECATED_FOR_TICKET_2232
    /**
     * 警告：已弃用，请改用 enumDev2()。此函数在多线程环境中不安全。
     *
     * 枚举系统中安装的所有视频设备。
     *
     * @return          视频设备信息列表。
     */
    const VideoDevInfoVector &enumDev() PJSUA2_THROW(Error);
#endif

    /**
     * 枚举系统中安装的所有视频设备。
     *
     * @return          视频设备信息列表。
     */
    VideoDevInfoVector2 enumDev2() const PJSUA2_THROW(Error);

    /**
     * 根据驱动程序和设备名称查找设备索引。
     *
     * @param drv_name  驱动程序名称。
     * @param dev_name  设备名称。
     *
     * @return          设备ID。如果未找到设备，将抛出错误。
     */
    int lookupDev(const string &drv_name,
                  const string &dev_name) const PJSUA2_THROW(Error);

    /**
     * 获取指定能力的字符串信息。
     *
     * @param cap       能力ID。
     *
     * @return          能力名称。
     */
    string capName(pjmedia_vid_dev_cap cap) const;

    /**
     * 配置视频格式能力到视频设备。
     * 如果视频设备当前处于活动状态，方法将立即将设置转发到视频设备实例应用，如果支持的话。
     *
     * 此方法仅在 VideoDevInfo.caps 标志中设备具有 PJMEDIA_VID_DEV_CAP_FORMAT 能力时有效，否则将抛出错误。
     *
     * @param dev_id    视频设备ID。    
     * @param format    视频格式。
     * @param keep      指定是否保留设置以供将来使用。
     */
    void setFormat(int dev_id, 
                   const MediaFormatVideo &format, 
                   bool keep) PJSUA2_THROW(Error);

    /**
     * 获取视频设备的视频格式能力。
     * 如果视频设备当前处于活动状态，方法将向视频设备转发请求。
     * 如果视频设备当前处于非活动状态，并且应用程序之前设置了设置并标记了保留设置，则将返回该设置。
     * 否则，此方法将引发错误。
     *
     * 此方法仅在 VideoDevInfo.caps 标志中设备具有 PJMEDIA_VID_DEV_CAP_FORMAT 能力时有效，否则将抛出错误。
     *
     * @param dev_id    视频设备ID。
     * @return          视频格式。
     */
    MediaFormatVideo getFormat(int dev_id) const PJSUA2_THROW(Error);

    /**
     * 配置视频输入比例能力到视频设备。
     * 如果视频设备当前处于活动状态，方法将立即将设置转发到视频设备实例应用，如果支持的话。
     *
     * 此方法仅在 VideoDevInfo.caps 标志中设备具有 PJMEDIA_VID_DEV_CAP_INPUT_SCALE 能力时有效，否则将抛出错误。
     *
     * @param dev_id    视频设备ID。
     * @param scale     视频比例。
     * @param keep      指定是否保留设置以供将来使用。
     */
    void setInputScale(int dev_id, 
                       const MediaSize &scale, 
                       bool keep) PJSUA2_THROW(Error);

    /**
     * 获取视频输入比例能力到视频设备。
     * 如果视频设备当前处于活动状态，方法将向视频设备转发请求。
     * 如果视频设备当前处于非活动状态，并且应用程序之前设置了设置并标记了保留设置，则将返回该设置。
     * 否则，此方法将引发错误。
     *
     * 此方法仅在 VideoDevInfo.caps 标志中设备具有 PJMEDIA_VID_DEV_CAP_FORMAT 能力时有效，否则将抛出错误。
     *
     * @param dev_id    视频设备ID。
     * @return          视频格式。
     */
    MediaSize getInputScale(int dev_id) const PJSUA2_THROW(Error);

    /**
     * 配置快速切换到另一个视频设备。
     * 如果视频设备当前处于活动状态，方法将立即将设置转发到视频设备实例应用，如果支持的话。
     *
     * 此方法仅在 VideoDevInfo.caps 标志中设备具有 PJMEDIA_VID_DEV_CAP_OUTPUT_WINDOW_FLAGS 能力时有效，否则将抛出错误。
     * 
     * 注意，如果将设置保留以供将来使用，则将应用到任何设备，即使应用程序已更改要使用的视频设备。
     *
     * @param dev_id    视频设备ID。
     * @param flags     视频窗口标志。
     * @param keep      指定是否保留设置以供将来使用。
     */
    void setOutputWindowFlags(int dev_id, int flags, bool keep)
                              PJSUA2_THROW(Error);
    
    /**
     * 获取视频设备的窗口输出标志能力。
     * 如果视频设备当前处于活动状态，方法将向视频设备转发请求。
     * 如果视频设备当前处于非活动状态，并且应用程序之前设置了设置并标记了保留设置，则将返回该设置。
     * 否则，此方法将引发错误。
     *
     * 此方法仅在 VideoDevInfo.caps 标志中设备具有 PJMEDIA_VID_DEV_CAP_OUTPUT_WINDOW_FLAGS 能力时有效，否则将抛出错误。
     *
     * @param dev_id    视频设备ID。
     * @return keep     视频格式。
     */
    int getOutputWindowFlags(int dev_id) PJSUA2_THROW(Error);

    /**
     * 配置快速切换到另一个视频设备。
     * 如果视频设备当前处于活动状态，方法将立即将设置转发到视频设备实例应用，如果支持的话。
     *
     * 此方法仅在 VideoDevInfo.caps 标志中设备具有 PJMEDIA_VID_DEV_CAP_SWITCH 能力时有效，否则将抛出错误。
     *
     * @param dev_id    视频设备ID。
     * @param param     视频切换参数。
     */
    void switchDev(int dev_id,
                   const VideoSwitchParam &param) PJSUA2_THROW(Error);

    /**
     * 检查视频捕获设备当前是否处于活动状态，即是否已启动视频预览或正在使用该设备进行视频通话。
     *
     * @param dev_id    视频设备ID
     * 
     * @return          如果设备处于活动状态，则为 true。
     */
    bool isCaptureActive(int dev_id) const;

    /**
     * 配置视频捕获设备的视频方向。
     * 如果视频设备当前处于活动状态，方法将立即将设置转发到视频设备实例应用，如果支持的话。
     *
     * 此方法仅在 VideoDevInfo.caps 标志中设备具有 PJMEDIA_VID_DEV_CAP_ORIENTATION 能力时有效，否则将抛出错误。
     *
     * @param dev_id    视频设备ID。
     * @param orient    视频方向。
     */
    void setOrientation(int dev_id,
                        pjmedia_orient orient) PJSUA2_THROW(Error);

    /**
     * 获取视频捕获设备的视频方向。
     * 如果视频设备当前处于活动状态，方法将向视频设备转发请求。
     * 如果视频设备当前处于非活动状态，并且应用程序之前设置了设置并标记了保留设置，则将返回该设置。
     * 否则，此方法将引发错误。
     *
     * 此方法仅在 VideoDevInfo.caps 标志中设备具有 PJMEDIA_VID_DEV_CAP_ORIENTATION 能力时有效，否则将抛出错误。
     *
     * @param dev_id    视频设备ID。
     * @return          视频方向。
     */
    pjmedia_orient getOrientation(int dev_id) const PJSUA2_THROW(Error);

    /**
     * 启动视频捕获到本地显示。
     *
     * @param dev_id    视频设备ID。
     * @param cap_dev   视频捕获设备ID。
     * @param vid_win   视频窗口。
     */
    void startPreview(int dev_id,
                      int cap_dev,
                      const VidWin &vid_win) PJSUA2_THROW(Error);

    /**
     * 停止视频捕获预览。
     *
     * @param dev_id    视频设备ID。
     */
    void stopPreview(int dev_id) PJSUA2_THROW(Error);

    /**
     * 获取视频设备的设备类型。
     *
     * @param dev_id    视频设备ID。
     * @return          视频设备类型。
     */
    pjmedia_vid_dev_type getType(int dev_id) const PJSUA2_THROW(Error);

    /**
     * 获取视频设备的视频输入比例。
     *
     * @param dev_id    视频设备ID。
     * @return          视频输入比例。
     */
    MediaSize getDefInputScale(int dev_id) const PJSUA2_THROW(Error);

    /**
     * 获取视频设备的视频输入比例列表。
     *
     * @param dev_id    视频设备ID。
     * @return          视频输入比例列表。
     */
    MediaSizeVector getCapInputScales(int dev_id) const PJSUA2_THROW(Error);

    /**
     * 获取当前的默认视频设备。
     *
     * @return          默认视频设备ID。
     */
    int getDefaultDev() const PJSUA2_THROW(Error);

    /**
     * 设置默认视频设备。
     *
     * @param dev_id    视频设备ID。
     */
    void setDefaultDev(int dev_id) PJSUA2_THROW(Error);

    /**
     * 将视频设备绑定到特定的音频设备。
     *
     * @param dev_id    视频设备ID。
     * @param dev_index 音频设备索引。
     */
    void bindAudDev(int dev_id,
                    unsigned dev_index) PJSUA2_THROW(Error);

    /**
     * 获取与指定视频设备绑定的音频设备索引。
     *
     * @param dev_id    视频设备ID。
     * @return          音频设备索引。
     */
    unsigned getAudDev(int dev_id) const PJSUA2_THROW(Error);

    /**
     * 启动从指定的视频设备到视频渲染设备的视频显示。
     *
     * @param dev_id    视频设备ID。
     * @param rtp_sess  RTP会话。
     */
    void startRender(int dev_id,
                     const VidWin &vid_win) PJSUA2_THROW(Error);

    /**
     * 停止视频渲染。
     *
     * @param dev_id    视频设备ID。
     */
    void stopRender(int dev_id) PJSUA2_THROW(Error);

    /**
     * 根据特定的视频设备信息配置视频设备。
     *
     * @param dev_id    视频设备ID。
     * @param info      视频设备信息。
     */
    void setDevInfo(int dev_id,
                    const VideoDevInfo &info) PJSUA2_THROW(Error);

    /**
     * 获取视频设备的详细信息。
     *
     * @param dev_id    视频设备ID。
     * @return          视频设备信息。
     */
    VideoDevInfo getDetailedInfo(int dev_id) const PJSUA2_THROW(Error);

    /**
     * 重置所有视频设备。
     */
    void reset() PJSUA2_THROW(Error);

};

```

## 28.编码信息

````c
/**
 * 编解码器管理
 */

/**
 * 此结构体描述编解码器信息。
 */
struct CodecInfo
{
    /**
     * 编解码器的唯一标识符。
     */
    string codecId;

    /**
     * 编解码器优先级（整数，范围 0-255）。
     */
    pj_uint8_t priority;

    /**
     * 编解码器描述。
     */
    string desc;

    /**
     * 根据 pjsua_codec_info 结构体构造 CodecInfo。
     *
     * @param codec_info 要初始化的 pjsua_codec_info 结构体。
     */
    void fromPj(const pjsua_codec_info &codec_info);
};

````

## 29.标准参数

```c
/**
 * 编解码器特定参数的结构体，包含了名称=值对。
 * 这些编解码器特定参数按照标准（例如 RFC 3555）应用于 SDP 中的 'a=fmtp' 属性。
 */
typedef struct CodecFmtp
{
    string name;   /**< 参数名称 */
    string val;    /**< 参数值 */
} CodecFmtp;

```

## 30.编解码器设置

````c

/**
 * 音频编解码器参数信息。
 */
struct CodecParamInfo
{
    unsigned    clockRate;              /**< 采样率，单位 Hz            */
    unsigned    channelCnt;             /**< 声道数                     */
    unsigned    avgBps;                 /**< 平均带宽，单位比特/秒       */
    unsigned    maxBps;                 /**< 最大带宽，单位比特/秒       */
    unsigned    maxRxFrameSize;         /**< 最大帧大小                 */
    unsigned    frameLen;               /**< 解码器帧时长，单位毫秒     */
    unsigned    frameLenDenum;          /**< 解码器帧时长分母，如果帧时长为整数则为零 */
    unsigned    encFrameLen;            /**< 编码器帧时长，如果与解码器帧时长相同则为零 */
    unsigned    encFrameLenDenum;       /**< 编码器帧时长分母，如果帧时长为整数则为零 */
    unsigned    pcmBitsPerSample;       /**< PCM 采样位数               */
    unsigned    pt;                     /**< 负载类型                   */
    pjmedia_format_id fmtId;            /**< 源格式，编码器输入和解码器输出的格式 */
public:
    /**
     * 默认构造函数
     */
    CodecParamInfo() 
    : clockRate(0),
      channelCnt(0),
      avgBps(0),
      maxBps(0),
      maxRxFrameSize(0),
      frameLen(0),
      frameLenDenum(0),
      encFrameLen(0),
      encFrameLenDenum(0),
      pcmBitsPerSample(0),
      pt(0),
      fmtId(PJMEDIA_FORMAT_L16)
    {}
};

/**
 * 音频编解码器参数设置。
 */
struct CodecParamSetting
{
    unsigned    frmPerPkt;          /**< 每个数据包的帧数。           */
    bool        vad;                /**< 语音活动检测器。             */
    bool        cng;                /**< 舒适噪声生成器。             */
    bool        penh;               /**< 感知增强。                   */
    bool        plc;                /**< 丢包隐藏。                   */
    bool        reserved;           /**< 保留字段，必须为零。         */
    CodecFmtpVector encFmtp;        /**< 编码器的 fmtp 参数。         */
    CodecFmtpVector decFmtp;        /**< 解码器的 fmtp 参数。         */
    unsigned    packetLoss;         /**< 编码器预期丢包百分比。       */
    unsigned    complexity;         /**< 编码器复杂度，范围 0-10（最大）。 */
    bool        cbr;                /**< 是否为恒定比特率。           */
};

````

## 31.音频编解码器和查询音频编解码

```c
/**
 * 用于配置音频编解码器和查询音频编解码器工厂能力的详细编解码器属性。
 *
 * 请注意，编解码器参数还包含了 SDP 特定的设置，如 setting::decFmtp 和 setting::encFmtp，
 * 这些可能需要根据实际设置进行适当的设置。
 * 查看每个编解码器的文档以获取更多详细信息。
 */
struct CodecParam
{
    /** 信息 */
    struct CodecParamInfo info;
    /** 设置 */
    struct CodecParamSetting setting;

    void fromPj(const pjmedia_codec_param &param);

    pjmedia_codec_param toPj() const;
};

```

## 32.编码器参数配置

````c
/**
 * Opus 编解码器参数设置。
 */
struct CodecOpusConfig
{
    unsigned   sample_rate; /**< 采样率，单位 Hz。                     */
    unsigned   channel_cnt; /**< 通道数。                            */
    unsigned   frm_ptime;   /**< 帧时长，单位毫秒。                    */
    unsigned   frm_ptime_denum;/**< 帧时长分母。                      */
    unsigned   bit_rate;    /**< 编码器比特率，单位 bps。               */
    unsigned   packet_loss; /**< 编码器预期的丢包率百分比。             */
    unsigned   complexity;  /**< 编码器复杂度，0-10（10 最高）。        */
    bool       cbr;         /**< 是否为恒定比特率。                    */

    pjmedia_codec_opus_config toPj() const;
    void fromPj(const pjmedia_codec_opus_config &config);
};
````

## 33.Lyra设置

```c
/**
 * Lyra 编解码器设置。
 */
struct CodecLyraConfig
{
    /**
     * 解码器比特率由接收器请求。终端可以配置不同的比特率。
     * 例如，本地终端可能设置为3200比特率，而远程终端设置为6000比特率。
     * 在这种情况下，远程终端将以3200比特率发送数据，而本地终端将以6000比特率发送数据。
     * 有效的比特率有：3200、6000、9200。默认设置为 PJMEDIA_CODEC_LYRA_DEFAULT_BIT_RATE。
     */
    unsigned   bitRate;

    /**
     * Lyra 需要一些额外的模型文件，包括 lyra_config.binarypb、lyragan.tflite、quantizer.tflite 和 soundstream_encoder.tflite。
     * 此设置表示包含上述文件的文件夹路径。
     * 指定的文件夹应包含这些文件。如果提供的文件夹无效，则编解码器创建将失败。
     */
    string     modelPath;

    pjmedia_codec_lyra_config toPj() const;
    void fromPj(const pjmedia_codec_lyra_config &config);
};

```

## 34.视频编解码器

```c
/**
 * 视频编解码器详细属性，用于配置视频编解码器和查询视频编解码器工厂的能力。
 * 
 * 请注意，编解码器参数还包含特定于SDP的设置，如 #decFmtp 和 #encFmtp，根据实际设置可能需要适当设置。
 * 详细信息请参阅每个编解码器的文档。
 */
struct VidCodecParam
{
    pjmedia_dir         dir;            /**< 方向                            */
    pjmedia_vid_packing packing;        /**< 打包策略                        */

    struct
    MediaFormatVideo    encFmt;         /**< 编码格式                        */
    CodecFmtpVector     encFmtp;        /**< 编码器fmtp参数                  */
    unsigned            encMtu;         /**< MTU或最大负载大小设置            */

    struct
    MediaFormatVideo    decFmt;         /**< 解码格式                        */
    CodecFmtpVector     decFmtp;        /**< 解码器fmtp参数                  */

    bool                ignoreFmtp;     /**< 忽略fmtp参数。如果设置为true，
                                             则编解码器将仅应用在encFmt和decFmt中指定的格式设置。*/

public:
    /**
     * 默认构造函数
     */
    VidCodecParam() : dir(PJMEDIA_DIR_NONE),
                      packing(PJMEDIA_VID_PACKING_UNKNOWN),
                      encMtu(0),
                      ignoreFmtp(false)
    {}

    void fromPj(const pjmedia_vid_codec_param &param);

    pjmedia_vid_codec_param toPj() const;
};

```

## 34.媒体格式更改事件

```c
/**
 * 此结构体描述了媒体格式更改事件。
 */
struct MediaFmtChangedEvent
{
    unsigned newWidth;      /**< 新宽度。 */
    unsigned newHeight;     /**< 新高度。 */
};

```

## 35.音频设备错误事件

```c
/**
 * This structure describes an audio device error event.
 */
struct AudDevErrorEvent
{
    pjmedia_dir             dir;        /**< The direction.         */
    int                     id;         /**< The audio device ID.   */
    pj_status_t             status;     /**< The status code.       */
};
```

## 36.媒体事件数据

```c
/**
 * 此联合体定义了多种可能的媒体事件数据类型，包括媒体格式更改事件和音频设备错误事件。
 */
typedef union MediaEventData {
    /**
     * 媒体格式更改事件数据。
     */
    MediaFmtChangedEvent    fmtChanged;

    /**
     * 音频设备错误事件数据。
     */
    AudDevErrorEvent        audDevError;
    
    /**
     * 指向用户事件数据存储的指针，如果它在这个结构体之外。
     */
    GenericData             ptr;

} MediaEventData;

```

## 37.媒体事件

```c
/**
 * 此结构描述了一个媒体事件，包括事件类型、事件相关的附加数据或参数（具体数据类型取决于报告的事件类型）、以及指向原始 pjsip 的 pjmedia_event 的指针（仅在从 PJSIP 的 pjmedia_event 转换时有效）。
 */
struct MediaEvent
{
    /**
     * 事件类型。
     */
    pjmedia_event_type          type;

    /**
     * 事件的附加数据或参数。数据类型将根据报告的事件类型而定。
     */
    MediaEventData              data;
    
    /**
     * 指向原始 pjmedia_event 的指针。仅当该结构体从 PJSIP 的 pjmedia_event 转换而来时有效。
     */
    void                       *pjMediaEvent;

public:
    /**
     * 默认构造函数。
     */
    MediaEvent() : type(PJMEDIA_EVENT_NONE), pjMediaEvent(NULL)
    {}

    /**
     * 从 pjsip 转换。
     */
    void fromPj(const pjmedia_event &ev);
};

```

