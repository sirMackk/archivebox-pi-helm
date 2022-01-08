# Archivebox RPI Helm Chart

This helm chart is meant to install [Archivebox][0] on a Raspberry Pi or similar system (ARM, Debian, etc.) running k3s or similar k8s-like service.

It's been tested on RPi4 B w/ 4G of ram, running Raspbian 11 (bullseye) and k3s version v1.21.7+k3s1 (ac705709) compiled with Go 1.16.10.

It does not include Sonic because [Sonic doesn't compile to run on ARM.][1].

## Installing

1. Copy the values.yaml file and edit it for your setup.
  1. Take special note of the nodeAffinityHostname setting and the storage.localPath setting.
2. Run `helm install archivebox archivebox_helm/ -f custom-values.yaml`
3. Wait 20-60 seconds for archivebox to set itself up.
4. Navigate to `<ip of raspberry pi>:<the external port you set>` and enjoy!

## Troubleshooting

kubectl is your friend.

1. `kubectl describe pod archivebox-0`
  1. Check things like container states and volume mounts. Specifically check if the init container succeeded.
2. `kubectl logs archivebox-0 -c archivebox-init`
  1. K8s uses [expect][2] to setup the admin user. If that fails, the pod will never come up healthy.
3. `kubectl exec -it archivebox-0 -- bash`
  1. If the init container succeeded, check out what's going on inside the container. At this point, you likely have to troubleshoot archivebox itself.
4. `kubectl get services`
  1. Check if the port you expect to be exposed is exposed. K3s w/ traefik uses iptables to foward traffic to the container, so running `netstat -tpln` on the host box will not show whetehr anything is listening on the port you set up.

## LICENSE

Copyright 2022 sirMackk

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.



[0]: https://archivebox.io/
[1]: https://github.com/valeriansaliou/sonic/issues/202
[2]: https://linux.die.net/man/1/expect
