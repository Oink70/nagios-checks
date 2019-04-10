# nagios-checks
Various Nagios checks

## `check_ifstat`

Uses `ifstat(1)` to monitor interface bandwidth.

```
check_ifstat usage: ./check_ifstat [OPTION ...]
 
check_ifstat performs bandwidth checks using ifstat(1)
 
Examples: 
  ./check_ifstat -i br0 -w 1024 -c 2048 # check interface "br0". >=1024kb/s is warning, >=2048kb/s critical, both in- and outgoing traffic.
  ./check_ifstat -i br0 -W 2048 -C 4096 # check interface "br0", >=2048kb/s is warning, >=4096kb/s critical for outgoing, default values for incoming traffic.
  ./check_ifstat -b -i eth0             # check interface "br0" using kbit/s as unit and default warning/critical levels.
 
Options: 
    -b              Use kbit/s instead of kb/s
    -i <interface>  Specify network interface to monitor
    -w <warn>       Warning threshold for incoming traffic
    -c <crit>       Critical threshold for incoming traffic
    -W <warn_out>   Warning threshold for outgoing traffic (optional, defaults to internal defaults or -w)
    -C <crit_out>   Critical threshold for outgoing traffic (optional, defaults to internal values or -c)
```
