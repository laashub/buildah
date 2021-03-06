# git/Dockerfile
#
# Build a Buildah container image from the latest
# upstream version of Buildah on GitHub.
# https://github.com/containers/buildah
# This image can be used to create a secured container
# that runs safely with privileges within the container.
# The containers created by this image also come with a
# Buildah development environment in /root/buildah.
#
FROM fedora:latest
ENV GOPATH=/root/buildah

# Install the software required to build Buildah.
# Then create a directory and clone from the Buildah
# GitHub repository, make and install Buildah
# to the container.
# Finally remove the buildah directory and a few other packages
# that are needed for building but not running Buildah
RUN useradd build; yum -y update; yum -y reinstall shadow-utils; yum -y install --enablerepo=updates-testing \
     make \
     golang \
     bats \
     btrfs-progs-devel \
     device-mapper-devel \
     glib2-devel \
     gpgme-devel \
     libassuan-devel \
     libseccomp-devel \
     git \
     bzip2 \
     go-md2man \
     runc \
     fuse-overlayfs \
     fuse3 \
     containers-common; \
     mkdir /root/buildah; \
     git clone https://github.com/containers/buildah /root/buildah/src/github.com/containers/buildah; \
     cd /root/buildah/src/github.com/containers/buildah; \
     make;\
     make install;\
     rm -rf /root/buildah/*; \
     yum -y remove bats git golang go-md2man make; \
     yum clean all;

ADD https://raw.githubusercontent.com/containers/buildah/master/contrib/buildahimage/stable/containers.conf /etc/containers/

# Adjust storage.conf to enable Fuse storage.
RUN sed -i -e 's|^#mount_program|mount_program|g' -e '/additionalimage.*/a "/var/lib/shared",' -e 's|^mountopt[[:space:]]*=.*$|mountopt = "nodev,fsync=0"|g' /etc/containers/storage.conf
RUN mkdir -p /var/lib/shared/overlay-images /var/lib/shared/overlay-layers; touch /var/lib/shared/overlay-images/images.lock; touch /var/lib/shared/overlay-layers/layers.lock

# Set an environment variable to default to chroot isolation for RUN
# instructions and "buildah run".
ENV BUILDAH_ISOLATION=chroot
