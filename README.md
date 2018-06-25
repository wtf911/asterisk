# Work-In-Progress GVSIP changes to Asterisk

## Status
- registering with oauth token: WORKING
- incoming calls: WORKING
- outgoing calls: WORKING

## Known Issues
- outgoing calls that are not answered within 25 seconds do not go to voicemail, instead receiving a 'SIP/2.0 603 media inactivity' response and terminating

## Config example

### pjsip.conf
```
; if using chan_sip to host sip clients instead of chan_pjsip,
; you wont have the (required) udp transport that supports those
; clients. if so, just make a dummy one on a port that won't
; conflict with chan_sip
;[incoming-registrations-unused-but-required]
;type=transport
;protocol=udp
;bind=0.0.0.0:9999

[gvsip]
type=transport
protocol=tls
bind=0.0.0.0:5061

[gvsip]
type=registration
outbound_auth=gvsip
server_uri=sip:obihai.sip.google.com
outbound_proxy=sip:obihai.telephony.goog:5061\;transport=tls\;lr
client_uri=sip:<your choice for device identifier>@obihai.sip.google.com
retry_interval=60
support_path=yes
support_outbound=yes

[gvsip]
type=auth
auth_type=oauth
refresh_token=<your oauth refresh token>
oauth_clientid=<your oauth client id>
oauth_secret=<your oauth client secret>
username=<your choice for device identifier>
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
outbound_proxy=sip:obihai.telephony.goog:5061\;transport=tls\;lr\;hide
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
type=global
debug=true
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
