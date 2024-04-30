FROM node:22-bookworm

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Tokyo
RUN echo $TZ > /etc/timezone

ARG WORKDIR=/project
WORKDIR ${WORKDIR}

ARG NPM_GLOBAL=/usr/local/share/npm-global
ENV PATH=${NPM_GLOBAL}/bin:${PATH}

ARG USERNAME=node

# change default shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
RUN chsh -s /bin/bash

# Increase timeout for apt-get to 300 seconds
# hadolint ignore=DL3059
RUN /bin/echo -e "\n\
    Acquire::http::Timeout \"300\";\n\
    Acquire::ftp::Timeout \"300\";" >> /etc/apt/apt.conf.d/99timeout

RUN \
    # Configure global npm install location, use group to adapt to UID/GID changes
    if ! cat /etc/group | grep -e "^npm:" > /dev/null 2>&1; then groupadd -r npm; fi \
    && usermod -a -G npm ${USERNAME} \
    && umask 0002 \
    && mkdir -p ${NPM_GLOBAL} \
    && touch /usr/local/etc/npmrc \
    && chown ${USERNAME}:npm ${NPM_GLOBAL} /usr/local/etc/npmrc \
    && chmod g+s ${NPM_GLOBAL} \
    && npm config -g set prefix ${NPM_GLOBAL} \
    && su ${USERNAME} -c "npm config -g set prefix ${NPM_GLOBAL}" \
    # Install eslint
    && su ${USERNAME} -c "umask 0002 && npm install -g eslint" \
    && npm cache clean --force > /dev/null 2>&1

# Configure apt and install packages
RUN rm -rf /var/lib/apt/lists/* \
    && apt-get update \
    && apt-get upgrade -y \
    && apt-get -y --no-install-recommends install sudo vim tzdata less \
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# sudo settings
RUN echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# install node modules
ARG NODE_MODULES="tslint-to-eslint-config typescript"
RUN umask 0002 \
    && npm install -g ${NODE_MODULES} \
    && npm cache clean --force > /dev/null 2>&1

# install hadolint
RUN wget --progress=dot:giga https://github.com/hadolint/hadolint/releases/latest/download/hadolint-Linux-x86_64 -O /usr/local/bin/hadolint \
    && chmod +x /usr/local/bin/hadolint

USER $USERNAME

# terminal setting
RUN wget --progress=dot:giga https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash -O ~/.git-completion.bash \
    && wget --progress=dot:giga https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh -O ~/.git-prompt.sh \
    && chmod a+x ~/.git-completion.bash \
    && chmod a+x ~/.git-prompt.sh \
    && echo -e "\n\
    export VIRTUAL_ENV_DISABLE_PROMPT=1\n\
    source ~/.git-completion.bash\n\
    source ~/.git-prompt.sh\n\
    export PS1='\\[\\e[30m\\]\\\\t\\[\\e[0m\\] \\[\\e]0;\\u@\\h: \\w\\a\\]\${debian_chroot:+(\$debian_chroot)}\\[\\033[01;32m\\]\\u\\[\\033[00m\\]:\\[\\033[01;34m\\]\\w\\[\\033[00m\\]\\[\\033[1;30m\\]\$(__git_ps1)\\[\\033[0m\\] \\$ '\n\
    " >>  ~/.bashrc

ENV DEBIAN_FRONTEND=
CMD ["/bin/bash"]
