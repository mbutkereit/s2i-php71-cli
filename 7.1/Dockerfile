FROM openshift/base-centos7

ENV PHP_VERSION=7.0 \
    PATH=$PATH:/opt/rh/rh-php71/root/usr/bin

LABEL io.k8s.description="PHP 7 (Cli)" \
      io.k8s.display-name="PHP 7 (Cli)" \
      io.openshift.tags="builder,php,php7,php70,php-cli"

# Install Apache httpd and PHP
RUN curl 'https://setup.ius.io/' -o setup-ius.sh && \
    bash setup-ius.sh
RUN rm setup-ius.sh
RUN yum install -y --setopt=tsflags=nodocs --enablerepo=centosplus \
    php71u-cli \
    php71u-gd \
    php71u-imap \
    php71u-json \
    php71u-mbstring \
    php71u-mcrypt \
    php71u-opcache \
    php71u-pdo \
    php71u-xml
RUN yum clean all -y

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Each language image can have 'contrib' a directory with extra files needed to
# run and build the applications.
COPY ./contrib/ /opt/app-root

# In order to drop the root user, we have to make some directories world
# writeable as OpenShift default security model is to run the container under
# random UID.
RUN mkdir /tmp/sessions && \
    chown -R 1001:0 /opt/app-root /tmp/sessions && \
    chmod -R a+rwx /tmp/sessions && \
    chmod -R ug+rwx /opt/app-root && \
    chmod +x /opt/app-root/services.sh

USER 1001

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
