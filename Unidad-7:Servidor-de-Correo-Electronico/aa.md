
```
iptables -t nat -A PREROUTING -i ens3 -p tcp --dport 25 -j DNAT --to 192.168.0.3
```