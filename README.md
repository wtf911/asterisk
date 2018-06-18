# Work-In-Progress GVSIP changes to Asterisk

## Status
- registering with oauth token: WORKING
- incoming calls: WORKING
- outgoing calls: WORKING

## Known Issues
- TBD

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
support_path=yes
support_outbound=yes
support_replaces=yes

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
outbound_proxy=sips:obihai.telephony.goog:5061\;lr\;hide
aors=gvsip
direct_media=no
ice_support=yes
rtcp_mux=yes
media_use_received_transport=yes
outbound_registration=gvsip

[gvsip]
type=identify
endpoint=gvsip
match=obihai.telephony.goog

[gvsip]
type=system
disable_secure_dlg_check=yes

[gvsip]
type=global
keep_alive_interval=300
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
