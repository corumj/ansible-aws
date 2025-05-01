# AWS Related Demos 



## Backup and Restore Demos 
Tested with Red Hat internal tools - Blank AWS Open Environment 


Current EE (should match included ansible-navigator.yml): quay.io/jerrycorum/cloud_ee:3.5

CAVEATS:
These are not parameterized, they create, backup, and restore a single instance.
Many more steps would need to be considered for production backup and restore processes, but these should be a good starting point. 
I'm backing up just the root device in this example - the options are there for backing up any device attached, see the ec2_snapshot and ec2_vol module documentation for more information


### Backup and Restore an EC2 Instance from a snapshot 
Setup an Execution Environment in your Automation Controller the Current EE above or pull it from the included ansible-navigator.yml file (the playbooks were developed and tested with ansible-navigator as well). 
Export AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY so they can be passed through by ansible-navigator to the modules.
```
export AWS_ACCESS_KEY_ID=<access key>
export AWS_SECRET_ACCESS_KEY=<secret key>
```

You'll also need to have EC2 Dynamic inventory setup - for source_variables use `aws_ec2.yml` in this repo, you can also use that with `ansible-navigator run playbook.yml -i aws_ec2.yml`.  

Setup the job templates - 
| Job Template Name | Playbook Name |
| ----------------- | ------------- |
| [AWS] Backup 1 Create Instance | backup.ec2.1.create-instance.yml |
| [AWS] Backup 2 Install a simple app we can backup | backup.ec2.2.installasimpleapp.yml | 
| [AWS] Backup 3 Create a snapshot | backup.ec2.3.create-snapshot.yml |
| [AWS] Backup 4 Create a catastrophe aka mess up our app | backup.ec2.4.catastrophe.yml |
| [AWS] Backup 5 Restore from snapshot | backup.ec2.5.restore-from-snapshot.yml |

And if you'd like, setup a workflow to piece some of these steps together, I wouldn't recommend adding a workflow just because though, add failover options, notifications, and recovery options for the backup steps themselves as well.  


-- TODO
### Backup and Restore an AWS RDS PostgreSQL Instance from a snapshot 