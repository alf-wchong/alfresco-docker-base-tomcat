Get Tomcat package. Below is a non-exhaustive list of URLs to get Tomcat.
        https://www.apache.org/dyn/closer.cgi?action=download&filename=tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz.asc
	https://www-us.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz.asc
	https://www.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz.asc
	https://archive.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz.asc

Repackage the downloaded package so that it doesn't contain a root directory that identifies the version of Tomcat, see the "strip component" option of https://github.com/Alfresco/alfresco-docker-base-tomcat/blob/b2324f55c6f86f475dbc4bc84bae22b207548331/Dockerfile#L104

The repackaged archive should be named tomcat.tar.xz
