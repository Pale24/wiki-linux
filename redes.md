# Protocolo TCP/IP

## Modelo de capas:
Superior: cercana al usuario

- 4 Aplicación.	**L7 OSI** 	Dato
- 3 Transporte.	**L4 OSI**	Segmento
- 2 Red.		**L3 OSI**	Paquete. Direccionamiento logico, enrutamiento
- 1 Enlace. 	**L2 OSI** 	Trama. Direccionamiento fisico. 

Inferior: Hardware


# Comandos de redes con iproute2:

## Listado de interfaces de red:
```
ip address
ip addr
ip a
```

## Analizar la información de nuestra tabla de enrutamiento:
```
ip route # tambien 'ip ro' o 'ip r' 

default via 192.168.0.1 dev wlp3s0 proto dhcp metric 600
169.254.0.0/16 dev wlp3s0 scope link metric 1000
192.168.0.0/24 dev wlp3s0 proto kernel scope link src 192.168.0.6 metric 600
```

## Agregar IP estatica:
```
ip a add 10.0.1.2/24 dev enp0s3 brd +
```

## Levantar interfaz de red
```
ip li set dev enp0s3 up
```

## Agregar puerta de enlace:
```
ip r add default via 10.0.1.1/24 dev enp0s3
```

## Agregar una red remota 20.0.0.0/24 gateway: 192.168.10.254 

- El gateway tiene que ser una dirección que pertenezca a uno de los segmentos locales. De modo que, para poder alcanzar una red que no es local a la nuestra, vamos a mandar todo el trafico destinado a esa red a una puerta de enlace que si es local 
- Vamos a asociarla al dispositivo que tiene la ip de la puerta de enlace

```
ip r add 20.0.0.0/24 via 192.168.10.20 dev eth1 # eth1 tiene la IP 192.168.10.20

```

## Eliminar información de la tabla de enrutamiento 
```
ip r del 20.0.0.0/24
```

## Eliminar información de dirección de red
```
ip a del 192.168.10.20/24 dev eth1
```

## Eliminar toda configuración de red de un dispositivo
```
ip a flush dev eth1
```
## Ver tabla de ARP
Tabla de ARP: Asocia las direcciones IP con las que nosotros hemos interactuado localmente con su dirección MAC de forma de no tener que resolver esta relación cada vez que nos queremos volver a conectar con ese dispositivo

## [DNS](DNS.md)
## [[nmap]]
## [[dhcp-dns]]
## [[LDAP]]

