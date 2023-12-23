# IPTables_cheatsheet

3 kind of rules: 
- Input: traffic enters our system
- Output: traffic leaves our system
- Forward: traffic enters our system and is resent somewhere else

iptables commands need to be run with root privileges

```bash
# Manual
man iptables

# Flush (remove) all rules
iptables -F

# Flush rules from the filter and NAT tables
iptables -t filter -F
iptables -t nat -F

# Restart counters
iptables -Z

# Same for NAT packages
iptables -t nat -Z

# Deny by default and enable specific (whitelisting)
iptables -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP

# Accept by default and you can disable specific (blacklisting)
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT

# Show rules (-n to show IP and port instead of name, -v for verbose output)
iptables -L -nv --line-numbers

# Show rules in the same format in which they were defined
iptables -S

# Append rule at the end of the rule list (last to be evaluated)
iptables -A <chain> <-p protocol> <-s source_IP> <-d destination_IP> <-i input_interface> <-o output interface> <-j ACCEPT|DROP>

# Insert rule at the beginning of the rule list (last to be evaluated)
iptables -I <chain> <-p protocol> <-s source_IP> <-d destination_IP> <-i input_interface> <-o output interface> <-j ACCEPT|DROP>

# If a rule is enabled, both the input and corresponding output need to be enabled, otherwise we can send traffic but not receive it. 
# Example to accept incoming and outgoing ICMP traffic to the enp0s3 interface. 
iptables -A OUTPUT -o enp0s3 -p icmp -j ACCEPT
iptables -A INPUT -i enp0s3 -p icmp -j ACCEPT

# Accept DNS traffic (with this, ping works with domain names, not only IPs). Pay attention to --dport and --sport for source and destination ports
iptables -A OUTPUT -o enp0s3 -p tcp --dport 53 -j ACCEPT
iptables -A INPUT -i enp0s3 -p tcp --sport 53 -j ACCEPT
iptables -A OUTPUT -o enp0s3 -p udp --dport 53 -j ACCEPT
iptables -A INPUT -i enp0s3 -p udp --sport 53 -j ACCEPT

# The list of rules is checked one by one. If no rule matches, the default policy is applied, but if any matches, that one is applied

# Rules hava a number (first column of `iptables -L`). A specific rule (number 2) can be removed
iptables -D OUTPUT 2

# systemd-resolved is a systemd service that provides network name resolution to local applications via a D-Bus interface (/etc/resolv.conf file).
# To be able to use it, accept all traffic in the loopback interface
iptables -I INPUT 1 -l lo -j ACCEPT
iptables -I OUTPUT 1 -o lo -j ACCEPT

# Enable HTTP traffic
iptables -A OUTPUT -o enp0s3 -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -i enp0s3 -p tcp --sport 80 -j ACCEPT

# Enable HTTPS traffic
iptables -A OUTPUT -o enp0s3 -p tcp --dport 443 -j ACCEPT
iptables -A INPUT -i enp0s3 -p tcp --sport 443 -j ACCEPT

```
