# Contrôle OSC

Port UDP **8000** sur toutes les unités.

## Messages

```
/mode       ,i    entier 0-5
/brightness ,f    float 0.0-1.0
```

## Broadcast multi-unités
```
IP   : 192.168.0.255
Port : 8000
```

## Max/MSP
```
[udpsend 192.168.0.255 8000]
```

## Pure Data
```
[oscformat /mode]
[netsend -u -b 192.168.0.255 8000]
```
