# Active-Directory

AWS CloudFormation script that creates a Microsoft ActiveDirectory using the AWS Directory Service within a private VPC, and maintains
a running Windows server (that is joined to the AD Domain), within a second, peered VPC.  The second VPC can either be private or
public (which allows direct connection).  If public, then an Elastic IP is created and associated with the Windows server, to allow
easy connectivity.

The aim of the Windows server is to provide a management server for the ActiveDirectory.

The route tables and security groups ensure minimal access to the environment, other than to the Active Directory itself.

The script creates the following:

![alt text](https://github.com/gford1000-aws/active-directory/blob/master/Active-Directory%20Design.png "Script per designer")

Notes:

1. This script has been tested with the Microsoft Windows Server 2016 Base AMI (ami-8de6f7ee in ap-southeast-2)
2. An m4.large is recommended as the smallest EC2 Instance Type to use for the Windows server - t2.xxx get credit exhaustion too quickly
3. The Windows server will reboot to ensure the Domain has been joined correctly
4. The Windows server is managed via an Autoscaling Group, which triggers a Lambda function to associate the EIP, if the VPC is public.
5. VPC details for both the ActiveDirectory VPC and management VPC are output, to allow further peering with other VPCs.


## Arguments

| Argument            | Description                                                                          |
| ------------------- |:------------------------------------------------------------------------------------:|
| AdminPassword       | The password of the Admin account of the AD                                          |
| ADCidrAddress       | The initial X.Y of the CIDR X.Y.0.0/16 for the Active Directory VPC                  |
| ADMgrCidrAddress    | The initial X.Y of the CIDR X.Y.0.0/16 for the VPC of the Windows server             |
| AllowedCIDRRange    | The CIDR address block that can access the VPC of the Windows server                 |
| AllowIngressToADMgr | If true, the the VPC of the Windows server is public, otherwise it is private        |
| DomainName          | The Domain name that the Active Directory will manage                                |
| InstanceAmiId       | The AMI of a Windows 2016 OS build, to be used for the Windows server                |
| InstanceType        | The EC2 Instance Type to use for the Windows server                                  |
| KeyName             | The KeyName with which to retrieve the Administrator password for the Windows server |
| VPCTemplateURL      | The S3 URL to the VPC template                                                       |


## Outputs

| Output                        | Description                                        |
| ----------------------------- |:--------------------------------------------------:|
| ActiveDirectoryIPAddresses    | The pair of IP addresses for the ActiveDirectory   |
| ActiveDirectoryVPC            | The VPC containing the ActiveDirectory             |
| ActiveDirectoryVPCCidrBlock   | The CIDR block of the VPC for the ActiveDirectory  |
| ActiveDirectoryVPCRouteTable  | The route table of the VPC for the ActiveDirectory |
| ManagerEIP                    | The Elastic IP of the management Windows server    |
| ManagerVPC                    | The VPC of the management Windows server           |
| ManagerVPCCidrBlock           | The CIDR block of the VPC of the management server |
| ManagerVPCRouteTable          | The route table of the VPC of the mgmt server      |

## Licence

This project is released under the MIT license. See [LICENSE](LICENSE) for details.
