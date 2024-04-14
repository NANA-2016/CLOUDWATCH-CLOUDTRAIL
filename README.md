# CLOUDWATCH-CLOUDTRAIL


## Monitoring infrastructure in AWS.(Cloudwatch and cloud trail)

This is to help you monitor your systemperformance,security and aperational health.

### Create an AIM Role.

Policies to use 

Cloudwatchfullaccess and SSMFullAccess

![Screenshot 2024-04-13 024140](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/2d4e86d9-6be2-4736-9058-fc72ac69b84f)

![Screenshot 2024-04-13 024603](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/5ddcdd9b-2418-4583-ad55-dd1ecf1d79d1)


![image](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/9b7e0cef-7642-489b-bbd0-83745f391298)


![image](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/310e6a59-cec3-49b8-bfd0-bc8e5f7157e6)

### Create a Parameter in System Manager.

This will enable you to define the metrics you want to monitor in your EC2 instance 

AWS System Manager Console >parameter store >create new parameter > use code below to create the parameters fot the cloud 

watch agent defining the metrics that will be collected from the EC2 instance and sent to cloud watch


{
	"metrics": {
		"append_dimensions": {
			"InstanceId": "${aws:InstanceId}"
		},
		"metrics_collected": {
			"mem": {
				"measurement": [
					"mem_used_percent"
				],
				"metrics_collection_interval": 180
			},
            "disk": {
				"measurement": [
                     "disk_used_percent"
				],
				"metrics_collection_interval": 180
			}
		}
	}
}



code analysis

metrics - Metrics to be collected

append_dimensions - specifies key value pairs that help identify source of data in the cloud watch to be appended to all

collected metrics.Ie instanceId is appended  is appended and its value populated dynamically with the instance ID of the  

EC2 INSTANCE where the cloud watch agent is installed.

"InstanceId": "${aws:InstanceId}" - specifies the  value of instance id 

"metrics_collected"- Specific metrics to be collected  from the EC2 iinstance 

	"mem": -  Memory related metrics to be collected .

	"measurement": - specific memory metrics to be collected.

 "measurement": - specific disk metrics to be collected .

   "disk_used_percent" - disk space used on the instance 

   "metrics_collection_interval": 180 - how frequent the disk metrics will be collected in seconds (180 seconds)

##  EC2 INSTANCE CREATION

  The instance you will create here is the instance you are going to monitor.

   You will attach it to the role already created earlier.

   Instance >Actions >security >Modify IAM role > EC2-role created earlier >Update IAM role


![Screenshot 2024-04-14 153107](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/8576461a-303a-493a-9b13-688c06973af6)

![Screenshot 2024-04-14 153555](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/2b4c7998-9418-4d33-9efa-31f1eb3be058)


![Screenshot 2024-04-14 153639](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/aabcafe9-8207-4cfc-8481-9cb3b4e6726b)


   ## INSTALL CLOUD WATCH AGENT .

##### Create a file script.sh

![image](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/1b69ccea-0d5a-48a3-8c9f-c09af69516b9)


#### create the file content with the scrpit below

#!/bin/bash
wget https://s3.amazonaws.com/amazoncloudwatch-agent/linux/amd64/latest/AmazonCloudWatchAgent.zip
unzip AmazonCloudWatchAgent.zip
sudo ./install.sh
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:/alarm/AWS-CWAgentLinConfig -s


![image](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/2ad93a70-b06d-4893-a427-8746786f2366)


##### make the file executable 

sudo chmod +x script.sh


![image](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/1c156bf9-e1ba-4f29-a558-acd53da66bd1)


##### Save and run the file USING sudo command 

sudo ./script.sh

![image](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/0f50a0fe-722a-46f9-a3be-a73fd4cfdc11)

#####  Install the unzip if it is not already installed then re-run the coomand above  using  the command 

 sudo apt-get install unzip

![image](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/db1e86e7-9c35-4314-bc7d-2621fc5c964b)


#####  Start the cloudwatch agent 

   sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a start

   ![image](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/61408fb0-b1d5-400f-8156-b2a455941f64)


   ##### verify if the cloudwatch is installed and running successfully .

    sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a status

   ![image](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/6ef5b97f-6519-4f35-b201-97452ead9aa5)
 

## MONITORING YOUR METRICS

 ### Create a new policy for Earlier created IAM role

  IAM console >policy >create pilicy > Role created earlier(EC2-role) select it and double click on the role >edit > Add 
 
 permission >Attach policies > select JSON  and add the code provided below after deleting the content already present > Add Policy name .

 {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeTags"
            ],
            "Resource": "*"
        }
    ]
}



![Screenshot 2024-04-14 161127](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/e580104d-8e90-45a8-b746-33212ab3987b)

![Screenshot 2024-04-14 161652](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/3139fdf2-30bd-47ed-86da-8f048b217d77)

![Screenshot 2024-04-14 161739](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/04bb25d7-4b20-4bc4-be0e-a624b3cc16b1)

![Screenshot 2024-04-14 161836](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/3462aa20-b993-472e-ae9e-e557298aba7a)


![Screenshot 2024-04-14 161900](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/803c724a-4351-4dab-9058-d992f36d9552)


## CLOUDWATCH CONSOLE

 Here you are going to see all the metrics you defined earlier fotr the EC2 iinstance you created hence enabling you 
 
 monitor the instance status as defined on the parameter code .



 ##### IE
 

"InstanceId": "${aws:InstanceId}" - specifies the  value of instance id 

"metrics_collected"- Specific metrics to be collected  from the EC2 iinstance 

	"mem": -  Memory related metrics to be collected .

	"measurement": - specific memory metrics to be collected.

 "measurement": - specific disk metrics to be collected .

   "disk_used_percent" - disk space used on the instance 

   "metrics_collection_interval": 180 - how frequent the disk metrics will be collected in seconds (180 seconds)

   

  ##### Cloudwatch console >All Metrics >Browser tab >CWAgent(see the instance created and allthe metrics defined )

  ![Screenshot 2024-04-14 162220](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/2a842deb-5899-4bc9-b9aa-d9140ab734d6)


  ![Screenshot 2024-04-14 162553](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/04db3436-9030-4e78-a8f4-f6a29dd5f5b8)


 ![Screenshot 2024-04-14 162553](https://github.com/NANA-2016/CLOUDWATCH-CLOUDTRAIL/assets/141503408/0eb11812-a7d6-4f48-b520-547e1f5c0be3)

 
 
