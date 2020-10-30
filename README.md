# Deploy Docker registry on Kubernetes


## Update configuration values
Replace the below values in some manifest files with new value (if needed)

We can use `envsubst` command to substitute environment variables against the teamplate file to create the manifest files with value from envrionment

I keep this repo simple without adding multiple template files to review easily

** Domain name for docker registry **
Default value: `registry.jimmytoan.com`

** AWS ACM for the above domain **
Default value: `arn:aws:acm:ap-southeast-1:421376589955:certificate/b3e3890a-02a5-47c2-ab42-64fec4315231`

** VPC CIDR for proxy-real-ip-cidr of nginx ingress controller **
Default value: `10.10.0.0/19`

** AWS EFS volume ID to store docker image data **
Default value: `fs-ab5e4cea `

** Password for admin user to access docker registry **
Default value: `password`

## Deployment
** Create Nginx ingress controller **
```
kubectl apply -f ingress-controller/nginx.yml
```

** Create namespace docker-registry **
```
kubectl apply -f common/namespace.yml
```

** Create redis service **
```
kubectl apply -f redis/deployment.yml
kubectl apply -f redis/service.yml
```

** Create storage class, persistent volume and persistent volume claim for AWS EFS **
```
kubectl apply -f registry/efs.yml
```

Running command `htpasswd -Bc auth admin` to creat auth file contain username and hashed password
Store htpasswd in config map in registry/deployment.yml

** Create docker registry **
```
kubectl apply -f registry/secret.yml
kubectl apply -f registry/deployment.yml
kubectl apply -f registry/service.yml
kubectl apply -f registry/ingress.yml
```

** Create cronjob for garbage collection and image retention **
```
kubectl apply -f cronjob/configmap.yml
kubectl apply -f cronjob/garbage-collection.yml
kubectl apply -f cronjob/retention.yml
```