```
ip neigh

192.168.0.1 dev wlp3s0 lladdr 24:7f:20:05:41:fc REACHABLE
192.168.0.3 dev wlp3s0 lladdr d4:c9:4b:9d:3b:32 REACHABLE
192.168.0.4 dev wlp3s0 lladdr 14:ab:c5:c4:ca:43 REACHABLE
fe80::267f:20ff:fe05:41fc dev wlp3s0 lladdr 24:7f:20:05:41:fc router REACHABLE
```
## TCPDUMP: Sniffer
```
tcpdump -i enp0s3			# vemos todo el trafico que pasa por el dispositivo 
# Por cada linea nos muestra el tipo de trafico en particular, dirección de origen, puerto de salida, dirección de destino.
# Tiempo de captura Protocolo 
# > indica dirección
# 
listening on wlp3s0, link-type EN10MB (Ethernet), capture size 262144 bytes
08:14:56.734067 IP6 FitzRoy.54642 > 2800:810:201::12.https: Flags [.], ack 1838822104, win 10919, options [nop,nop,TS val 671361665 ecr 1689570045], length 0
08:14:56.743326 IP6 2800:810:201::12.https > FitzRoy.54642: Flags [.], ack 1, win 483, options [nop,nop,TS val 1689580285 ecr 671341241], length 0
08:14:56.962636 IP FitzRoy.41278 > 100.72.3.5.domain: 53374+ [1au] A? calendar.google.com. (48)
08:14:56.962825 IP FitzRoy.44886 > 100.72.3.5.domain: 55278+ [1au] AAAA? calendar.google.com. (48)
08:14:56.973055 IP 100.72.3.5.domain > FitzRoy.41278: 53374 1/0/1 A 172.217.30.238 (64)
```
```
tcpdump -i enp0s3 -nn 		# vemos todo el trafico que pasa por enp0s3 sin resolución de nombres y de puertos
# Se puede ver información adicional que tiene que ver con el protocolo TCP (cabecera IP, etc)
10:37:14.046574 IP 192.168.0.6.60720 > 35.186.224.47.443: Flags [P.], seq 1436539499:1436539534, ack 2503159669, win 501, options [nop,nop,TS val 2182458866 ecr 3756495893], length 35
10:37:14.060391 IP 35.186.224.47.443 > 192.168.0.6.60720: Flags [.], ack 35, win 266, options [nop,nop,TS val 3756526748 ecr 2182458866], length 0
10:37:14.222966 IP 35.186.224.47.443 > 192.168.0.6.60720: Flags [P.], seq 1:32, ack 35, win 266, options [nop,nop,TS val 3756526912 ecr 2182458866], length 31
10:37:14.223005 IP 192.168.0.6.60720 > 35.186.224.47.443: Flags [.], ack 32, win 501, options [nop,nop,TS val 2182459042 ecr 3756526912], length 0


tcpdump -i enp0s3 -nn -c 3 # captura 3 tramas
```
```
tcpdump -nni enp0s3 -v		# Información mas extendida
tcpdump -nni enp0s3 -vv		# Vemos solo el trafico icmp

10:43:20.251490 IP6 (flowlabel 0x56119, hlim 64, next-header TCP (6) payload length: 71) 2800:810:4fd:8456:d08f:b88:f947:243d.58622 > 2800:3f0:4002:802::200e.443: Flags [P.], cksum 0xa350 (correct), seq 1575284489:1575284528, ack 2899537237, win 501, options [nop,nop,TS val 3272762345 ecr 1243327691], length 39
10:43:20.268107 IP6 (flowlabel 0x0241e, hlim 59, next-header TCP (6) payload length: 32) 2800:3f0:4002:802::200e.443 > 2800:810:4fd:8456:d08f:b88:f947:243d.58622: Flags [.], cksum 0xdeb6 (correct), seq 1, ack 39, win 332, options [nop,nop,TS val 1243386023 ecr 3272762345], length 0
10:43:20.268143 IP6 (flowlabel 0x0241e, hlim 59, next-header TCP (6) payload length: 71) 2800:3f0:4002:802::200e.443 > 2800:810:4fd:8456:d08f:b88:f947:243d.58622: Flags [P.], cksum 0x4db0 (correct), seq 1:40, ack 39, win 332, options [nop,nop,TS val 1243386023 ecr 3272762345], length 39
10:43:20.310103 IP6 (flowlabel 0x56119, hlim 64, next-header TCP (6) payload length: 32) 2800:810:4fd:8456:d08f:b88:f947:243d.58622 > 2800:3f0:4002:802::200e.443: Flags [.], cksum 0xddab (correct), seq 39, ack 40, win 501, options [nop,nop,TS val 3272762404 ecr 1243386023], length 0
10:43:20.889552 IP (tos 0x0, ttl 64, id 1843, offset 0, flags [none], proto TCP (6), length 681)
```

```
tcpdump -i wlp3s0 -nn -v -c 30 -e

10:45:51.710472 f4:8c:50:6e:da:cf > 24:7f:20:05:41:fc, ethertype IPv6 (0x86dd), length 86: (flowlabel 0x60b83, hlim 64, next-header TCP (6) payload length: 32) 2800:810:4fd:8456:d08f:b88:f947:243d.57710 > 2800:810:201::12.443: Flags [.], cksum 0xba6b (correct), ack 471104572, win 12335, options [nop,nop,TS val 680416530 ecr 1698625075], length 0
10:45:51.722396 24:7f:20:05:41:fc > f4:8c:50:6e:da:cf, ethertype IPv6 (0x86dd), length 86: (class 0x20, flowlabel 0xe49b8, hlim 59, next-header TCP (6) payload length: 32) 2800:810:201::12.443 > 2800:810:4fd:8456:d08f:b88:f947:243d.57710: Flags [.], cksum 0xe90c (correct), ack 1, win 364, options [nop,nop,TS val 1698635270 ecr 680406368], length 0
10:45:52.478059 f4:8c:50:6e:da:cf > d4:c9:4b:9d:3b:32, ethertype IPv4 (0x0800), length 66: (tos 0x0, ttl 64, id 2879, offset 0, flags [DF], proto TCP (6), length 52)
    192.168.0.6.39046 > 192.168.0.3.1716: Flags [.], cksum 0x0069 (correct), ack 3169555423, win 501, options [nop,nop,TS val 2561664399 ecr 523739164], length 0
10:45:52.586435 d4:c9:4b:9d:3b:32 > f4:8c:50:6e:da:cf, ethertype IPv4 (0x0800), length 66: (tos 0x0, ttl 64, id 5468, offset 0, flags [none], proto TCP (6), length 52)
    192.168.0.3.1716 > 192.168.0.6.39046: Flags [.], cksum 0x250d (correct), ack 1, win 245, options [nop,nop,TS val 523740220 ecr 2561654218], length 0
10:45:53.484728 f4:8c:50:6e:da:cf > 24:7f:20:05:41:fc, ethertype IPv6 (0x86dd), length 198: (flowlabel 0x8c2d7, hlim 64, next-header TCP (6) payload length: 144) 2800:810:4fd:8456:d08f:b88:f947:243d.54876 > 2800:3f0:4002:80c::200e.80: Flags [P.], cksum 0x450b (correct), seq 1332642199:1332642311, ack 3080029116, win 507, options [nop,nop,TS val 2395723187 ecr 2463903612], length 112: HTTP, length: 112
	GET /generate_204 HTTP/1.1
	Host: clients3.google.com
	User-Agent: Go-http-client/1.1
	Accept-Encoding: gzip


```

