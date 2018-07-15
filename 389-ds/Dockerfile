FROM centos:centos7

MAINTAINER Andrew O'Neill <andrew at meanjollies dot com>

# Install 389ds package
RUN yum install -y 389-ds-base

# Install initial config along with users and groups
COPY conf/ds-setup.inf /ds-setup.inf
COPY conf/users_groups.ldif /users_groups.ldif
COPY conf/additional-conf.ldif /additional-conf.ldif

# Set up 389ds
RUN useradd ldapadmin \
    && rm -fr /var/lock /usr/lib/systemd/system \
    # The 389-ds setup will fail because the hostname can't reliably be determined, so we'll bypass it and then install
    && sed -i 's/checkHostname {/checkHostname {\nreturn();/g' /usr/lib64/dirsrv/perl/DSUtil.pm \
    && sed -i 's/updateSelinuxPolicy($inf);//g' /usr/lib64/dirsrv/perl/* \
    # Do not restart at the end
    && sed -i '/if (@errs = startServer($inf))/,/}/d' /usr/lib64/dirsrv/perl/* \
    && setup-ds.pl --silent --file /ds-setup.inf \
    && /usr/sbin/ns-slapd -D /etc/dirsrv/slapd-dir \
    # ns-slapd takes a while to start up before we can add users and groups
    && sleep 5 \
    && ldapadd -H ldap:/// -f /users_groups.ldif -x -D "cn=Directory Manager" -w password

# Expose port 389 for access
EXPOSE 389

# Start an instance of 389ds and output realtime transactions
CMD /usr/sbin/ns-slapd -D /etc/dirsrv/slapd-dir && tail -F /var/log/dirsrv/slapd-dir/access
