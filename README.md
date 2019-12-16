# Running Prometheus on Kubernetes

# Install Helm
```
wget https://storage.googleapis.com/kubernetes-helm/helm-v2.11.0-linux-amd64.tar.gz
tar -xzvf helm-v2.11.0-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm
kubectl create -f https://raw.githubusercontent.com/wardviaene/kubernetes-course/master/helm/helm-rbac.yaml
helm init --service-account tiller 
```

```
If get below error then proceed with running following commands:
Error: error installing: the server could not find the requested resource
$helm init --service-account tiller --override spec.selector.matchLabels.'name'='tiller',spec.selector.matchLabels.'app'='helm' --output yaml | sed 's@apiVersion: extensions/v1beta1@apiVersion: apps/v1@' | kubectl apply -f -
```

## Start Prometheus (without storage)
```
helm install --name prometheus --set server.persistentVolume.enabled=false,alertmanager.persistentVolume.enabled=false stable/prometheus
```

## Exposing prometheus port
```
export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace default port-forward $POD_NAME 9090 &
socat TCP4-LISTEN:9091,fork TCP4:localhost:9090 &

Now access: Now access through:http://<server_ip>:9091/ 
```


## Install Grafana
sudo ./install-grafana.sh

If want to change default port 3000 to another port then change port number in below file and restart Grafana:
1. /etc/grafana/grafana.ini
2. /usr/share/grafana/conf/defaults.ini
3. /usr/share/grafana/conf/sample.ini
4. sudo service grafana-server stop
   sudo service grafana-server start
or 
systemctl daemon-reload
systemctl start grafana-server
systemctl enable grafana-server.service
