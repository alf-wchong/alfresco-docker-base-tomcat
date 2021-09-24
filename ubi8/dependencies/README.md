The Tomcat package should be retrieved the following non-exhaustive list

        https://www.apache.org/dyn/closer.cgi?action=download&filename=tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz \
	https://www-us.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz \
	https://www.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz \
	https://archive.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz
	
The retrieved packaged should be rearchived as tomcat.tar.xz after stripping out the root directory that indicates the [version & name of the package](https://github.com/Alfresco/alfresco-docker-base-tomcat/blob/b2324f55c6f86f475dbc4bc84bae22b207548331/Dockerfile#L104) so that it gets expanded into the correct location when [ADD](https://github.com/alf-wchong/alfresco-docker-base-tomcat/blob/aa4d5d9ce59f4ca292daa70b4f91cca2f0d75337/ubi8/Dockerfile#L21)ed during the image build.

