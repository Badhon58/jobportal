## Step 1

- Create a EC2 instance then set the role or I Am User
  - AdministratorAccess
  - IAMFullAccess
  - AmazonVPCFullAccess
  - AmazonEC2FullAccess
  - AWSCloudFormationFullAccess
  - Amazon_EBS_CSI_Driver
    -> `{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:AttachVolume",
        "ec2:CreateSnapshot",
        "ec2:CreateTags",
        "ec2:CreateVolume",
        "ec2:DeleteSnapshot",
        "ec2:DeleteTags",
        "ec2:DeleteVolume",
        "ec2:DescribeInstances",
        "ec2:DescribeSnapshots",
        "ec2:DescribeTags",
        "ec2:DescribeVolumes",
        "ec2:DetachVolume"
      ],
      "Resource": "*"
    }
  ]
}`

Add this to role

---

## Step 2

- Create A Cluster

```
# Create Cluster
eksctl create cluster --name=cluster1 \
                      --region=ap-south-1  \
                      --zones=ap-south-1a,ap-south-1b,ap-south-1c\
                      --without-nodegroup
# Get List of clusters
eksctl get clusters
```

# Step 3

- Verify the IAM ODIC Provider

```
eksctl utils associate-iam-oidc-provider \
    --region ap-south-1 \
    --cluster cluster1 \
    --approve
```

# Step 4

- Create Node Group with additional Add-Ons in Public Subnets

```
# Create Public Node Group
eksctl create nodegroup --cluster=cluster1 \
                       --region=ap-south-1  \
                       --name=eksdemo1-ng-public1 \
                       --node-type=t3.medium \
                       --nodes=1 \
                       --nodes-min=1 \
                       --nodes-max=2 \
                       --node-volume-size=20
# Check The Nodes
$ Kubectl get nodes
$ Kubectl get svc
```

---

# Step 5

Create A Adon in Our Cluster

```
eksctl create addon \
  --name aws-ebs-csi-driver \
  --cluster cluster1 \
  --force
```

# Step 6

- Create The Mongo DB with aws.esk.ebs.storage class

```
# -------------------------
# PersistentVolume (EBS-backed)
# -------------------------
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gp2-csi
parameters:
  type: gp2
  fsType: ext4
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer
reclaimPolicy: Delete

---
# -------------------------
# PersistentVolumeClaim (EBS-backed)
# -------------------------
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mongo-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: gp2 # Use the default EBS gp3 StorageClass in EKS

```

Delete The Cluster

```
eksctl delete cluster --name cluster1 --region ap-south-1
```