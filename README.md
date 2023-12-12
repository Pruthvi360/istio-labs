# istio-labs
# download and extract the latest release automatically (Linux or macOS):
```
curl -L https://istio.io/downloadIstio | sh -
```
1. Move to the Istio package directory**
   ```
   cd istio-1.20.0
   ```
2. Add the istioctl client to your path (Linux or macOS):**
   ```
   export PATH=$PWD/bin:$PATH
   ```
3. Install Istio**
   ```
   istioctl install --set profile=demo -y
   ```
4. Add a namespace label to instruct Istio to automatically inject Envoy sidecar proxies when you deploy your application later:**
   ```
   kubectl label namespace default istio-injection=enabled
   ```
5. Deploy your application using the kubectl command:**
   ```
   kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
   ```
**If you disabled automatic sidecar injection during installation and rely on manual sidecar injection, use the istioctl kube-inject command to modify the bookinfo.yaml file before deploying your application.**
   ```
   kubectl apply -f <(istioctl kube-inject -f samples/bookinfo/platform/kube/bookinfo.yaml)
   ```

6. The application will start. As each pod becomes ready, the Istio sidecar will be deployed along with it.**
   ```
   kubectl get services
   kubectl get pods
   ```
7. Verify everything is working correctly up to this point. Run this command to see if the app is running inside the cluster and serving HTML        pages by checking for the page title in the response:**
   ```
   kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage |    grep -o "<title>.*</title>"
   ```

# Determine the ingress IP and port and Open the application to outside traffic

**The Bookinfo application is deployed but not accessible from the outside. To make it accessible, you need to create an Istio Ingress Gateway, which maps a path to a route at the edge of your mesh.**

1. Associate this application with the Istio gateway:
   ```
   kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
   ```
2. Ensure that there are no issues with the configuration:
   ```
   istioctl analyze
   ```
3. Confirm the gateway has been created:
   ```
   kubectl get gateway
   ```
4. Execute the following command to determine if your Kubernetes cluster is running in an environment that supports external load balancers
   ```
   kubectl get svc istio-ingressgateway -n istio-system
   ```

**Note:** If the EXTERNAL-IP value is set, your environment has an external load balancer that you can use for the ingress gateway. If the EXTERNAL-IP value is <none> (or perpetually <pending>), your environment does not provide an external load balancer for the ingress gateway.

# MetalLB for External IP assignment

1. Set **strictARP: false** to **strictARP: true** in **kube-proxy** in kube-system namespace
   **see what changes would be made, returns nonzero returncode if different**
   ```
   kubectl get configmap kube-proxy -n kube-system -o yaml | \
   sed -e "s/strictARP: false/strictARP: true/" | \
   kubectl diff -f - -n kube-system
   ```
   **actually apply the changes, returns nonzero returncode on errors only**
   ```
   kubectl get configmap kube-proxy -n kube-system -o yaml | \
   sed -e "s/strictARP: false/strictARP: true/" | \
   kubectl apply -f - -n kube-system
   ```
2. Installation By Manifest
   ```
   kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.12/config/manifests/metallb-native.yaml
   ```
3. Assign IP ranges to External Loadbalancer
   ```
   nano ip-allocation.yml
   ```
   ```
   apiVersion: metallb.io/v1beta1
   kind: IPAddressPool
   metadata:
     name: first-pool
     namespace: metallb-system
   spec:
     addresses:
        #- 192.168.0.0/24  or
        - 192.168.0.230-192.168.0.254
   ```
5. Create IP Pool
   ```
   kubectl apply -f ip-allocation.yml
   ```
