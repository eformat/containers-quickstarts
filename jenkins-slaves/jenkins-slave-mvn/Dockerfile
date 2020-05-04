FROM quay.io/openshift/origin-jenkins-agent-base:latest

ENV MAVEN_VERSION=3.6 \
    BASH_ENV=/usr/local/bin/scl_enable \
    ENV=/usr/local/bin/scl_enable \
    PROMPT_COMMAND=". /usr/local/bin/scl_enable" \
    LANG=en_US.UTF-8 \
    LC_ALL=en_US.UTF-8 \
    MAVEN_OPTS="-Duser.home=$HOME"

# Copy entitlements
COPY ./etc-pki-entitlement /etc/pki/entitlement
# Copy subscription manager configurations
COPY ./rhsm-conf /etc/rhsm
COPY ./rhsm-ca /etc/rhsm/ca

# Install Maven
RUN INSTALL_PKGS="java-11-openjdk-devel.x86_64 java-1.8.0-openjdk-devel.x86_64 rh-maven36*" && \
    DISABLES="--disablerepo=rhel-server-extras --disablerepo=rhel-server --disablerepo=rhel-fast-datapath --disablerepo=rhel-server-optional --disablerepo=rhel-server-ose --disablerepo=rhel-server-rhscl --enablerepo=rhel-server-rhscl-7-rpms --enablerepo=rhel-7-server-rpms" && \
    yum $DISABLES install -y $INSTALL_PKGS && \
    yum clean all -y && \
    mkdir -p $HOME/.m2 && \
    rm -rf /var/cache/yum && \
    rm -rf /etc/pki/entitlement && \
    rm -rf /etc/rhsm

# When bash is started non-interactively, to run a shell script, for example it
# looks for this variable and source the content of this file. This will enable
# the SCL for all scripts without need to do 'scl enable'.
ADD scl_enable /usr/local/bin/scl_enable
ADD configure-agent /usr/local/bin/configure-agent

ADD settings.xml $HOME/.m2/settings.xml

RUN chown -R 1001:0 $HOME && \
    chmod -R g+rw $HOME

USER 1001
