Install k3s using ansible, reference : https://github.com/techno-tim/k3s-ansible


download helm at https://github.com/helm/helm/releases
choose "Linux amd64"
tar -zxvf helm-<ver>-linux-amd64.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
kubectl create namespace cattle-system

helm install rancher rancher-stable/rancher \
  --namespace cattle-system \
  --set hostname=rancher.akubangjago.my.eu.org \
  --set bootstrapPassword=QWXpnh953OOGl5 \
  --set ingress.enabled=false \
  --set ingress.tls.source=secret

cat <<EOF > svc.yml
apiVersion: v1
kind: Service
metadata:
  name: ranched
spec:
  selector:
    app: rancher
  ports:
    - name: http
      port: 80
      targetPort: 80
    - name: https
      port: 443
      targetPort: 443
  type: LoadBalancer
EOF

kubectl apply -f svc.yml --namespace=cattle-system