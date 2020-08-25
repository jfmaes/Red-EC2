# Ansible-Red-EC2
Deploy RedTeam Specific EC2 via ansible.

## Requirements

To use this ansible role you'll need to have ansible installed (kinda obvious there)
Furthermore you'll need to have python3 and boto installed
```
pip3 install boto boto3

```

You will also need to have an AWS IAM user that has the capabilities of full control over EC2 instances
for more info on how to do that, please read the documentation over at AWS: https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console

This role does **NOT** automatically generate security groups for you. 
Please create security groups inn AWS manually (or use another ansible role other than this one) 


## Caveats
Although this module is capable of provisioning multiple EC2 instances of the same type at once, the module is limited into naming convention. As a result, spinning up more than one instance of any type (for example 2 redirectors) will mean that these instances will have the same name in the EC2 console (which has no limit on the operational side of things)

## Usage
This role is intended to be ran on localhost, the role will provision EC2 instances and generate an in memory inventory file that can be used to provision the instances with other roles. <br>

The dynamic inventory will look like this: <br>
C2 instances will be added by public IP to the C2 group <br>
redirectors will be added by public IP to the Redirector group <br>
RedELK instances will be added by public IP to the RedELK group <br>

ALL the instances created by this role will also be added to a group called Dynamically_Created 
This group is used for debugging purposes. 

## Testing
Two test playbooks are included in this repository: <br>
* test_play.md: provisions the EC2 instances, nothing more nothing less
* test_play_dynamic_inventory.md: provisions the EC2 instances and will install cobalt-strike on the C2 hosts.
The dynamic playbook will requires my cobalt-strike ansible role: https://github.com/jfmaes/Ansible-Cobalt-Strike

in order to use the test playbooks, please fill in the required variables in host vars and group vars.

## Role variables

### AWS specific variables
| variable  	| default value  	|  description 	|   	
|:-:	|:-:	|:-:	|	
|   aws_access_key	| N/A   	|  needed to auth to AWS  	|   	   	
| aws_secret_key  	| N/A  	| needed to auth to AWS  	|   	  	
|  ec2_region 	| eu-west-1  	| the AWS region in where to spin up these instances  	|
| ssh_key_dir | N/A | the directory where ansible can store the SSH keys generated |
| ssh_user| kali | the ssh user that ansible will use to connect to the instances (in case this role is being used in addition to other roles) |

### C2 specific variables
| variable  	| default value  	|  description 	|   	
|:-:	|:-:	|:-:	|	
|c2_ami_id| ami-0b7668b6cfbce14eb | the latest kali linux AMI
|c2_key_name| N/A | the name of the SSH key to use to connect to the C2 instances|
|c2_ssh_key| "{{ssh_key_dir}}{{c2_key_name}}.pem" | the full path of the ssh key to use, should not be changed|
|c2_amount| 1 | the amount of C2 instances to spin up | 
|c2_security | N/A | the security group to be used for the C2 instances|

### Redirector specfic variables
| variable  	| default value  	|  description 	|   	
|:-:	|:-:	|:-:	|	
|redir_ami_id| ami-0b7668b6cfbce14eb | the latest kali linux AMI
|redir_key_name| N/A | the name of the SSH key to use to connect to the C2 instances|
|redir_ssh_key| "{{ssh_key_dir}}{{c2_key_name}}.pem" | the full path of the ssh key to use, should not be changed|
|redir_amount| 1 | the amount of redir instances to spin up | 
|redir_security | N/A | the security group to be used for the redirector instances|

### RedELK specfic variables
| variable  	| default value  	|  description 	|   	
|:-:	|:-:	|:-:	|	
|redelk_ami_id| ami-0b7668b6cfbce14eb | the latest kali linux AMI
|redelk_key_name| N/A | the name of the SSH key to use to connect to the C2 instances|
|redelk_ssh_key| "{{ssh_key_dir}}{{c2_key_name}}.pem" | the full path of the ssh key to use, should not be changed|
|redelk_amount| 1 | the amount of redir instances to spin up | 
|redelk_security | N/A | the security group to be used for the RedELK instances|
