# nagios-checks
Various Nagios checks. These may or may not work for you, you may have to adapt details of it. Be aware.

## `check_dns_record`

Requires `host`, `ifconfig` and `awk` to be installed.

```
check_dns_record usage: ./check_dns_record [OPTION ...]

check_dns_record - checks a DNS record of given type against an expected result

Examples:
  ./check_dns_record -r www.acme.com -i eth0                  # check host "www.acme.com" against the primary address of interface eth0
  ./check_dns_record -r www.acme.com -t AAAA -a 2a04:1:2:3::1 # check host "www.acme.com" against the given IPv6 address, using AAAA

Options:
    -r <record>      DNS record to resolve
    -e <expectation> Expected result
    -s <dns_server>  DNS server to talk to
    -i <interface>   Primary address of interface to use as expected result (use with -t AAAA for IPv6)
    -t <record_type> Record type to check for (defaults to A)
    -w               WARN only when DNS/Address mismatches (default: CRIT)

NOTE: '-i' does not reliably work with '-t AAAA'/IPv6!
```

## `check_electrumx`

Needs `awk`, `bc`, `curl`, `jq`, `tr` and `electrumx_rpc` installed.

```
check_electrumx usage: ./check_electrumx [OPTION ...]

check_electrumx - checks an ElectrumX server for its status

Examples:
  ./check_electrumx -H 10.0.0.2 -p 8012 -r https://explorer.somecoin.org/get/current_height # checks the electrum server at 10.0.0.2:8012 using the explorer api url specified with -r

Options:
    -H <host>                 ElectrumX host to check (default: 127.0.0.1)
    -p <port>                 Port number to connect to (default: 10000)
    -r <remote_height_url>    URL to get remote height from (defaults to iquidus api call on 127.0.0.1:8080)
    -w <uptime_sec_warn>      Uptime in seconds after which WARN status is raised (default: 60 days)
    -c <uptime_sec_crit>      Uptime in seconds after which CRIT status is raised (default: 75 days)
    -W <height_distance_warn> Height distance at which WARN is raised (default: 3)
    -C <height_distance_crit> Height distance at which CRIT is raised (default 5)

Notes:
    - Uptime is compared against full days only, rest of ElectrumX uptime data is disregarded
```

## `check_ifstat`

Requires `ifstat`, `bc` and `awk` to be installed.

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

## `check_multiple_records`

Requires `awk` and `host` to be installed.

```
check_multiple_records usage: ./check_multiple_records [OPTION ...]

check_multiple_records - checks a given comparison value against up to nine given DNS records of a given type

Examples:
  ./check_multiple_records -t A -1 web0.acme.com -2 web1.acme.com -c 1.2.3.4 # check wether any of "web0.acme.com web1.acme.com" matches 1.2.3.4
  ./check_multiple_records -t AAAA -1 web0.acme.com -i eth0 -w               # check wether "web0.acme.com" resolved to the IPv6 address of eth0, only WARNing if not

Options:
    -1..-9 <record>  DNS record to resolve, up to 9 in total. first one is mandatory.
    -c <comparison>  Result to compare to
    -s <dns_server>  DNS server to talk to
    -i <interface>   Primary address of interface to use as expected result (use with -t AAAA for IPv6)
    -t <record_type> Record type to check for (defaults to A)
    -a               All checked records need to match (default: only one needs to match)
    -w               WARN only when DNS/Address mismatches (default: CRIT)

NOTE: using '-i' will only match the first public/global address of the given interface!
```

## `check_ssl_cert`

Requires `bc`, `awk` and `curl` to be installed.

```
check_ssl_cert usage: ./check_ssl_cert [OPTION ...]

check_ssl_cert - checks ssl certificates for CN and expiry using curl(1)

Examples:
  ./check_ssl_cert -H www.acme.com -p 4443            # check host "www.acme.com" using 30 days warn/14 days crit thresholds.
  ./check_ssl_cert -H www.acme.com -w 864000 -c 86400 # check host "www.acme.com" using 10 days warn/1 day crit thresholds.

Options:
    -H <hostname>   Hostname to connect to
    -p <port>       Port number to connect to (default 443)
    -w <warn_sec>   Warning threshold if certificate expiry is closer than this many seconds (default 30 days)
    -c <crit_sec>   Critical threshold if certificate expiry is closer than this many seconds (default 14 days)
```

## `check_tor`

Requires `curl`, `awk` and `bc` to be installed.

