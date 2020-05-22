# NVIDIA Container Toolkit を docker-compose で使う(workaround)

## 背景

- Docker本体がGPU利用に対応するようになった
- それに合わせてnvidia-docker2 → NVIDIA Container Toolkit へのアップデートで、Docker本体のGPU機能を利用するようにアーキテクチャが変更された
- Docker自体の利用はそれで問題ないが、docker-composeがいつまで経っても追随しないので素直に 
Docker / NVIDIA Container Toolkit をインストールするだけだと docker-compose が走らない

### 現状

- docker-compose v3 系統は修正 pull requst のマージ待ち
  - 最近マージされたっぽい。次期リリースでは素直に v3 系統で使えるかも
- v2 系統では下記のような docker-compose.yml でGPU利用できる

```yaml
version: '2.3'

services:
  deep:
    container_name: deep
    image: tackman/deeplearning
    runtime: nvidia
    volumes:
      - /host/directory:/container/directory
    stdin_open: true
    tty: true
    ports:
      - "6006:6006"
```

ただし、普通にインストールしただけだと下記のようなエラーになる

```sh
takuma@ran:~/cvproj$ docker-compose up -d                                                                               
Creating network "cvproj_default" with the default driver
Creating deep ... error

ERROR: for deep  Cannot create container for service deep: Unknown runtime specified nvidia

ERROR: for deep  Cannot create container for service deep: Unknown runtime specified nvidia
ERROR: Encountered errors while bringing up the project.                                      
```

## 対策

通常のインストールに加えて、 /etc/docker/daemon.json を追加。デフォルトのランタイムをnvidiaにしてしまう

```json
{
   "default-runtime":"nvidia",
   "runtimes":{
      "nvidia":{
         "path":"/usr/bin/nvidia-container-runtime",
         "runtimeArgs":[ ]
      }      
    }
  }
}
```

```sh
sudo systemctl restart docker
```

source: https://github.com/docker/compose/issues/6691

デフォオルトランタイムを書き換えてしまうという乱暴な方法なので、言うまでもなくまともな方法ではない。
docker-compose 将来のバージョンで、v3系統 docker-compose.yml でGPU利用オプションを素直に利用できるようになるまでのつなぎ。
