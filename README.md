## [公式のApacheのdockerイメージ](https://github.com/docker-library/httpd/blob/242f3c62ba1ceee0a3633045fc4fd9277cb86cd3/2.4/Dockerfile)がDebianベースで動くように書かれているので、RHEL系に読み替えて動かしてみた。最低限動くようにはなった。
以下に記載するように、対応していないパッケージもある。


##[ Official Apache docker image](https://github.com/docker-library/httpd/blob/242f3c62ba1ceee0a3633045fc4fd9277cb86cd3/2.4/Dockerfile) runs on Debian, so I changed it to RHEL and looked at it. It almost started moving.
As mentioned above, there are some packages that are not compatible.


## Debian系とRHEL系でのパッケージの違いをchatgptに聞いた結果を整理(誤りある可能性有り)
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

