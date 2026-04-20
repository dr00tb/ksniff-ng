# ksniff-ng

Capture traffic from a Kubernetes pod and pipe it into Wireshark.

Inspired by [eldadru/ksniff](https://github.com/eldadru/ksniff), which had this on its roadmap:

> Instead of uploading static tcpdump, use the future support of "kubectl debug" feature...

ksniff-ng drops an `alpine:latest` ephemeral container into the target pod's network namespace, `apk add tcpdump` at runtime, and streams pcap back out.

## Demo
![Demo!](assets/demo.gif)

## Install

```sh
cp kubectl-sniff kubectl_complete-sniff /usr/local/bin/
chmod +x /usr/local/bin/kubectl-sniff /usr/local/bin/kubectl_complete-sniff
```

## Usage

```
Usage: kubectl-sniff [options] POD

Options:
  -n, --namespace NS     Pod namespace (default: current kube context)
  -i, --interface IFACE  Capture interface inside the pod (default: any)
  -f, --filter EXPR      tcpdump filter expression, e.g. 'tcp port 80'
  -o, --output DEST      Write pcap to DEST. Use '-' to write to stdout so
                         you can pipe into anything (e.g. tshark -r -).
                         Default: open Wireshark.
  -I, --image IMAGE      Ephemeral container image (default: alpine:latest).
                         Useful for air-gapped clusters that mirror images
                         to an internal registry.
  -h, --help             Show this help

Examples:
  kubectl-sniff my-pod
  kubectl-sniff -n prod -f 'port 443' my-pod
  kubectl-sniff -o /tmp/trace.pcap my-pod
  kubectl-sniff -f 'port 80' -o - my-pod | tshark -r -
  kubectl-sniff -I registry.internal/mirror/alpine:3.20 my-pod
```

## Requirements

- kubectl 1.27+ (for `--profile=netadmin`)
- A cluster with ephemeral containers enabled (on by default since 1.25)
- Wireshark on PATH, unless you're redirecting with `-o`