```
check_tor usage: ./check_tor [OPTION ...]

check_tor - check wether TOR connection is alive

Examples:
  ./check_tor -a 127.0.0.8 -p 9051 -u "https://www.torproject.org" # check TOR SocksPort at "127.0.0.8:9051", using https://www.torproject.org to test connectivity.
  ./check_tor -w 10 -c 30                                          # check default TOR SocksPort using default URL with Warning threshold of 10s, Crticial threshold of 30s.

Per default, the SocksPort at "127.0.0.1:9050" will be used to call "https://www.veruscoin.io". Latency >5s is WARN, >10s is CRIT.

Options:
    -a <address>    Address of TOR SocksPort
    -p <port>       Port of TOR SocksPort
    -u <url>        URL to test TOR connectivity with
    -w <warn>       Warning threshold for latency
    -c <crit>       Critical threshold for latency
```

## `check_s-nomp`

Requires `curl`, `bc`, `awk`, `grep` and `jq` to be installed.

```
check_s-nomp usage: ./check_s-nomp [OPTION ...]

check_s-nomp - check wether S-NOMP pool is alive

Examples:
  ./check_s-nomp -a 127.0.0.8 -p 8090 -u "/manual" -n "cmake_minim" # check S-NOMP at "127.0.0.8:8090", searching "/manual" for string "cmake_minim"

Per default, "127.0.0.1:8080" will be used to get "/stats" and find the string "Blocks".

Options:
    -a <address>    Address of S-NOMP HTTP Interface
    -p <port>       Port of S-NOMP HTTP Interface
    -s              Enable HTTPS
    -u <url>        URL to get
    -n <needle>     String to search for
```

### `check_iquidus_height`

Requires `curl`, `bc`, `awk` and obviously `MongoDB` to be installed.

```
check_iquidus_height usage: ./check_iquidus_height [OPTION ...]

check_iquidus_height - Compare Iquidus MongoDB height against Wallet height

Examples:
  check_iquidus_height -d explorerdb-foobar -w "/home/bitcoin/bin/bitcoin-cli getblockcount" # Use "explorerdb-foobar" and compare against "bitcoin-cli getblockcount"


Options:
    -d <database>              Specify MongoDB database to use
    -w <wallet_height_command> Command to get wallet height with
    -W <warn_difference>       WARN threshold (default: 2)
    -C <crit_difference>       CRIT threshold (default: 5)
```

## `check_dns_rr`

Requires `awk` and `host` to be installed.

```
check_dns_rr usage: ./check_dns_rr [OPTION ...]

check_dns_rr - checks a DNS round-robin setup for active hosts

Examples:

Check 1.2.3.4, 2.3.4.5, 3.4.5.6 against "www.acme.com", warn at 2 active, crit at 1 active.
  ./check_dns_rr -r www.acme.com -s 8.8.8.8 -1 1.2.3.4,web1 -2 2.3.4.5,web2 -3 3.4.5.6,web3 -w 2 -c 1

Options:
    -r <record>         DNS record to resolve
    -t <record_type>    Record type to check for (defaults to A)
    -s <dns_server>     DNS server to talk to
    -w <warn_threshold> Warn with equal to/less than this amount of active servers
    -c <crit_thresold>  Crit with equal to/less than this amount of active servers
    -0 .. -9 <ip:name>  IP/name double (up to 10 in total)

```

## `check_height_vrsc`

Requires `bc`, `curl`, `jq`, `tr` and obviously `verus` to be installed

```
check_height_vrsc usage: ./check_height_vrsc

check_height_vrsc - Compare Local Verusd daemon block height against external explorer height

Examples:
  check_height_vrsc
```

## `check_hash_vrsc`

Requires `curl`, `jq` and obviously `verus` to be installed

```
check_hash_vrsc usage: ./check_hash_vrsc

check_hash_vrsc - Compare Local Verusd daemon most recent block hash against external explorer hash for that block.

Examples:
  check_hash_vrsc
```

## `check_deprecation_vrsc`

Requires `bc`, `jq`, `tr` and obviously `verus` to be installed

```
check_deprecation_vrsc usage: ./check_deprecation_vrsc

check_deprecation_vrsc - Compare Local Verusd daemon block height against the deprecation height of the running daemon.

Examples:
  check_deprecation_vrsc
```

## `check_version_vrsc`

Requires `awk`, `curl`, `jq` and obviously `verus` to be installed

```
check_version_vrsc usage: ./check_version_vrsc

check_version_vrsc - Compare Local Verusd daemon version against the latest released version on GitHub.

Examples:
  check_version_vrsc
```

## `check_VerusID`

Requires `bc`, `curl`, `jq` and obviously `verus` to be installed

```
check_VerusID usage: ./check_VerusID [OPTION ...]

check_VerusID - Check the vault status of the VerusID

Options:
    -id <i-address || ID@>     Mandatory: existing VerusID to check the status for.
    -s <blocks>                Optional: amount of blocks that is considered safe between current block height and unlock block height.
                                        if omitted, the default value of 1440 blocks is being used.

Examples:

Check Oink@ and consider unlock height more than 1 day in the future (1440 blocks) to be safe:
  check_version_vrsc -id Oink@ -s 1440
```
