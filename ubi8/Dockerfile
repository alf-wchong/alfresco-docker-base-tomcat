FROM registry.access.redhat.com/ubi8/ubi:8.4-209
LABEL org.label-schema.schema-version="1.0" \
	org.label-schema.name="Alfresco_RH_UBI8 Base Tomcat Image" \
	org.label-schema.vendor="Hyland COPS" \
	org.label-schema.build-date="$CREATED" \
	org.opencontainers.image.title="Alfresco_RH_UBI8" \
	org.opencontainers.image.vendor="Hyland COPS" \
	org.opencontainers.image.revision="$REVISION" \
	org.opencontainers.image.source="https://github.com/alf-wchong/alfresco-docker-base-tomcat" \
	org.opencontainers.image.created="$CREATED"
	
ENV CATALINA_HOME /usr/local/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH
RUN mkdir -p "$CATALINA_HOME"
WORKDIR $CATALINA_HOME

# let "Tomcat Native" live somewhere isolated
ENV TOMCAT_NATIVE_LIBDIR $CATALINA_HOME/native-jni-lib
ENV LD_LIBRARY_PATH ${LD_LIBRARY_PATH:+$LD_LIBRARY_PATH:}$TOMCAT_NATIVE_LIBDIR

ADD dependencies/tomcat.tar.xz $CATALINA_HOME

RUN set -eux; \
	yum install -y java-11-openjdk-devel apr-devel redhat-rpm-config gcc make openssl-devel; \
	nativeBuildDir="$(mktemp -d)"; \
	tar -xvf bin/tomcat-native.tar.gz -C "$nativeBuildDir" --strip-components=1; \
	( \
		export CATALINA_HOME="$PWD"; \
		cd "$nativeBuildDir/native"; \
		./configure \
			--with-java-home="$(echo $(readlink -f $(which java))|rev|cut -d '/' -f3-|rev)" \
                        --libdir="$TOMCAT_NATIVE_LIBDIR" \
			--prefix="$CATALINA_HOME" \
			--with-apr="$(which apr-1-config)" \
			--with-ssl=yes; \
		make -j "$(nproc)"; \
		make install; \
	); \
# comment off the rollback to check if it is causing errors. yum history -y rollback last-1; \
	find /etc -mindepth 2 -name *.rpmsave -exec rm -v '{}' +; \
	rm -rf "$nativeBuildDir"; \
	rm bin/tomcat-native.tar.gz; \
	\
# sh removes env vars it doesn't support (ones with periods)
# https://github.com/docker-library/tomcat/issues/77
	find ./bin/ -name '*.sh' -exec sed -ri 's|^#!/bin/sh$|#!/usr/bin/env bash|' '{}' +; \
	\
# fix permissions (especially for running as non-root)
# https://github.com/docker-library/tomcat/issues/35
	chmod -R +rX .; \
	chmod 777 logs work ; \
	\
	# Security improvements:
	# Remove server banner, Turn off loggin by the VersionLoggerListener, enable remoteIP valve so we know who we're talking to
	sed -i \
          -e "s/\  <Listener\ className=\"org.apache.catalina.startup.VersionLoggerListener\"/\  <Listener\ className=\"org.apache.catalina.startup.VersionLoggerListener\"\ logArgs=\"false\"/g" \
          -e "s%\(^\s*</Host>\)%\t<Valve className=\"org.apache.catalina.valves.RemoteIpValve\" />\n\n\1%" \
          -e "s/\    <Connector\ port=\"8080\"\ protocol=\"HTTP\/1.1\"/\    <Connector\ port=\"8080\"\ protocol=\"HTTP\/1.1\"\n\               Server=\" \"/g" /usr/local/tomcat/conf/server.xml; \
	# Removal of default/unwanted Applications
	rm -f -r -d /usr/local/tomcat/webapps/* ; \
	# Change SHUTDOWN port and command.
	#     sed -i "s/<Server\ port=\"8005\"\ shutdown=\"SHUTDOWN\">/<Server\ port=\"ShutDownPort\"\ shutdown=\"ShutDownCommand\">/g" /usr/local/tomcat/conf/server.xml ; \
	# Replace default 404,403,500 page
	sed -i "$ d" /usr/local/tomcat/conf/web.xml ; \
	sed -i -e "\$a\    <error-page\>\n\        <error-code\>404<\/error-code\>\n\        <location\>\/error.jsp<\/location\>\n\    <\/error-page\>\n\    <error-page\>\n\        <error-code\>403<\/error-code\>\n\        <location\>\/error.jsp<\/location\>\n\    <\/error-page\>\n\    <error-page\>\n\        <error-code\>500<\/error-code\>\n\        <location\>\/error.jsp<\/location\>\n\    <\/error-page\>\n\n\<\/web-app\>" /usr/local/tomcat/conf/web.xml ; \
	yum clean all

# verify Tomcat Native is working properly
RUN set -e \
	&& nativeLines="$(catalina.sh configtest 2>&1 | grep -c 'Loaded Apache Tomcat Native library')" \
	&& test $nativeLines -ge 1 || exit 1

EXPOSE 8080
# Starting tomcat with Security Manager
CMD ["catalina.sh", "run", "-security"]
