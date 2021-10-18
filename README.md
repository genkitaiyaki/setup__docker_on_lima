# setup__docker_on_lima

私用のmacbookにlimaを導入したときのメモ

## limaとは？

- macOS上で動くLinuxの仮想マシン。
- Windowsで言うWSLみたいなもの。多分。
- 詳しくは公式のリポジトリ参照。

> <https://github.com/lima-vm/lima>

## なぜlimaが必要？

- 今までコンテナ環境使って開発するときは、Docker for Desktopを使っていた。
- ある日一部の利用者はお金がかかるようになってしまった。

> <https://www.docker.com/legal/docker-subscription-service-agreement>

- お金かかる条件は妥当だと思うし、自分の使い方だとお金払わなくてもいいと思う。
- ただ、代替手段は知ってた方がいい気がしたんで、limaを入れてdockerを動かしてみることにした。

## limaの導入

- Homebrewを使ってlimaとdockerを入れる

```bash
❯ brew install lima docker
```

## dockerの起動

- 公式からdockerの設定ファイルを拝借しdockerを起動
  - > <https://github.com/lima-vm/lima/blob/15aba00bbb85e64c3e17ea3611b39b6503727064/examples/docker.yaml>
  - 基本的に流用しているがdiskサイズは20GBに変えた

```bash
❯ limactl start ./docker.yaml
```

- 以下の出力が出ればOK

```bash
❯ INFO[0375] READY. Run `limactl shell docker` to open the shell. 
```

## 環境変数の設定

- docker.yamlに書かれているヒントをもとに環境変数の設定をしていく

> Hint: To allow `docker` CLI on the host to connect to the Docker daemon running inside the guest,
> add `NoHostAuthenticationForLocalhost yes` in ~/.ssh/config , and then run the following commands:

- DOCKER_HOSTを書き換える

```bash
❯ export DOCKER_HOST=ssh://docker_localhost:60006
```

- ssh/configにNoHostAuthenticationForLocalhost yesを書き足す
- macosの場合ローカルホスト名は「xxx.local」の方だから注意
  - sshの公開鍵でホスト名確認するのが一番確実

```bash
❯ vi ~/.ssh/config
```

```TEXT
Host docker_localhost
  HostName {PCのローカルホスト名}
  User {PCにログインしているユーザ名}
  NoHostAuthenticationForLocalhost yes
  Port 60006
```

- ちなみに起動時に~/.ssh/*.pubを読み込むらしい
- ここに気づかず大分時間を無駄にしてしまった、、

```bash
  # Load ~/.ssh/*.pub in addition to $LIMA_HOME/_config/user.pub , for allowing DOCKER_HOST=ssh:// .
  # This option is enabled by default.
  # If you have an insecure key under ~/.ssh, do not use this option.
```

## 動作確認

- 特にエラーなくバージョン情報が出れば成功

```bash
❯ docker version
```
