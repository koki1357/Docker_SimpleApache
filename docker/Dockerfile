FROM oraclelinux:9

# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
#RUN groupadd -r www-data && useradd -r --create-home -g www-data www-data

ENV HTTPD_PREFIX /opt/apache/httpd
ENV PATH $HTTPD_PREFIX/bin:$PATH
RUN groupadd -g 300 www-data && \
    	useradd -u 300 -g www-data -d /home/www-data -s /bin/bash www-data && \
	mkdir -p "$HTTPD_PREFIX" \
	&& chown www-data:www-data "$HTTPD_PREFIX"

WORKDIR $HTTPD_PREFIX

# install httpd runtime dependencies
# https://httpd.apache.org/docs/2.4/install.html#requirements
RUN set -eux; \
	yum update; \
	yum install -y \
# https://github.com/docker-library/httpd/issues/214
		ca-certificates \
		apr-util-ldap \
# https://github.com/docker-library/httpd/issues/209
		openldap \
	; \
	yum clean all

ENV HTTPD_VERSION 2.4.57
ENV HTTPD_SHA256 dbccb84aee95e095edfbb81e5eb926ccd24e6ada55dcd83caecb262e5cf94d2a

# https://httpd.apache.org/security/vulnerabilities_24.html
ENV HTTPD_PATCHES="rewrite-windows-testchar-h.patch 1d5620574fa03b483262dc5b9a66a6906553389952ab5d3070a02f887cc20193"

# see https://httpd.apache.org/docs/2.4/install.html#requirements
RUN set -eux; \
	# mod_http2 mod_lua mod_proxy_html mod_xml2enc
	# https://anonscm.debian.org/cgit/pkg-apache/apache2.git/tree/debian/control?id=adb6f181257af28ee67af15fc49d2699a0080d4c
	yum update; \
	yum install -y \
		bzip2 \
		rpm-build \
		gcc \
		gnupg2 \
		apr-devel \
		apr-util-devel \
		brotli-devel \
		libcurl-devel \
#
# 以下の3つのパッケージはyumになかったので、一旦省略。基本動作には問題なし。
# HTTP/2.0に対応したい場合は、パッケージの個別設定が必要。
#
#		jansson-devel \
#		lua-devel \
#		libnghttp2-devel \
		pcre-devel \
		openssl-devel \
		libxml2-devel \
		make \
		patch \
		wget \
		zlib-devel \
	; \
#	yum clean all;
	\
	ddist() { \
		local f="$1"; shift; \
 		local distFile="$1"; shift; \
		local success=; \
		local distUrl=; \
		for distUrl in \
# https://issues.apache.org/jira/browse/INFRA-8753?focusedCommentId=14735394#comment-14735394
			'https://www.apache.org/dyn/closer.cgi?action=download&filename=' \
# if the version is outdated (or we're grabbing the .asc file), we might have to pull from the dist/archive :/
			https://downloads.apache.org/ \
			https://www-us.apache.org/dist/ \
			https://www.apache.org/dist/ \
			https://archive.apache.org/dist/ \
		; do \
			if wget -O "$f" "$distUrl$distFile" && [ -s "$f" ]; then \
				success=1; \
				break; \
			fi; \
		done; \
		[ -n "$success" ]; \
	}; \
	\
	ddist 'httpd.tar.bz2' "httpd/httpd-$HTTPD_VERSION.tar.bz2"; \
	echo "$HTTPD_SHA256 *httpd.tar.bz2" | sha256sum -c -; \
	\
# see https://httpd.apache.org/download.cgi#verify
	ddist 'httpd.tar.bz2.asc' "httpd/httpd-$HTTPD_VERSION.tar.bz2.asc"; \
	\
	mkdir -p src; \
	tar -xf httpd.tar.bz2 -C src --strip-components=1; \
	rm httpd.tar.bz2; \
	cd src; \

	./configure \
		--prefix="$HTTPD_PREFIX" \
		--enable-mods-shared=reallyall \
		--enable-mpms-shared=all \
# enable the same hardening flags as Debian
# - https://salsa.debian.org/apache-team/apache2/blob/87db7de4e59683fb03e97900f078d06ef2292748/debian/rules#L19-21
# - https://salsa.debian.org/apache-team/apache2/blob/87db7de4e59683fb03e97900f078d06ef2292748/debian/rules#L115
		--enable-pie \
	; \
	make -j "$(nproc)"; \
	make install; \
	\
	cd ..; \
	rm -r src man manual; \
	\
	sed -ri \
		-e 's!^(\s*CustomLog)\s+\S+!\1 /proc/self/fd/1!g' \
		-e 's!^(\s*ErrorLog)\s+\S+!\1 /proc/self/fd/2!g' \
		-e 's!^(\s*TransferLog)\s+\S+!\1 /proc/self/fd/1!g' \
		-e 's!^(\s*User)\s+daemon\s*$!\1 www-data!g' \
		-e 's!^(\s*Group)\s+daemon\s*$!\1 www-data!g' \
		"$HTTPD_PREFIX/conf/httpd.conf" \
		"$HTTPD_PREFIX/conf/extra/httpd-ssl.conf" \
	; \
	grep -E '^\s*User www-data$' "$HTTPD_PREFIX/conf/httpd.conf"; \
	grep -E '^\s*Group www-data$' "$HTTPD_PREFIX/conf/httpd.conf"; \
	\
# yumで不要な依存関係を削除する場合、yum autoremove -yをこの行に記載する。
	\
# smoke test
	httpd -v

# https://httpd.apache.org/docs/2.4/stopping.html#gracefulstop
STOPSIGNAL SIGWINCH

COPY httpd-foreground $HTTPD_PREFIX/bin
RUN chmod 755 $HTTPD_PREFIX/bin/httpd-foreground

EXPOSE 80

CMD ["httpd-foreground"]
