# otus-vpn
Я собрал небольшой стенд из методички, дополнив его ансиблом. Единственная проблема оказалась в том, что при запуске vagrant up, собирался образ сервера и запускася provision, но не собирайлся клиент, пришлось сначала создать две ВМ, а затем запустить provision.
По итогу создаются две машины, у которых уже установлены пакеты в т.ч. openvpn.

На openvpn сервере запустил iperf3 в режиме сервера

На openvpn клиенте запустил iperf3 в режиме клиента и замерил скорость в туннеле

<details>
  <summary>Сервер tap</summary>
  
```
[root@server system]# iperf3 -s &
[1] 24831
[root@server system]# -----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
Accepted connection from 10.10.10.2, port 42598
[  5] local 10.10.10.1 port 5201 connected to 10.10.10.2 port 42600
[ ID] Interval           Transfer     Bandwidth
[  5]   0.00-1.00   sec  20.9 MBytes   176 Mbits/sec                  
[  5]   1.00-2.00   sec  22.5 MBytes   189 Mbits/sec                  
[  5]   2.00-3.00   sec  22.1 MBytes   185 Mbits/sec                  
[  5]   3.00-4.00   sec  21.9 MBytes   184 Mbits/sec                  
[  5]   4.00-5.01   sec  24.0 MBytes   199 Mbits/sec                  
[  5]   5.01-6.00   sec  22.5 MBytes   191 Mbits/sec                  
[  5]   6.00-7.00   sec  22.1 MBytes   186 Mbits/sec                  
[  5]   7.00-8.01   sec  24.2 MBytes   201 Mbits/sec                  
[  5]   8.01-9.00   sec  24.6 MBytes   208 Mbits/sec                  
[  5]   9.00-10.00  sec  23.4 MBytes   196 Mbits/sec                  
[  5]  10.00-11.00  sec  23.1 MBytes   194 Mbits/sec                  
[  5]  11.00-12.00  sec  22.8 MBytes   191 Mbits/sec                  
[  5]  11.00-12.00  sec  22.8 MBytes   191 Mbits/sec                  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth
[  5]   0.00-12.00  sec  0.00 Bytes  0.00 bits/sec                  sender
[  5]   0.00-12.00  sec   293 MBytes   205 Mbits/sec                  receiver
iperf3: the client has terminated
```
</details>

<details>
  <summary>Клиент tap</summary>
  
```
[root@client system]# iperf3 -c 10.10.10.1 -t 40 -i 5
Connecting to host 10.10.10.1, port 5201
[  4] local 10.10.10.2 port 42600 connected to 10.10.10.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-5.00   sec   123 MBytes   207 Mbits/sec   74    356 KBytes       
[  4]   5.00-10.00  sec   127 MBytes   213 Mbits/sec   22    481 KBytes       
^C[  4]  10.00-11.83  sec  44.2 MBytes   202 Mbits/sec    0    541 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-11.83  sec   294 MBytes   209 Mbits/sec   96             sender
[  4]   0.00-11.83  sec  0.00 Bytes  0.00 bits/sec                  receiver
iperf3: interrupt - the client has terminated
```
</details>

Следующим этапом меняем в директиве dev tap на tun на обоих серверах и замеряем скорость снова

<details>
  <summary>Сервер tun</summary>
  
```
Accepted connection from 10.10.10.2, port 42602
[  5] local 10.10.10.1 port 5201 connected to 10.10.10.2 port 42604
[ ID] Interval           Transfer     Bandwidth
[  5]   0.00-1.00   sec  18.8 MBytes   157 Mbits/sec                  
[  5]   1.00-2.01   sec  22.4 MBytes   187 Mbits/sec                  
[  5]   2.01-3.00   sec  23.0 MBytes   194 Mbits/sec                  
[  5]   3.00-4.00   sec  24.4 MBytes   204 Mbits/sec                  
[  5]   4.00-5.00   sec  25.0 MBytes   210 Mbits/sec                  
[  5]   5.00-6.00   sec  22.3 MBytes   187 Mbits/sec                  
[  5]   6.00-7.00   sec  23.4 MBytes   197 Mbits/sec                  
[  5]   7.00-8.01   sec  23.1 MBytes   192 Mbits/sec                  
[  5]   8.01-9.00   sec  22.2 MBytes   188 Mbits/sec                  
[  5]   9.00-10.01  sec  23.5 MBytes   195 Mbits/sec                  
^C
[2]+  Exit 1                  iperf3 -s
[root@server openvpn]# [  5]  10.01-11.00  sec  23.9 MBytes   202 Mbits/sec                  
[  5]  11.00-12.00  sec  22.8 MBytes   191 Mbits/sec                  
[  5]  12.00-13.01  sec  25.0 MBytes   208 Mbits/sec                  
[  5]  13.01-14.00  sec  23.6 MBytes   200 Mbits/sec
```
</details>

