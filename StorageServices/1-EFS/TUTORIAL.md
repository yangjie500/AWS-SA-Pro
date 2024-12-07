# Tutorial: Creating writable per-user subdirectories
```bash
sudo useradd -c "Mike Smith" mike
sudo passwd mike
sudo mkdir /home/mike/testdir
sudo mkdir /<EFSMountLocation:/efs/wp-content>/mike
sudo echo "fs-018d0ef45feeadae6.efs.us-east-1.amazonaws.com:/mike /home/mike/testdir nfs nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport 0 0" | sudo tee -a /etc/fstab
```

# Tutorial: Mounting Access Point
```bash
sudo echo "fs-018d0ef45feeadae6: /tenant efs _netdev,tls,accesspoint=fsap-0de716f354b6861ba 0 0" | sudo tee -a /etc/fstab
```