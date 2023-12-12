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
**Deploy your application using the kubectl command:**
```
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```
# If you disabled automatic sidecar injection during installation and rely on manual sidecar injection, use the istioctl kube-inject command to modify the bookinfo.yaml file before deploying your application.

```
kubectl apply -f <(istioctl kube-inject -f samples/bookinfo/platform/kube/bookinfo.yaml)
```

**The application will start. As each pod becomes ready, the Istio sidecar will be deployed along with it.**
```
kubectl get services
kubectl get pods
```
**Verify everything is working correctly up to this point. Run this command to see if the app is running inside the cluster and serving HTML pages by checking for the page title in the response:**
```
kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
```

# Determine the ingress IP and port
# Open the application to outside traffic

**The Bookinfo application is deployed but not accessible from the outside. To make it accessible, you need to create an Istio Ingress Gateway, which maps a path to a route at the edge of your mesh.**

1. Associate this application with the Istio gateway:
   ```
   kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
   ```
2. Ensure that there are no issues with the configuration:
   ```
   ```


```
