Väijy - SP network focused NMS
==============================

First goal would be identify components that NMS has and how they interact
which each other, what kind of API they expose.

    * Event handler (all events are sent blindly here)
    * Alarm correlator/suppressor (if LDP/ISIS has parent interface and
    interface is down, add information to alarm for handler consumption. Does this
    mean we queue alarms for few seconds to wait for parent alarm? Or should we
    explicitly do blocking poll for parent status before child alarm is given to
    alarm handler?) * Alarm handler (MQ2Email, MQ2SMS, MQ2Ticketing, depending on
    SLA, type etc) * Poller * Device model (describes device, implements alarm
    models)
    * Alarm model (defines alarm)
    * Logger
    * DB abstraction (1st backend postgres? sqlite?)
    * MQ abstraction (1st backend MQTT? AMQP?)

Overall goals
=============

Architecture should not forbid these, but maybe no concrete plan to implement
needs to be useful out-of-the-box for typical SP use-case.

    * easy integration, get customer/devices from external systems, send data to external systems
    * support multiple pollers
    * IP address is not assume to be unique (poller in customer1 and customer2 LAN, same addressing)
    * allow integration for source data (devices, customers...)
    * support multiple domains(?) (maybe you're consultant/integrator with many networks to handle?)
    * decouple monitor/alarm type from platform/vendor, i.e. LDP up/down is
    abstract type, platform/vendor specification tells how this platform implements
    it
    * hierarchy, do not send ldp/igp alarm if int is down (different MQ topic?
    JSON key for 'masked_by: ', indicating parent alarm suppressing it? So
    alarm2email can ignore message with masked_by?
    * easy integration between multiple installations? (maybe I have one installation monitoring managed LAN, one installation CPE, and I don't want to raise alarms on LAN switches being down, if CPE device in this site is down?)
    * fully unit tested and integration tested
    * attempt to be triggered/evented/reactive, information should propagate
    immediately, not periodically. If I add device, it should be there immediately.
    (why does it have to be immediately?)

release 0
=========

infrastructure that will not forbid overall goals

    * no graphs, no disco
    * snmp poller, snmp traps, ping
    * router, interface, ldp, igp, bgp up/down
    * alarms in MQ
    * MQ2email and MQ2sms

Tools
=====

    * ruby? python? rust? go?
    * rails?
    * postgres/sqlite?
    * mqtt/amqp?
    * golang? (when performance critical)
    * some tsdb for snmp polling?
    * storm?

    graphite alternatives tsdb thingies:
    http://influxdb.com/
    https://github.com/ahupowerdns/metronome/
    http://opentsdb.net

poller
======

    * async or sync?
        - async means we need to read TSDB constantly, to confirm data is being inserted to DB in timely manner
        (one could use deadlining here, we could mark when we expect result to
            be returned, and this way we don't need constantly poll the database,
            intervalled polls are ok. also, we can safely discard resending before
            retrying, if it's not going to answer, it's problem anyways.) - sync means we
            immediately know about failed poll, can retry, can log
        - TSDB is optimized for write, read is costly
        - async means trivial scale to linerate
        - sync with programming language with green threads is no problem,
        having thousands of low-cost routine/greenthreads would be no problem, each
        mostly being in I/O wait
    * how to get information what to poll? (mq? sql?)
    * where to store data (tsdb?)
    * how to track ifindex <-> interface mapping? Should we assume it to always
    change between polling intervals? What is authorative ID for interface ifAlias?
    (ifName?)

