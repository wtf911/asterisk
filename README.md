# Work-In-Progress GVSIP changes to Asterisk

Currently not working. REGISTER succeeds, but
- incoming calls don't work due to media problems?
- outgoing calls don't receive a response to the INVITE request

##Config example
```
[transport-tls]
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
outbound_auth=gvsip
outbound_proxy=sips:obihai.telephony.goog:5061\;lr
aors=gvsip
media_encryption=dtls
media_encryption_optimistic=yes
ice_support=yes

[gvsip]
type=identify
endpoint=gvsip
match=obihai.telephony.goog
```