```
tcpdump -i wlp3s0 -nn -v -c 30 -e -x 	# vemos el hexaadecimal del paquete

10:48:36.042076 IP (tos 0x0, ttl 64, id 15491, offset 0, flags [DF], proto TCP (6), length 87)
    192.168.0.6.60720 > 35.186.224.47.443: Flags [P.], cksum 0x3ffa (correct), seq 1436540269:1436540304, ack 2503160351, win 501, options [nop,nop,TS val 2183140830 ecr 3757177894], length 35
	0x0000:  4500 0057 3c83 4000 4006 3986 c0a8 0006
	0x0010:  23ba e02f ed30 01bb 559f dd6d 9533 321f
	0x0020:  8018 01f5 3ffa 0000 0101 080a 8220 15de
	0x0030:  dff1 fc26 1703 0300 1e00 0000 0000 0000
	0x0040:  dcf6 97b6 9681 2398 d5d6 e62f f5d5 ba07
	0x0050:  4bfc 2693 4d69 80
10:48:36.055009 IP (tos 0x0, ttl 55, id 41340, offset 0, flags [none], proto TCP (6), length 52)
    35.186.224.47.443 > 192.168.0.6.60720: Flags [.], cksum 0xdb29 (correct), ack 35, win 266, options [nop,nop,TS val 3757208721 ecr 2183140830], length 0
	0x0000:  4500 0034 a17c 0000 3706 1db0 23ba e02f
	0x0010:  c0a8 0006 01bb ed30 9533 321f 559f dd90
	0x0020:  8010 010a db29 0000 0101 080a dff2 7491
	0x0030:  8220 15de

```

