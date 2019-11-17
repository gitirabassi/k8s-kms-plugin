## [k8s-vault-kms-plugin](cmd/k8s-kms-plugin)

This plugin used to encrypt kuberentes's secrets in ETCD coming from kube-apiserver. This is a complete rewrite using libvault of the original https://github.com/oracle/kubernetes-vault-kms-plugin. If you like to use an updated version of the origin version with go modules and usable with `vault agent` please go here: https://github.com/gitirabassi/kubernetes-vault-kms-plugin

## Installation 
```shell
#!/bin/bash
set -xe 
KMS_PLUGIN_VERSION=0.0.1

# Build and run script for https://github.com/gitirabassi/k8s-vault-kms-plugin
curl -sS -L https://github.com/gitirabassi/k8s-vault-kms-plugin/releases/download/v${KMS_PLUGIN_VERSION}/k8s-vault-kms-plugin_${KMS_PLUGIN_VERSION}_linux_amd64.tar.gz -o /tmp/k8s-vault-kms-plugin_${KMS_PLUGIN_VERSION}_linux_amd64.tar.gz
tar xzvf /tmp/k8s-vault-kms-plugin_${KMS_PLUGIN_VERSION}_linux_amd64.tar.gz -C /usr/local/bin
chmod +x /usr/local/bin/k8s-vault-kms-plugin
```

## Usage
```shell
cat <<EOF >/etc/systemd/system/vault-kms.service
[Unit]
Description=Kubernetes KMS plugin provider for HashiCorp Vault

[Service]
Environment=VAULT_ADDR=unix:///tmp/vaultagent.sock
ExecStart=
ExecStart=/usr/local/bin/kms-plugin \
    -socketFile=unix:///var/run/vault-kms.sock \
    -transitPath=transit
    -keyNames=kubernetes

Restart=always
StartLimitInterval=0
RestartSec=10

[Install]
WantedBy=multi-user.t
EOF

systemctl enable --now vault-kms
```

## Configure Vault
```shell
vault status
vault secrets enable transit
vault write -f transit/keys/kubernetes
```

## Then Configure Kubeadm
```yaml

```