<details>
  <summary>Клиент tun</summary>

```
[root@client openvpn]# iperf3 -c 10.10.10.1 -t 40 -i 5
Connecting to host 10.10.10.1, port 5201
[  4] local 10.10.10.2 port 42604 connected to 10.10.10.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-5.00   sec   116 MBytes   194 Mbits/sec   98    262 KBytes       
[  4]   5.00-10.01  sec   115 MBytes   193 Mbits/sec   85    325 KBytes       
[  4]  10.01-15.00  sec   118 MBytes   198 Mbits/sec   34    457 KBytes       
[  4]  15.00-20.00  sec   117 MBytes   197 Mbits/sec   23    377 KBytes       
[  4]  20.00-25.00  sec   117 MBytes   196 Mbits/sec    0    558 KBytes       
^C[  4]  25.00-27.37  sec  55.8 MBytes   197 Mbits/sec    0    625 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-27.37  sec   639 MBytes   196 Mbits/sec  240             sender
[  4]   0.00-27.37  sec  0.00 Bytes  0.00 bits/sec                  receiver
iperf3: interrupt - the client has terminated
```
</details>
Как мне это видится, режим tap чуть шустрее из-за более высокой пропускной способности.

### RAS на базе OpenVPN

```
~/Linux-2022-12/otus-vpn# vagrant up
```
Попытка пингануть внутренний IP адрес сервера в туннеле:

```
~/Linux-2022-12/otus-vpn# ping -c 4 10.10.10.1
PING 10.10.10.1 (10.10.10.1) 56(84) bytes of data.
^C
--- 10.10.10.1 ping statistics ---
2 packets transmitted, 0 received, 100% packet loss, time 1005ms
```
сервер не отвечает.

После этого на хосте выполняем

```
~/Linux-2022-12/otus-vpn# openvpn --config client.conf
```
и повторяем попытку

```
~/Linux-2022-12/otus-vpn# ping -c 4 10.10.10.1
PING 10.10.10.1 (10.10.10.1) 56(84) bytes of data.
64 bytes from 10.10.10.1: icmp_seq=1 ttl=64 time=0.502 ms
64 bytes from 10.10.10.1: icmp_seq=2 ttl=64 time=1.33 ms
64 bytes from 10.10.10.1: icmp_seq=3 ttl=64 time=1.21 ms
64 bytes from 10.10.10.1: icmp_seq=4 ttl=64 time=1.34 ms
--- 10.10.10.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3018ms
rtt min/avg/max/mdev = 0.502/1.097/1.344/0.347 ms
```
Также проверяем командой ip r (netstat -rn) на хостовой машине
что сеть туннеля импортирована в таблицу маршрутизации.

```
~/Linux-2022-12/otus-vpn# netstat -rn
Destination Gateway Genmask Flags MSS Window irtt Iface
0.0.0.0         192.168.1.1     0.0.0.0         UG        0 0          0 wlp0s20f3
10.10.10.0      10.10.10.5      255.255.255.0   UG        0 0          0 tun0
10.10.10.5      0.0.0.0         255.255.255.255 UH        0 0          0 tun0
169.254.0.0     0.0.0.0         255.255.0.0     U         0 0          0 wlp0s20f3
172.17.0.0      0.0.0.0         255.255.0.0     U         0 0          0 docker0
192.168.1.0     0.0.0.0         255.255.255.0   U         0 0          0 wlp0s20f3
192.168.20.0    0.0.0.0         255.255.255.0   U         0 0          0 vboxnet5
192.168.30.0    0.0.0.0         255.255.255.0   U         0 0          0 vboxnet4
192.168.40.0    0.0.0.0         255.255.255.0   U         0 0          0 vboxnet3
192.168.50.0    0.0.0.0         255.255.255.0   U         0 0          0 vboxnet2
192.168.56.0    0.0.0.0         255.255.255.0   U         0 0          0 vboxnet0
```

