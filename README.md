* overall goals * 
  - architecture should not forbid these, but maybe no concrete plan to implement
  - needs to be useful out-of-the-box for typical SP use-case
  - easy integration, get customer/devices from external systems, send data to
    external systems
  - support multiple pollers
  - IP address is not assume to be unique (poller in customer1 and customer2
    LAN, same addressing)
  - allow integration for source data (devices, customers...)
  - support multiple domains(?) (maybe you're consultant/integrator with many
    networks to handle?)
  - decouple monitor/alarm type from platform/vendor, i.e. LDP up/down is
    abstract type, platform/vendor specification tells how this platform
    implements it
  - hierarchy, do not send ldp/igp alarm if int is down (different MQ topic?
    JSON key for 'masked_by: <int>', indicating parent alarm suppressing it?
    So alarm2email can ignore message with masked_by?
  - easy integration between multiple installations? (maybe I have one
    installation monitoring managed LAN, one installation CPE, and I don't
    want to raise alarms on LAN switches being down, if CPE device in this
    site is down?)
  - fully unit tested

* release 0 *
   - infrastructure that will not forbid overall goals
   - no graphs, no disco
   - snmp poller, snmp traps, ping
   - router, interface, ldp, igp, bgp up/down
   - alarms in MQ
   - MQ2email and MQ2sms

* tools *
   - ruby?
   - rails?
   - postgres/sqlite?
   - golang? (when performance critical)
   - tsdb?
   - storm?

* poller *
   - assync
   - how to get information what to poll? (mq? sql?)
   - where to store data (tsdb?)
