# filecoin

## 1. local network

### 1.1 with gengen

#### 配置文件

```javascript
{
  "keys": 5,
  "preAlloc": [
    "1000",
    "2000",
    "3000",
    "4000",
    "5000"
  ],
  "miners": [{
    "owner": 0,
    "numCommittedSectors": 1
  },{
    "owner": 1,
    "numCommittedSectors": 2
  },{
    "owner": 3,
    "numCommittedSectors": 3
  }]
}
```

#### 命令行

```text
go-filecon $ ./gengen/gengen --keypath fixtures --out-car ../localnet_configuration/genesis.car --out-json ../localnet_configuration/gen.json --config ../localnet_configuration/setup.json

./gengen/gengen --keypath ../localnet_configuration --out-car ../localnet_configuration/genesis.car --out-json ../localnet_configuration/gen.json --config ../localnet_configuration/setup.json
```

运行结果：

* gen.json
  * Keys\(privateKey, curve\), - 相当于 0.key 文件
  * Miners\(owner, address, power\)
  * GenesisCid\(/\) - 创世块
* genesis.car

说明：忘记修改keypath选项，将 0.key 等文件生成到了fixtures文件夹中

#### 启动网络

初始化网络：

```text
$ go-filecoin init --genesisfile=../localnet_configuration/genesis.car
initializing filecoin node at /home/liuyi/.filecoin

$  go-filecoin daemon
My peer ID is QmQjsmsicmn9bTG4S3pNbGEmRnYEAFJ6UdxY1WgnjGsBnh
Swarm listening on: /ip4/127.0.0.1/tcp/6000
Swarm listening on: /ip4/10.108.112.236/tcp/6000
Swarm listening on: /ip4/172.17.0.1/tcp/6000
23:32:26.323  INFO chain.stor: start loading chain at tipset: { zDPWYqFD4aWAHyyMn2WNSLYwfYNnzDePaRmVRZYxji4AsAfE9LvY }, height: 0 store.go:116
23:32:26.393  INFO chain.stor: finished loading 0 tipsets from { zDPWYqFD4aWAHyyMn2WNSLYwfYNnzDePaRmVRZYxji4AsAfE9LvY } store.go:157

```

```text
$ mineraddr=$(jq -r '.Miners[0].Address' ../localnet_configuration/gen.json)
$ peerid=$(go-filecoin --enc=json id | jq -r '.ID')
$ go-filecoin config mining.minerAddress $mineraddr
"t22kiskaqiekjaii73ezkp2ncfxto3rl67e5h5fvi"
$ walletaddr=$(go-filecoin --enc=json wallet import ../localnet_configuration/0.key | jq -r '.Addresses[0]')
$ go-filecoin config wallet.defaultAddress $walletaddr
$ go-filecoin miner update-peerid --from $walletaddr --gas-price 0.001 --gas-limit=300 $mineraddr $peerid

```



