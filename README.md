# RE-ARCHITECTURING A MULTI TIER WEB JAVA APPLICATION STACK ON AWS-PAAS

This setup is an improvement to the project , where we deploy our app on EC2, and we had to install and manage the ec2 and the installed app manually (Infrastructure as a Service[IaaS]).

However, in this project, we will still be using AWS services, but it will be base on platform as a Service (PaaS), this means we will not be creating ec2 and deploy app in them manually, we will be using AWS services directly with no concern about the underline ec2, example of those services include RDS, elastic beanstalk, memcache etc. 

We will be using AWS cloud managed service.

## Sevices to be used for this project

1.  **Frontend Services:**

    * **Beanstalk:** Beans stalk service will take care of tomcat server, Load balancer, Autoscaling.
    * **S3/EFS:** For storage

2. **Backend Services:**

    * **RDS:** For databaces
    * **Elastic cache:** For Memcached 
    * **Active MQ:** For RabbitMQ
    * **Route 53:** For DNS service:
    * **Cloudfront:** For content delivery service

## FLOW OF EXECUTION

1. Login to AWS account
2. Create keypair for beanstalk instance login
3. Create security group for Elastic cache, RDS and ActiveMQ (backend)
4. Create:
    * RDS
    * Elastic Cache
    * Rabbit MQ
5. Create Elastic Beanstalk Environment
6. Update SG of backend to allow traffic from Bean SG
7. Update SG of backend to allow internal traffic
8. Launch an Ec2-instance for initializing
9. Login to the instance and initialize RDS DB
10. Change healthcheck on beanstalk to /login
11. Add 443 https listener to ELB
12. Build Artifact with backend information
13. Deploy Artifact to Beanstalk
14. Create CDN with ssl cert
15. Update Entery in either route53 dns or any other dns provider of choice
16. Test the URL

* Create subnet group and parameter group for both RDS and memcached

* After creating the rds, you will need to connected to it, that is why pem key must be attache to the rds.

![RDS-created](RDS-created.png)

Clone the source code the local system 

```markdown
git clone https://github.com/hkhcoder/vprofile-project.git
```

* Then create an Ec2, install db client to be able to access RDS, while login to the ec2, send [this table](vprofile-project/src/main/resources/db_backup.sql) to mysql, that is what the tomcat in the elastic bean will access

**Login details:**

rds endpoint: java-app-db.cn0qacy4c7da.eu-north-1.rds.amazonaws.com
memcached endpoint: java-app-cache.rh3hlv.cfg.eun1.cache.amazonaws.com
rabbitmq endpoint: b-57a9cf3c-c509-46c9-8ecc-598d1b8190dc.mq.eu-north-1.amazonaws.com

Remember to update this [file](vprofile-project/src/main/resources/application.properties) with the above login details including username, password and port number details.

### Beanstalk deployment

* EC2 IAM role that have beanstalk permission
* Also note that a default IAM role will be created for beanstalk
* [Deployment policies of beanstalk](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.rolling-version-deploy.html)
* Edit the ACL permission of the s3 bucket created by beanstalk so that account user or owner can have access to it, although, for now acl is enabled by default on the bucket.
* Open 3306,11211, 5672, for the backend security group to allow traffic from beanstalk security group that was created during beanstalk deployment.

* Within Beanstalk we have: 
    * Application load balancer
    * Tomcat server in an autoscaling group



* Finally, upload the source code (vprofile-v2.war), after building with mavin on local machine.

**Final project output**

![Project-output](Finalprojectsite2.png)

![Project-output2](Finalprojectsite2.png)