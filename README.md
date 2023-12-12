# istio-labs
# download and extract the latest release automatically (Linux or macOS):
```
curl -L https://istio.io/downloadIstio | sh -
```
**Move to the Istio package directory**
```
cd istio-1.20.0
```
**Add the istioctl client to your path (Linux or macOS):**
```
export PATH=$PWD/bin:$PATH
```
**Install Istio**
```
istioctl install --set profile=demo -y
```
**Add a namespace label to instruct Istio to automatically inject Envoy sidecar proxies when you deploy your application later:**
```
kubectl label namespace default istio-injection=enabled
```
