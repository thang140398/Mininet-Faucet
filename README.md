# Mininet-Faucet

*Mininet 2.2.2*

*Faucet 1.9.47*

### Giới thiệu

### Cài đặt mininet
```
sudo apt -get install mininet
```
Tạo file mntest.py
```
from mininet.topo import Topo

class MyTopo( Topo ):
    "Simple topology example."

    def __init__( self ):
        "Create custom topo."

        # Initialize topology
        Topo.__init__( self )

 		# Add hosts and switches
        leftHost = self.addHost( 'h1' )
        rightHost = self.addHost( 'h2' )
        Switch = self.addSwitch( 's3' )
     
        # Add links
        self.addLink( leftHost, Switch )
        self.addLink( Switch, rightHost )



topos = { 'mytopo': ( lambda: MyTopo() ) }
```

```
#!/usr/bin/python

from mininet.net import Mininet
from mininet.node import Controller, RemoteController, OVSController
from mininet.node import CPULimitedHost, Host, Node
from mininet.node import OVSKernelSwitch, UserSwitch
from mininet.node import IVSSwitch
from mininet.cli import CLI
from mininet.log import setLogLevel, info
from mininet.link import TCLink, Intf
from subprocess import call

def myNetwork():

    net = Mininet( topo=None,
                   build=False,
                   ipBase='10.0.0.0/8')

    info( '*** Adding controller\n' )
    c0=net.addController(name='c0',
                      controller=RemoteController,
                      ip='127.0.0.1',
                      protocol='tcp',
                      port=6653)

    info( '*** Add switches\n')
    s5 = net.addSwitch('s5', cls=OVSKernelSwitch)

    info( '*** Add hosts\n')
    h2 = net.addHost('h2', cls=Host, ip='10.0.0.2', defaultRoute=None)
    h1 = net.addHost('h1', cls=Host, ip='10.0.0.1', defaultRoute=None)

    info( '*** Add links\n')
    net.addLink(h1, s5)
    net.addLink(h2, s5)

    info( '*** Starting network\n')
    net.build()
    info( '*** Starting controllers\n')
    for controller in net.controllers:
        controller.start()

    info( '*** Starting switches\n')
    net.get('s5').start([c0])

    info( '*** Post configure switches and hosts\n')

    CLI(net)
    net.stop()

if __name__ == '__main__':
    setLogLevel( 'info' )
    myNetwork()

```


### Cài đặt faucet

##### 1. Cài đặt các gói cần thiết
```
sudo apt-get install curl gnupg apt-transport-https lsb-release
echo "deb https://packagecloud.io/faucetsdn/faucet/$(lsb_release -si | awk '{print tolower($0)}')/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/faucet.list
curl -L https://packagecloud.io/faucetsdn/faucet/gpgkey | sudo apt-key add -
sudo apt-get update
```
##### 2. Cài faucet-all-in-one
```
sudo apt-get install faucet-all-in-one
```

##### 3. Kiểm tra cấu hình faucet

File `/etc/faucet/faucet.yaml`

Dùng lệnh
```
check_faucet_config /etc/faucet/faucet.yaml
```
Nếu thay đổi cấu hình thì reload faucet
```
sudo systemctl reload faucet
```

##### 4. Kiểm tra log
Vị trí file `/var/log/faucet/faucet.log`
![alt](https://github.com/thang140398/Mininet-Faucet/blob/master/Picture%20for%20REAME.md/Screenshot%20from%202020-08-02%2001-51-15.png)

### Chạy mininet với controller là faucet (Dùng custom topo mntest)
```
sudo mn --controller=remote,ip=127.0.0.1,port=6653 --custom mntest.py --topo mytopo
```

![alt](https://github.com/thang140398/Mininet-Faucet/blob/master/Picture%20for%20REAME.md/Screenshot%20from%202020-08-02%2001-56-19.png)

ping để kiểm tra
`
pingall
`

![alt](https://github.com/thang140398/Mininet-Faucet/blob/master/Picture%20for%20REAME.md/Screenshot%20from%202020-08-02%2001-57-22.png)

### Chạy mininet với controller là faucet
```
sudo mn --controller=remote
```
Thử tắt faucet rồi mới chạy lệnh, sau đó pingall

![alt](https://github.com/thang140398/Mininet-Faucet/blob/master/Picture%20for%20REAME.md/Screenshot%20from%202020-08-02%2003-11-56.png)

![alt](https://github.com/thang140398/Mininet-Faucet/blob/master/Picture%20for%20REAME.md/Screenshot%20from%202020-08-02%2003-12-22.png)

Bật faucet lên rồi pingall

![alt](https://github.com/thang140398/Mininet-Faucet/blob/master/Picture%20for%20REAME.md/Screenshot%20from%202020-08-02%2003-12-41.png)

![alt](https://github.com/thang140398/Mininet-Faucet/blob/master/Picture%20for%20REAME.md/Screenshot%20from%202020-08-02%2003-12-56.png)


