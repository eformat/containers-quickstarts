FROM quay.io/openshift/origin-jenkins-agent-base:latest

ENV MAVEN_VERSION=3.6 \
    YQ_VERSION=3.3.0 \
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
    rm -rf /etc/rhsm && \
    curl -sL  https://github.com/mikefarah/yq/releases/download/${YQ_VERSION}/yq_linux_amd64 -o /usr/local/bin/yq && \
    chmod -R 775 /usr/local/bin/yq

# When bash is started non-interactively, to run a shell script, for example it
# looks for this variable and source the content of this file. This will enable
# the SCL for all scripts without need to do 'scl enable'.
ADD scl_enable /usr/local/bin/scl_enable
ADD configure-agent /usr/local/bin/configure-agent

ADD settings.xml $HOME/.m2/settings.xml

RUN chown -R 1001:0 $HOME && \
    chmod -R g+rw $HOME

USER 1001
