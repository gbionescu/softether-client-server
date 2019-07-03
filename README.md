# Create a VPN network using Docker based SoftEther containers

TLDR: This repository contains scripts that create systemd services which wrap the following commands:

```
# Create server container
docker run -d \
    --privileged \
    --cap-add NET_ADMIN \
    -p 4434:443/tcp \
    -e USERS=soft:ether \
    -e SPW=test \
    -e HPW=test \
    siomiz/softethervpn


# Create client container
docker run -d \
    --net=host --privileged \
    -e VPN_SERVER=my.server.com \
    -e VPN_PORT=4434 \
    -e ACCOUNT_USER=soft \
    -e ACCOUNT_PASS=ether \
    mitsutaka/softether-vpnclient

# The client container exposes an interface named vpn_default
# You can assign it an IP address
ifconfig vpn_default 192.168.20.10
```

### Server container

To install the server-side containers, clone this repository and run:

`./install-server`

The following parameters can be customized:

```
  --port PORT           What port to use to accept incoming connections
                        (default: 4434)
  --users USERS         Define what users should be accepted. Specify as
                        user1:password1;user2:password2 (default: soft:ether)
  --server_pass SERVER_PASS
                        Server management password (default: softether)
  --hub_pass HUB_PASS   DEFAULT hub password (default: softether)
  --server_ip SERVER_IP
                        IP Address to assign to the server inside the VPN
                        network (default: 192.168.8.1)
```

Running this will result in creating two systemd services which will (1) start the VPN server and (2) star the VPN client.


### Client container

To install the client-side container, clone this repository and run:

`./install-client --server_ip <my.server.com> --client_ip 192.168.20.99`

The following parameters can be customized:

```
  --server_ip SERVER_IP
                        VPN Server IP Address (default: None)
  --port PORT           VPN Server port (default: 4434)
  --user USER           Username used to authenticate (default: soft)
  --pass PASS           Password to use to authenticate (default: ether)
  --client_ip CLIENT_IP
                        What IP address to assign to this client after
                        connecting (default: None)
```

Running this will result in creating one systemd service which will connect to the VPN service.
