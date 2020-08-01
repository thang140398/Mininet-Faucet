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


### Cài đặt faucet

##### Cài đặt các gói cần thiết
```
sudo apt-get install curl gnupg apt-transport-https lsb-release
echo "deb https://packagecloud.io/faucetsdn/faucet/$(lsb_release -si | awk '{print tolower($0)}')/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/faucet.list
curl -L https://packagecloud.io/faucetsdn/faucet/gpgkey | sudo apt-key add -
sudo apt-get update
```
##### Cài faucet-all-in-one
```
sudo apt-get install faucet-all-in-one
```

###### Kiểm tra cấu hình faucet

File `/etc/faucet/faucet.yaml`

Dùng lệnh
```
check_faucet_config /etc/faucet/faucet.yaml
```
Nếu thay đổi cấu hình thì reload faucet
```
sudo systemctl reload faucet
```

##### Kiểm tra log
Vị trí file `/var/log/faucet/faucet.log`


### Chạy mininet với controller là faucet
```
sudo mn --controller=remote,ip=127.0.0.1,port=6653 --custom mntest.py --topo mytopo
```
### ping để kiểm tra
`
pingall
`





