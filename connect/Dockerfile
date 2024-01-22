FROM alpine:3.19 as builder

RUN apk add --no-cache ca-certificates curl bash git openssh

ARG KUBECTL_VERSION
ARG K9S_VERSION
ARG FLUX_VERSION

# Get zsh plugins:
RUN git clone https://github.com/zsh-users/zsh-autosuggestions ~/.zsh/plugins/zsh-autosuggestions
RUN git clone https://github.com/zsh-users/zsh-completions.git ~/.zsh/plugins/zsh-completions
RUN git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.zsh/plugins/zsh-syntax-highlighting

# Install kubectl:
RUN curl -LO "https://dl.k8s.io/release/$(curl -LO https://dl.k8s.io/release/v${KUBECTL_VERSION}/bin/linux/amd64/kubectl)/bin/linux/amd64/kubectl.sha256"
RUN install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
RUN kubectl version --client

# Install k9s:
RUN curl -LO https://github.com/derailed/k9s/releases/download/v${K9S_VERSION}/k9s_linux_amd64.apk
RUN apk add --allow-untrusted --no-cache k9s_linux_amd64.apk
RUN rm k9s_linux_amd64.apk

# Install flux:
RUN /bin/bash -c "curl -s https://fluxcd.io/install.sh | VERSION_FLUX=${FLUX_VERSION} bash"

FROM alpine:3.19

ARG USERNAME=captain
ARG HOME=/home/${USERNAME}

RUN apk add --no-cache zsh

RUN addgroup -g 1000 -S containerops && adduser -D -G containerops -u 1000 -S ${USERNAME}

USER ${USERNAME}

WORKDIR ${HOME}

RUN mkdir ${HOME}/.zsh

COPY .zshrc.template ${HOME}/.zshrc
COPY --from=builder /root/.zsh/ ${HOME}/.zsh/
COPY --from=builder /usr/local/bin/kubectl /usr/local/bin/
COPY --from=builder /usr/bin/k9s /usr/bin/
COPY --from=builder /usr/local/bin/flux /usr/local/bin/

ENTRYPOINT [ "/bin/zsh" ]