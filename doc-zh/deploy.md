# blocksout 部署文档

> - 参考文档1： https://mhxw.life/2021-10-05-blockscout-deployment-guide/
> - 参考文档2：https://www.cnblogs.com/dahuige/p/15524428.html


环境要求(特别注意版本！否则后面会报错)

- 系统：CentOS7
- Erlang/OTP 24 (版本24)
- Elixir 1.13.3
- Postgres 14 
- postgresql14-contrib (必须装，否则后面报错)
- Node.js 14.x.x 
- Rust & Cargo （按照官方安装脚本安装最新版本即可）
- gcc/g++/make/automake/libtool/...按照参考文档2安装即可


先设置必要的环境换变量，在运行mix相关命令

示例环境变量: [example-bashrc](./example-bashrc)

主要的命令如下：

```shell
mix do deps.get
mix do local.rebar --force
mix do deps.compile
mix do compile
```

下载依赖的时候，如果很慢或超时，可以用国内的镜像。

要保证github能够正常访问。

如果能用了代理，最好直接用官方镜像。

```
# 国内镜像
mix hex.config mirror_url https://hexpm.upyun.com

# 官方镜像
mix hex.config mirror_url https://repo.hex.pm
```

postgresql的用户和数据库blockscout，也设置postgres拥有所有权限。


-----

geth节点启动的参数：

```
nohup geth --gcmode=archive --syncmode=full --datadir /data/node3/data --ipcdisable --http --allow-insecure-unlock --http.addr "0.0.0.0" --unlock 0xc20001631404c81d19e0b5b5aa4d30d45ed793d0 --password /data/node3/data/password.txt --ws --ws.addr "0.0.0.0" --http.api debug,net,eth,shh,web3,txpool --ws.api "eth,net,web3,network,debug,txpool" --mine --bootnodes enode://2aa08cb22a14e1cb357a5638735f5c6c5591c5d3622b8a0c432c8c3f9633c0b9d4da44d0fa281ca0b1149bbd7809f8022b0305309902416db651e54ba0682be4@172.16.100.101:0?discport=30301 >> /data/geth.log 2>&1 &

```

其中, 特别注意以下几项，必须打开：

- `--gcmode=archive`
- `--syncmode=full`
- `--http.api debug,net,eth,shh,web3,txpool`
- `--ws.api "eth,net,web3,network,debug,txpool"`


---


生产环境，
- 加一个nginx代理到blocksout服务
- 对外使用https