```
tcpdump -i wlp3s0 -nn -v -c 30 -e -X	# con -X vemos el contenido en ASCII (texto plano si el contenido no está cifrado)

10:52:27.007680 24:7f:20:05:41:fc > f4:8c:50:6e:da:cf, ethertype IPv6 (0x86dd), length 1514: (class 0x20, flowlabel 0xe9560, hlim 59, next-header TCP (6) payload length: 1460) 2800:810:201::12.443 > 2800:810:4fd:8456:d08f:b88:f947:243d.57768: Flags [.], cksum 0xb9da (correct), seq 115669:117097, ack 1508, win 295, options [nop,nop,TS val 1699030534 ecr 680811787], length 1428
	0x0000:  620e 9560 05b4 063b 2800 0810 0201 0000  b..`...;(.......
	0x0010:  0000 0000 0000 0012 2800 0810 04fd 8456  ........(......V
	0x0020:  d08f 0b88 f947 243d 01bb e1a8 b9ff 7ede  .....G$=......~.
	0x0030:  4e85 a1c3 8010 0127 b9da 0000 0101 080a  N......'........
	0x0040:  6545 2606 2894 5d0b 5fbc 1b5c 0a2a fd6f  eE&.(.]._..\.*.o
	0x0050:  e447 7286 7314 5bd1 12d3 fb93 1118 759c  .Gr.s.[.......u.
	0x0060:  596e 8aff 279a 6337 4e3b 94df a47d 981a  Yn..'.c7N;...}..
	0x0070:  ddcb 4601 1587 7f9b c166 745c 35b2 4ee1  ..F......ft\5.N.
	0x0080:  c286 b345 13b8 ab1e 2fe8 140a 2388 a86a  ...E..../...#..j
	0x0090:  713e 7160 1cc2 bf16 3cb1 e1d0 ec6c 38b9  q>q`....<....l8.
	0x00a0:  6779 36c6 188a d392 d7a2 de33 36c2 8442  gy6........36..B
	0x00b0:  fbb8 5179 15a5 47d6 af73 dea9 ee44 061d  ..Qy..G..s...D..
	0x00c0:  814c b259 4fa9 4cf3 cfd5 a3d4 cf2e 1b8b  .L.YO.L.........
	0x00d0:  d4bd b48d 6f4b 426d 84f2 2428 16e0 4fc9  ....oKBm..$(..O.
	0x00e0:  93d7 8aa6 93b7 e118 5b1c 5ea5 561b ac84  ........[.^.V...
	0x00f0:  b2eb f4cc 2f64 0a5f 2d4f 8b11 61f9 7bec  ..../d._-O..a.{.
	0x0100:  660e 90c6 cb8d 134e 9327 1bf0 b9cb 1df4  f......N.'......
	0x0110:  5099 a3ac 7927 4a1b bb7b 3623 89a3 147b  P...y'J..{6#...{
	0x0120:  849a 9f89 7ecc c865 c44b 5259 06ed ab7f  ....~..e.KRY....
	0x0130:  e97a f1e6 e903 f5b3 2556 a95e 7731 ad1b  .z......%V.^w1..
	0x0140:  b705 a68c f9a0 2168 699a 3ea8 1ec4 4c95  ......!hi.>...L.
	0x0150:  bf25 6d98 b583 3cba 17eb 4680 417e f2d9  .%m...<...F.A~..
	0x0160:  e1f2 0079 a18d c887 a81b 1c34 174c 1703  ...y.......4.L..
	0x0170:  0340 11db f6a5 fe8a ae7b 16d2 9d71 9447  .@.......{...q.G
	0x0180:  c73e d299 947e 1843 a9c1 79a3 127d 1e2d  .>...~.C..y..}.-
	0x0190:  8105 2ad5 6034 a4ec 2004 a129 5266 c97e  ..*.`4.....)Rf.~
	0x01a0:  d39d b19a 5b1b 3e90 4202 c4ae 5533 949d  ....[.>.B...U3..
	0x01b0:  f025 7dab bbfa 36c4 5b9e 79e4 ea4d ec63  .%}...6.[.y..M.c
	0x01c0:  1447 5feb 9a1f dc11 73e3 7a16 837a a8c4  .G_.....s.z..z..
	0x01d0:  02ba a46a bb18 5b8c 2a00 5907 dd0f feb9  ...j..[.*.Y.....
	0x01e0:  62a6 8534 3402 94bd bd95 c28e a12c 705d  b..44........,p]
	0x01f0:  ce6f bd5b 63cb cc91 eac2 2904 641f 61c5  .o.[c.....).d.a.
	0x0200:  8b2a 4bcf ad59 43f0 7321 a73c 2e19 80fe  .*K..YC.s!.<....
	0x0210:  eb05 2b8f 573a 6d27 d798 7f36 0a81 22b9  ..+.W:m'...6..".
	0x0220:  7da5 b245 5af2 4958 4d9f 9caf 55c0 0112  }..EZ.IXM...U...
	0x0230:  ec29 1851 c5d5 65c1 33bf bb8d 60df d556  .).Q..e.3...`..V
	0x0240:  cfd6 e9da e86a aab4 1204 ad00 4bdb 79ec  .....j......K.y.
	0x0250:  ad60 8cfb 5999 3d29 5dfe c319 4573 9e0b  .`..Y.=)]...Es..
	0x0260:  9142 c811 0fda 550f 3156 61a7 9262 85e5  .B....U.1Va..b..
	0x0270:  5257 8752 94ff c7c2 5b28 e45c 252e 19ac  RW.R....[(.\%...
	0x0280:  877b 185c 34eb 2704 d9c7 f6e8 1172 3d7a  .{.\4.'......r=z
	0x0290:  ca97 4077 8cdc 3598 61cc 30b2 3458 a77f  ..@w..5.a.0.4X..
	0x02a0:  9af7 98c5 797e 0150 ebc0 f854 1640 d575  ....y~.P...T.@.u
	0x02b0:  abcf 3956 a852 3bed d11d 3227 c39f 2763  ..9V.R;...2'..'c
	0x02c0:  8557 2e5e 17e9 b1c3 b00c 25fd 5ed3 ab99  .W.^......%.^...
	0x02d0:  9885 ba1f 486d cf66 7bc1 7da8 a346 2848  ....Hm.f{.}..F(H
	0x02e0:  99af ffbf 784f d666 fece cd97 390b cc83  ....xO.f....9...
	0x02f0:  6e12 1007 2315 97d2 00d6 f017 1326 7dd9  n...#........&}.
	0x0300:  0c8f 2fc1 ada6 b6fe 4766 6bc8 ce36 c3e6  ../.....Gfk..6..
	0x0310:  24de 44dd 9a9e 1f30 9b9a d64c 3757 0521  $.D....0...L7W.!
	0x0320:  ffc6 29b4 4736 93ec 65e1 3de5 bf05 b663  ..).G6..e.=....c
	0x0330:  0d4f 4acd 3815 5acc 527b 4877 b1b9 090a  .OJ.8.Z.R{Hw....
	0x0340:  1147 c2fa fdf9 6971 bf10 781d 828f 3565  .G....iq..x...5e
	0x0350:  3204 089f eb37 7454 ebe2 cc6b 05da bd5d  2....7tT...k...]
	0x0360:  0695 d9d6 702a 8354 e0a2 e7ab 29ac 8f07  ....p*.T....)...
	0x0370:  ed4f d397 7420 a743 c6e9 b64f 4612 600e  .O..t..C...OF.`.
	0x0380:  d0e6 05fe ffde e825 72ee 61f3 c694 0826  .......%r.a....&
	0x0390:  b435 ea6a 5a8a 3ab2 eed3 bc1d 34c4 9b54  .5.jZ.:.....4..T
	0x03a0:  e9ec 3baa 98a5 a7ba 85e4 c0d3 3401 f4d6  ..;.........4...
	0x03b0:  99b3 42ff fcc8 b8bf 420e 54fb 430f 49df  ..B.....B.T.C.I.
	0x03c0:  ddfb 3544 4676 61e2 8e14 8478 285a 6df6  ..5DFva....x(Zm.
	0x03d0:  0a6a 57a8 d6d0 3a0f 1609 5715 cf5a 78f2  .jW...:...W..Zx.
	0x03e0:  6148 2743 ae6b 4ce8 89ed 3315 aed3 eaa2  aH'C.kL...3.....
	0x03f0:  8b2c b261 2529 c055 dea5 4d6d 5055 cbbd  .,.a%).U..MmPU..
	0x0400:  c7f9 b53a 5979 a492 3295 10d6 0ab2 68f0  ...:Yy..2.....h.
	0x0410:  67fe 39fd 0c47 8a80 e0d3 b262 08e3 c2c2  g.9..G.....b....
	0x0420:  aefa f432 1906 f432 a8ea 10aa f531 fe97  ...2...2.....1..
	0x0430:  6ed2 c87d 0886 aad3 8833 788a ff8c 90f6  n..}.....3x.....
	0x0440:  a2ce 1e3b 0c12 f0c9 8e99 049a ff4d 87ba  ...;.........M..
	0x0450:  6d39 cd6a bab6 ee20 214f dcc4 be4b faac  m9.j....!O...K..
	0x0460:  7f06 87ff c3de f4e6 9bfc d8ea bf38 bb31  .............8.1
	0x0470:  a443 0156 6fda 03fb 00d0 e628 a040 1b21  .C.Vo......(.@.!
	0x0480:  eaee f4ef 11b3 581d 818f c9d2 dfd6 32d8  ......X.......2.
	0x0490:  ebda 4f99 5971 3ef1 3c86 61d0 796f 989e  ..O.Yq>.<.a.yo..
	0x04a0:  11a1 ceb8 a19a a149 1b0e 8cbb 5520 9008  .......I....U...
	0x04b0:  11f4 b834 ed9f c146 6b36 26a5 77a1 b754  ...4...Fk6&.w..T
	0x04c0:  1dcc d98f 185a 59d9 18b1 d04d d639 9fca  .....ZY....M.9..
	0x04d0:  c57c eaf8 01e3 af50 e05c e5f9 2d23 551e  .|.....P.\..-#U.
	0x04e0:  3478 188f 18ed a34a d89e ed48 b743 06c4  4x.....J...H.C..
	0x04f0:  d899 20d4 07e7 9f02 179b fccb a99c c4d1  ................
	0x0500:  b682 9fa1 55c9 c2fe 5f0b d8a9 2997 ba4a  ....U..._...)..J
	0x0510:  bd2e 67dd f245 380a 168f e907 e8d9 b1c2  ..g..E8.........
	0x0520:  9b08 7003 e502 91e9 3b05 26c4 4dba f1e1  ..p.....;.&.M...
	0x0530:  2446 01ae a8ec ccde f49e e806 7166 bade  $F..........qf..
	0x0540:  3d84 aa82 a3c2 b8c5 41d0 ed10 7b22 df19  =.......A...{"..
	0x0550:  7652 65f6 759d 08f3 bc73 0bbb 5166 1ef0  vRe.u....s..Qf..
	0x0560:  3a69 1de4 c6b1 1d75 6566 ec88 5d08 db71  :i.....uef..]..q
	0x0570:  6930 9e3f 5e96 18eb da45 21fd e86b 15fe  i0.?^....E!..k..
	0x0580:  d4cf 6b34 7b01 eba5 e583 a01c d890 6c3a  ..k4{.........l:
	0x0590:  9dd1 5d7f 187e 431f d3e3 89cd 4488 ad84  ..]..~C.....D...
	0x05a0:  1a18 cd0c 22b4 7eac 3618 3f0a 87e4 e274  ....".~.6.?....t
	0x05b0:  b870 db4d 92b2 be08 6cd2 70b4 a61a 72bb  .p.M....l.p...r.
	0x05c0:  e861 c8b6 2d72 bf4c e0a3 1369 8bed 715d  .a..-r.L...i..q]
	0x05d0:  4866 b86f 67c4 3ac2 f44e 9c2e            Hf.og.:..N..

```
```
tcpdump -i wlp3s0 -nn -v -c 30 -e -A	# solo ASCII

10:55:32.208767 f4:8c:50:6e:da:cf > 24:7f:20:05:41:fc, ethertype IPv4 (0x0800), length 66: (tos 0x0, ttl 64, id 0, offset 0, flags [DF], proto TCP (6), length 52)
    192.168.0.6.58792 > 13.227.69.14.443: Flags [.], cksum 0xa7d4 (correct), ack 2, win 501, options [nop,nop,TS val 2839814308 ecr 2254978837], length 0
E..4..@.@.'%......E............O...........
.D ..h?.
```

```
tcpdump -i wlp3s0 -nn -v -c 30 -e -X -w /tmp/captura.pcap
```
```
tcpdump -r /tmp/captura.pcap	# Se usan las mismas opciones que para ver en la pantalla

reading from file /tmp/captura.pcap, link-type EN10MB (Ethernet)
11:42:10.627501 IP 192.108.254.120.https > FitzRoy.41032: Flags [P.], seq 510844488:510845347, ack 2401210496, win 83, options [nop,nop,TS val 2783854323 ecr 3247362158], length 859
11:42:10.627531 IP FitzRoy.41032 > 192.108.254.120.https: Flags [.], ack 859, win 493, options [nop,nop,TS val 3247363626 ecr 2783854323], length 0
11:42:10.697642 IP 192.108.254.120.https > FitzRoy.41032: Flags [P.], seq 859:1773, ack 1, win 83, options [nop,nop,TS val 2783854393 ecr 3247362158], length 914

```
### Filtros en TCPDUMP
- [not] { src | dst } {port | host | net } <valor>
- [not] portrange nn-mm
- [not] greater <bytes>
- [not] less <bytes>
- and & or
- uso de parentesis "escapados" `\(...\)`

Ejemplos:
```
tcpdump -nni eth0 port 443
tcpdump -nni eth0 src port 443 # filtra solo trafico https proveniente de ...
tcpdump -i wlp3s0 host 192.168.0.4
tcpdump -i wlp3s0 src host 192.168.0.4
tcpdump -i wlp3s0 not host 192.168.0.4
tcpdump -nni eth0 greater 1000 # filtra solo trafico mayor a 1000 bytes
tcpdump -nni eth0 less 500
tcpdump -nni eth0 greater 500 and less 1000
tcpdump -nni eth0 host 192.168.1.100 and \( port 443 or greater 800 \)
```


