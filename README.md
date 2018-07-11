# Work-In-Progress GVSIP changes to Asterisk

## Status
- registering with oauth token: WORKING
- incoming calls: WORKING
- outgoing calls: WORKING

## Known Issues
see https://github.com/naf419/asterisk/wiki

## Config example

### pjsip.conf
```
[global]
type=global
debug=true

; if using chan_sip to host sip clients instead of chan_pjsip,
; you wont have the (required) udp transport that supports those
; clients. if so, just make a dummy one on a port that won't
; conflict with chan_sip
;[incoming-registrations-unused-but-required]
;type=transport
;protocol=udp
;bind=0.0.0.0:9999

[transport_tls]
type=transport
protocol=tls
bind=0.0.0.0:5061


[gvsip1]
type=registration
outbound_auth=gvsip1
server_uri=sip:obihai.sip.google.com
outbound_proxy=sip:obihai.telephony.goog:5061\;transport=tls\;lr\;hide
client_uri=sip:<your choice for unique user identifier>@obihai.sip.google.com
retry_interval=60
support_path=yes
support_outbound=yes
line=yes
endpoint=gvsip1
contact_additional_params=obn=<name to appear on GV settings page>
transport=transport_tls
transport_reuse=no

[gvsip1]
type=auth
auth_type=oauth
refresh_token=<your oauth refresh token>
oauth_clientid=<your oauth client id>
oauth_secret=<your oauth client secret>
username=<your choice for unique user identifier>
realm=obihai.sip.google.com

[gvsip1]
type=aor
contact=sip:obihai.sip.google.com

[gvsip1]
type=endpoint
context=from-external
disallow=all
allow=ulaw
allow=opus
outbound_auth=gvsip1
outbound_proxy=sip:obihai.telephony.goog:5061\;transport=tls\;lr\;hide
aors=gvsip1
direct_media=no
ice_support=yes
rtcp_mux=yes
media_use_received_transport=yes
outbound_registration=gvsip1


[gvsip2]
type=registration
outbound_auth=gvsip2
server_uri=sip:obihai.sip.google.com
outbound_proxy=sip:obihai.telephony.goog:5061\;transport=tls\;lr\;hide
client_uri=sip:<your choice for 2nd unique user identifier>@obihai.sip.google.com
retry_interval=60
support_path=yes
support_outbound=yes
line=yes
endpoint=gvsip2
contact_additional_params=obn=<name to appear on GV settings page>
transport=transport_tls
transport_reuse=no

[gvsip2]
type=auth
auth_type=oauth
refresh_token=<your 2nd oauth refresh token>
oauth_clientid=<your oauth client id>
oauth_secret=<your oauth client secret>
username=<your choice for 2nd unique user identifier>
realm=obihai.sip.google.com

[gvsip2]
type=aor
contact=sip:obihai.sip.google.com

[gvsip2]
type=endpoint
context=from-external
disallow=all
allow=ulaw
allow=opus
outbound_auth=gvsip2
outbound_proxy=sip:obihai.telephony.goog:5061\;transport=tls\;lr\;hide
aors=gvsip2
direct_media=no
ice_support=yes
rtcp_mux=yes
media_use_received_transport=yes
outbound_registration=gvsip2
```

### rtp.conf
```
[general]
stunaddr=stun.l.google.com:19302
```

### extensions.conf
```
[from-internal]
exten => _+1NXXNXXXXXX,1,Goto(${EXTEN:2},1)
exten => _NXXNXXXXXX,1,Set(CALLERID(dnid)=1${CALLERID(dnid)})
exten => _NXXNXXXXXX,n,Goto(1${EXTEN},1)
exten => _1NXXNXXXXXX,1,Dial(PJSIP/${EXTEN}@gvsip1)
```
