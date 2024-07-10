```shell
export LD_LIBRARY_PATH=/path/to/libopenh264/directory:$LD_LIBRARY_PATH

```

```CPP
#include <iostream>
#include <pjsua2.hpp>
#include <err.h>

using namespace pj;
using std::cout;
using std::endl;
class MyCall : public Call
{
public:
    explicit MyCall(Account &acc, int call_id = PJSUA_INVALID_ID)
            : Call(acc, call_id)
    { }

    ~MyCall() override
    = default;

    // Notification when call's state has changed.
    void onCallState(OnCallStateParam &prm) override;

    // Notification when call's media state has changed.
    void onCallMediaState(OnCallMediaStateParam &prm) override;
};

void MyCall::onCallState(OnCallStateParam &prm) {
    Call::onCallState(prm);
}

void MyCall::onCallMediaState(OnCallMediaStateParam &prm) {
    Call::onCallMediaState(prm);
}

class MyAccount : public Account
{
public:
    MyAccount() = default;
    ~MyAccount() override = default;

    void onRegState(OnRegStateParam &prm) override
    {
        AccountInfo ai = getInfo();
        cout << (ai.regIsActive? "*** Register: code=" : "*** Unregister: code=")
             << prm.code << endl;
    }

    void onIncomingCall(OnIncomingCallParam &iprm) override
    {
        Call *call = new MyCall(*this, iprm.callId);

        // Just hangup for now
        CallOpParam op;
        op.statusCode = PJSIP_SC_DECLINE;
        call->hangup(op);

        // And delete the call
        delete call;
    }
};
void StartPreview(int device_id, void* hwnd, int width, int height, int fps)
{
    try {
        // Set the video capture device format.
        VidDevManager &mgr = Endpoint::instance().vidDevManager();
        MediaFormatVideo format = mgr.getDevInfo(device_id).fmt[0];
        format.width    = width;
        format.height   = height;
        format.fpsNum   = fps;
        format.fpsDenum = 1;
        mgr.setFormat(device_id, format, true);

        // Start the preview on a panel with window handle 'hwnd'.
        // Note that if hwnd is set to NULL, library will automatically create
        // a new floating window for the rendering.
        VideoPreviewOpParam param;
        param.window.handle.window = (void*) hwnd;

        VideoPreview preview(device_id);
        preview.start(param);
    } catch(Error& err) {
    }
}
int main() {
    auto *ep = new Endpoint;
    try{
        ep->libCreate();
    }catch (Error&err){
        cout << "Startup error: " << err.info() << endl;
    }
    EpConfig epConfig;
    ep->libInit(epConfig);
    try {
        TransportConfig tcfg;
        tcfg.port = 5060;
        TransportId tid = ep->transportCreate(PJSIP_TRANSPORT_UDP, tcfg);
    }catch (Error&err){
        cout << "Startup error: " << err.info() << endl;
    }
    ep->libStart();
    AccountConfig acc_cfg;
    acc_cfg.idUri = "sip:8805@192.168.31.30:7070";
    acc_cfg.regConfig.registrarUri = "sip:192.168.31.30:7070";
    acc_cfg.sipConfig.authCreds.emplace_back("digest", "*", "8805", 0, "hnsoc66199188" );

    auto *acc = new MyAccount;
    try {
        acc->create(acc_cfg);
    } catch(Error& err) {
        cout << "Account creation error: " << err.info() << endl;
    }
//    AudioMediaPlayer player;
//    AudioMedia& speaker_media = Endpoint::instance().audDevManager().getPlaybackDevMedia();
//    try {
//        player.createPlayer("/home/weilan/Desktop/wyf/vlpjsua/resource/music.wav");
//        player.startTransmit(speaker_media);
//    } catch(Error& err) {
//    }
   auto m_pViewPreview = new VideoPreview(PJMEDIA_VID_DEFAULT_CAPTURE_DEV);
    try {
        VideoPreviewOpParam param;
        param.rendId = PJMEDIA_VID_DEFAULT_RENDER_DEV;
        param.show = PJ_FALSE;

        m_pViewPreview->start(param); //这里得不到是否成功的反馈

    } catch (Error&err) {
        cout<<"robin:openCamera failed:"<<err.reason.c_str();
         return 0;
    }


    pj_thread_sleep(10000000);
    std::cout << "Hello,World!" << std::endl;
    return 0;
}

```

