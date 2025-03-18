k apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.9/config/manifests/metallb-native.yaml
k apply -f configmap.yaml -n metallb-system
