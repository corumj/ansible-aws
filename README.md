# AWS Related Demos 



## Backup and Restore Demos 
Tested with Red Hat internal tools - Blank AWS Open Environment 


Current EE (5 May 2025): quay.io/jerrycorum/cloud_ee:3.5

CAVEATS:
These playbooks are not parameterized.  The playbooks create, backup, and restore a single ec2 instance.
Many more steps would need to be considered for production backup and restore processes, but these should be a good starting point. 
I'm backing up just the root device in this example - the options are there for backing up any device attached, see the ec2_snapshot and ec2_vol module documentation for more information


### Backup and Restore an EC2 Instance from a snapshot 
Setup an Execution Environment in your Automation Controller with the Current EE listed above or pull it from the included ansible-navigator.yml file (the playbooks were developed and tested with ansible-navigator and the listed EE). 

Export AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY provided by the Red Hat Demo system so they can be passed through by ansible-navigator to the modules. Alternately, setup an AWS credential in the Automation Controller, it will provide these enviornment variables during runtime for the job template.  


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

#### Walkthrough by playbook
1. Create Instance.  We need something to backup.  This creates a barebones RHEL 9 instance in AWS as well as the supporting infrastrucutre - VPC, Subnet, Sec group, etc..  
2. Deploy an application so we have something to backup on the instance. This example is a web app running in Apache web server.
3. Take a snapshot of the EC2 instance. 
4. Mess up the app so we can see the restore work - I'm just replacing the web app with a different one in this case 
5. Restore the VM from snapshot

-- TODO
* [ ] Add DNS updates when the instance restarts (gets a new public IP)
* [ ] Add volumes to teardown.yml 

### Backup and Restore an AWS RDS PostgreSQL Instance from a snapshot 