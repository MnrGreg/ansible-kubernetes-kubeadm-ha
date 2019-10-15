# CronJob to backup ETCD state to S3 storage 

### 2. Update API yaml environment variables

For example:
- S3_FOLDER=k8skaf-api05-prod
- S3_BUCKET=prod-container-registry
- cron schedule

### 3. Upload Kubernetes API and S3 secrets to kubernetes

Create kubernetes secrets for the UCP password and S3 access keys
```
kubectl create -n kube-system -f ./etcd-backup-cronjob.yaml
kubectl create -n kube-system -f ./s3-storage-secret.yaml
```