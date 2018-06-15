# Work-In-Progress GVSIP changes to Asterisk

## Status
Currently only parially working, and still has too many GV-specific hacks that need to be wrapped in config options:

- registering with oauth token: WORKING
- incoming calls: WORKING
- outgoing calls: NOT working (don't receive a response to the INVITE request)

## Config example

### pjsip.conf
```
[gvsip]
type=transport
protocol=tls
bind=0.0.0.0:5061

[gvsip]
type=registration
outbound_auth=gvsip
server_uri=sip:obihai.sip.google.com
outbound_proxy=sips:obihai.telephony.goog:5061\;lr
client_uri=sip:88WHATEVER_1@obihai.sip.google.com
retry_interval=60

[gvsip]
type=auth
auth_type=oauth
refresh_token=<your oauth refresh token>
oauth_clientid=<your oauth client id>
oauth_secret=<your oauth client secret>
username=88WHATEVER_1
realm=obihai.sip.google.com

[gvsip]
type=aor
contact=sip:obihai.sip.google.com

[gvsip]
type=endpoint
context=from-external
disallow=all
allow=ulaw
allow=opus
outbound_auth=gvsip
outbound_proxy=sips:obihai.telephony.goog:5061\;lr
aors=gvsip
media_encryption=dtls
dtls_cert_file=/etc/asterisk/keys/asterisk.crt
dtls_private_key=/etc/asterisk/keys/asterisk.key
dtls_setup=actpass
media_encryption_optimistic=yes
direct_media=no
ice_support=yes
rtcp_mux=yes
use_avpf=yes
media_use_received_transport=yes

[gvsip]
type=identify
endpoint=gvsip
match=obihai.telephony.goog
```

### rtp.conf
```
[general]
stunaddr=stun.l.google.com:19302
```

### extensions.conf
```
[from-internal]
exten => _NXXNXXXXXX,1,Set(CALLERID(dnid)=1${CALLERID(dnid)})
exten => _NXXNXXXXXX,n,Goto(1${EXTEN},1)
exten => _1NXXNXXXXXX,1,Dial(PJSIP/${EXTEN}@gvsip,,r)
```
