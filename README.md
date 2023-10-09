- [Apache公式のDockerfile](https://github.com/docker-library/httpd/blob/242f3c62ba1ceee0a3633045fc4fd9277cb86cd3/2.4/Dockerfile)がDebian系のOSで動くように書かれているので、RHEL系に読み替えつつ、Dockerfileと起動用のスクリプトを書き換えた。
以下に記載するように、対応していないパッケージもある。


- [ Official Apache docker image](https://github.com/docker-library/httpd/blob/242f3c62ba1ceee0a3633045fc4fd9277cb86cd3/2.4/Dockerfile) runs on Debian, so I changed it to RHEL and looked at it. It almost started moving.
As mentioned above, there are some packages that are not compatible.

---

- Apacheコンテナの起動方法
  - 本プロジェクトをローカルにクローン後、`Docker_SimpleApache/docker/`にディレクトリを移動し、イメージをビルド後、コンテナ起動するとApacheコンテナがフォアグラウンドで起動する。(2023/10/9時点では、DockerHubにはPUSHしていない)

- How to start Apache container
  - After cloning this project locally, move the directory to `Docker_SimpleApache/docker/`, build the image, and start the container, the Apache container will start in the foreground. (As of October 9, 2023, it has not been pushed to DockerHub)

---

- このプロジェクトでやったこと
  - 基本的なDebian⇒RHEL対応
    - パッケージ管理コマンドをapt⇒yum化
    - パッケージ名をapt⇒yum化
    - ログの標準出力化
    - Apacheのプロセス起動
- やっていないこと
  - GPG鍵の検証
  - ハッシュ値が異なる場合のパッチ当て(最低限ソースからビルドしているので、まず動かす上では優先度が低く、今回は実施していない。今後実施予定)

- What we did in this project
  - Basic Debian⇒RHEL support
    - Change package management command from apt to yum
    - Change package name from apt to yum
    - Log standard output
    - Apache process startup
- What I haven't done
  - GPG key verification
  - Patch application when the hash values ​​are different (at least since it is built from source, it is a low priority to get it working first, so it is not implemented this time. Planned to be implemented in the future)


---


- Debian系とRHEL系でのパッケージの違いをchatgptに聞いた結果を(誤りある可能性有り)


| Debian系           | RHEL系              | 説明                                                                                                      |
|-------------------|--------------------|---------------------------------------------------------------------------------------------------------|
| ca-certificates   | ca-certificates    | SSL証明書のコレクション。両方のディストリビューションで利用可能。                                                  |
| libaprutil1-ldap  | apr-util-ldap      | Apache Portable Runtime Utility の LDAP サポート。                                                              |
| libldap-common    | -                  | Debian系のLDAPライブラリ関連のパッケージ。RHEL系には直接の対応パッケージはないが、openldap, openldap-clients, openldap-serversなどが関連する可能性あり。  |
| bzip2             | bzip2              | 圧縮ツール。両方のディストリビューションで利用可能。                                                                   |
| dpkg-dev          | rpm-build (等)     | Debianのパッケージビルドツール。RHEL系ではrpm-buildを利用。                                                       |
| gcc               | gcc                | GNU Compiler Collection。両方のディストリビューションで利用可能。                                                        |
| gnupg             | gnupg2             | GPG暗号ツール。両方のディストリビューションで利用可能だが、名前が少し異なる。                                                |
| libapr1-dev       | apr-devel          | Apache Portable Runtime の開発用ファイル。                                                                        |
| libaprutil1-dev   | apr-util-devel     | Apache Portable Runtime Utility の開発用ファイル。                                                                  |
| libbrotli-dev     | brotli-devel       | Brotli 圧縮ライブラリの開発用ファイル。                                                                              |
| libcurl4-openssl-dev | libcurl-devel  | cURLライブラリの開発用ファイル。                                                                                   |
| libjansson-dev    | jansson-devel      | JSONライブラリの開発用ファイル。                                                                                     |
| liblua5.2-dev     | lua-devel          | Lua言語の開発用ファイル。RHEL系ではバージョンが異なる可能性あり。                                                           |
| libnghttp2-dev    | libnghttp2-devel   | HTTP/2 プロトコルライブラリの開発用ファイル。                                                                        |
| libpcre3-dev      | pcre-devel         | Perl 互換の正規表現ライブラリの開発用ファイル。                                                                       |
| libssl-dev        | openssl-devel      | OpenSSLの開発用ファイル。                                                                                         |
| libxml2-dev       | libxml2-devel      | XML ライブラリの開発用ファイル。                                                                                    |
| make              | make               | Makeツール。両方のディストリビューションで利用可能。                                                                      |
| patch             | patch              | パッチツール。両方のディストリビューションで利用可能。                                                                     |
| wget              | wget               | ネットワークダウンロードツール。両方のディストリビューションで利用可能。                                                     |
| zlib1g-dev        | zlib-devel         | 圧縮ライブラリの開発用ファイル。                                                                                          |

