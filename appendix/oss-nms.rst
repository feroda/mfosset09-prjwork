Appendice A - Osservazioni su NMS open source in lista cisco-nsp
================================================================

La mailing-list **cisco-nsp** è una lista di discussione per persone che usano
apparati Cisco in Network Service Provider.

Si riporta qui di seguito un messaggio che include, nella parte quotata, alcune osservazioni 
sui sistemi di monitoraggio Open Source e la risposta dell'Ing. Michele Bergonzoni che 
è sistemista senior di reti di telecomunicazioni (nda: direi più un `network god`), nonché
autore unico di `pinger` e coautore di `SANET`.

Il messaggio assume un significato di particolare importanza in quanto riesce a cogliere 
gli aspetti tecnici più nascosti per cui si è deciso di sviluppare un sistema alternativo
per il monitoraggio delle reti.

Inizia qui il messaggio reperibile all'indirizzo 
`[c-nsp] Free NMS Tools <https://puck.nether.net/pipermail/cisco-nsp/2009-July/062347.html>`__.

.. sourcecode: TODO preformattato ?

    Michele Bergonzoni bergonz at labs.it
    Fri Jul 17 09:26:28 EDT 2009
    
        * Previous message: [c-nsp] Free NMS Tools
        * Next message: [c-nsp] Free NMS Tools
        * Messages sorted by: [ date ] [ thread ] [ subject ] [ author ]
    
    Saku Ytti <saku at ytti.fi> said:
    
    > To me all OSS NMS solutions out seem like they are made by 
    > coder-in-server-admin not coder-in-network-admin, and as such seem to
    >  have much more integration with servers than with network
    
    This is one of the reasons why over the years we developed sanet, the
    other being that many NMSs are very chatty and tend to keep you up all 
    night when relayed on pagers and SMS.
    
    Sanet is OSS but in prerelease, meaning that we use it and it works, but
    its documentation is not quite complete and it is not easy to install.
    If you are willing to setup many python packages by hand and to explore
    funcionalities without a concise HOWTO, or if you are just interested in
    the OIDs, you can find it at sanet.sf.net, the SVN version being much
    better (expecially for maps and reports) than the downloadable version.
    
    We use it mainly in multivendor corporate networks, but we have one case 
    of cisco MPLS carrier network.
    
    > Why don't they ship with MIBs or just specific OIDs for few top
    > vendors important traps etc?
    
    sanet has a a library of checks for common cisco, HP, fortigate and 
    other vendor's OIDs. Sorry we don't collect traps nor syslog in the 
    sanet DB, we usually transform traps to syslog (net-snmp snmptrapd) and 
    collect syslog (we are accustomed to grepping the results).
    
    > Adding appropriate reaction classification.
    
    Sanet does not react. You can trivially achieve that by binding scripts
    to emails, etc., but we are quite scared of this kind of triggering and
    we don't do it (yet).
    
    > People want NMS to automatically monitor BGP
    
    In the library there is the check for the BGP neighborship state:
    
    "1.3.6.1.2.1.15.3.1.2.$peer_ip:$community@$node == 6"
    
    it is not "automatic" because in sanet you have to decide all the
    monitoring that you want it to do.
    
    > OSPF
    
    We have the OSPF neighborship state check:
    
    "1.3.6.1.2.1.14.10.1.6.byRegexpUnique(1.3.6.1.2.1.4.20.1.2,^$ifindex$).0:$linked_community@$linked_node
    == 8"
    
    but it works only for point-to-point links. I'm sure we can make it better.
    
    > IS-IS
    
    Sorry no IS-IS here, but of course you can define your own if you know
    the OIDs. Please contribute it back if you do.
    
    > LDP
    
    We have an LDP neighborship check:
    
    "1.3.6.1.2.1.10.166.4.1.3.2.1.2.byBinaryIP(1.3.6.1.2.1.10.166.4.1.3.2.1.5:$community@$node,$peer_ip):$community@$node
    == 2"
    
    > status of some other CPU/memory than just control-plane
    
    Well, for IOS we usually check processor memory and IO memory. OIDs and
    suggestions are very, very welcome.
    
    > Other thing that annoys me is how SNMP pollers are implemented, 
    > they're blocking
    
    You are definitely right. Our poller is multithreaded but each thread is
    blocking, with adjustable timeouts.
    
    > While having SNMP poller poll 140k OID per second on 386 class PC is
    > rather trivial, using two process strategy, where single process
    > spews packets outs, and another listens what comes back, completely
    > asynchronous
    
    It was not so trivial for us, so we made it synchronous. The tricky part
    is to collect all the SNMP vars used to form an expression in the same
    moment (of course with some approximation), remembering what you asked
    for at each poll cycle. It is trivial if you just check variables
    against ranges, but we build complex expressions with current and past
    variables.
    
    Anyway, patches are welcome...
    
    > I've also only seen alarms based on absolute values of different
    > counters
    
    sanet can combine current and past (last poll cycle) vars, like this
    expression for a threshold on broadcast packets received:
    
    "((1.3.6.1.2.1.2.2.1.12.$ifindex:$community@$node -
    1.3.6.1.2.1.2.2.1.12.$ifindex#$node) / $delta) < $threshold"
    
    ($delta is the time in second since last poll)
    
    > This type of 'trending' module should be relatively easy, and could
    > be reused by any counter values.
    
    This is a good idea, I will try to think about how this can fit into our
    existing software or if a new check type is needed for that.
    
    > I demoed zenoss with 27 routers and it froze trying to poll their 
    > interface (granted there are very many interfaces)
    
    We measure installations from the number of targets (yes/no checks) and
    measures (graphs). One of our big ones is:
    
    root at XXXXXX:~# sanet-cli
    Benvenuti in SANET 2 su XXXXX
    
    sanet# sh ver
    ...
    Configuration defines 831 interfaces, 523 nodes, 409 links, 9868
    targets, 2089 measures.
    Targets summary: 9 down, 1 failing, 38 uncheckable, 0 out of time, 9820 up
    Measures summary: 2042 updated in last 2 mins, 2089 in last 5 mins, 2089
    in last 30 mins
    
    (this is running on a XEN VM, I/O being the bottleneck)
    
    I'm sure people on this list will appreciate the configuration via CLI 
    (web is used for displaying the status), which is shamelessly copied 
    from IOS. This was "sh ver", and in order to configure monitoring you 
    start with "conf t". You will probably appreciate physical maps (a /30 
    is a line, not a line with a cloud in between), NTP checks, IPv4/IPv6 
    pings with adjustable payload length, iface designation by name, MAC, 
    IP, CDP neighbor, route, IOS description, etc (no ifindex blues).
    
    Hope this helps,
    					Bergonz
    
    
    -- 
    Ing. Michele Bergonzoni - Laboratori Guglielmo Marconi S.p.a.
    Phone:+39-051-4392826 Fax:+39-051-6153683 e-mail: bergonz at labs.it
    alt.advanced.networks.design.configure.operate

