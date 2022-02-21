vscode 远程调试



```
1.设置代理地址

go env -w GOPROXY=https://goproxy.cn,direct

2.被调试机设置环境变量 

sudo vim /etc/profile
##编辑文本首行加入如下：
export GOPATH=/root/go
export PATH=${PATH}:${GOPATH}/bin
##wq保存退出
sudo source /etc/profile
```



远程调试机 dlv 开启监听

```
dlv attach $1 --headless --api-version=2 --log --listen=:8181
```

运行->打开配置 launch.json

```
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Launch remote",
      "type": "go",
      "request": "attach",
      "mode": "remote",
      // 代码目录
      "remotePath": "/home/devops/luban",
      // dlv 监听端口
      "port": 8181,
      // 远程主机
      "host": "172.18.89.41",
      "program": "${fileDirname}",
      "showLog": true,
      "env": {}
    }
  ]
}
```

