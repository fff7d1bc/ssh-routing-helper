# ssh-routing-helper

The ssh-routing-helper script offers a straightforward solution for configuring SSH to connect to a named host via multiple hostname addresses, prioritizing the first reachable one. Designed for users who prefer a direct Ethernet connection but need the flexibility to fall back to local WiFi or a VPN like Tailscale. With this script, regardless of the network setup, an SSH connection to a specific host remains consistently accessible, automatically selecting the best available route based on the provided hosts list order. For maximum performance and lowest delay (up to timeout setting) all the hosts are checked simultaneously.

## Usage

Add symlink or entire script somewhere in `$PATH` and add into `~/.ssh/config` configuration for named host, for example
```
host myhomeserver
    ProxyCommand ssh-routing-helper --port 22 --hosts 192.168.255.5,10.16.11.5,myhomeserver.local,100.100.100.5
```

And then simply connect with ssh or anything that uses libssh (scp, sftp, lftp sftp://, rclone, sshfs) to a host `myhomeserver`. In the above example the 192.168.255.0/24 is hardwired ethernet connection, 10.16.110/24 is the local wireless network, myhomeserver.local using mDNS/avahi to discovery host on local network  and 100.100.100.5/32 being the example IP over VPN, like over Tailscale.

## Options
```
usage: ssh-routing-helper [-h] --hosts HOSTS --port PORT [--timeout TIMEOUT] [--via {socat,nc}] [--noop]

Check ports on a list of hosts and proxy to the first one that responds, or print the IP and reachable hosts if --noop is used.

options:
  -h, --help         show this help message and exit
  --hosts HOSTS      Comma-separated list of hosts to check.
  --port PORT        Port to check on the hosts.
  --timeout TIMEOUT  Timeout in seconds for each port check. Default is 1 second.
  --via {socat,nc}   Choice of tool to use for the connection (socat or nc).
  --noop             Only print the IP it would connect to and reachable hosts, do not start proxy.
```

## Requirements

- Python 3.
- Either a `socat` or `nc` in `$PATH`.
- libssh powered client that consumes `~/.ssh/config`
