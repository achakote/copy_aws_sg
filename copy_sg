import boto3, json

source_sg_list = [ "sg-23342sad", "sg-sdfsf23", "sg-23424dwsdsa" ]
dest_sg_list   = [ "sg-12sd3131", "sg-sda324", "sg-3612535f" ]
source_region = "eu-west-1"
dest_region = "eu-west-2"
i = 0

client = boto3.client('ec2', region_name=source_region)
dest_client = boto3.client('ec2', region_name=dest_region)

while i != len(source_sg_list):
        source_sg = source_sg_list[i]
        dest_sg = dest_sg_list[i]

        print(source_sg, dest_sg)
        dsg = boto3.resource('ec2', region_name=dest_region).SecurityGroup(dest_sg)
        if dsg.ip_permissions:
              dsg.revoke_ingress(IpPermissions=dsg.ip_permissions)
        sg_details = client.describe_security_groups(GroupIds=[source_sg])

        ippermissions = []
        for permission in sg_details['SecurityGroups'][0]['IpPermissions']:
            for iprange in permission['IpRanges']:
                cidr = iprange['CidrIp']
                from_port = permission['FromPort']
                to_port  = permission['ToPort']
                protocol = permission['IpProtocol']
                if 'Description' in iprange:
                    description = iprange['Description']
                else:
                    description = ""
                print(protocol, from_port, to_port, cidr, description)
                item = {'IpRanges':[{'CidrIp': cidr,'Description':description}],'FromPort':from_port,'IpProtocol':protocol,'ToPort':to_port}
                ippermissions.append(item)
        try:
           response = dest_client.authorize_security_group_ingress( GroupId=dest_sg, IpPermissions=ippermissions )
        except Exception as e:
           print(e)

        for permission in sg_details['SecurityGroups'][0]['IpPermissionsEgress']:
            for iprange in permission['IpRanges']:
                cidr = iprange['CidrIp']
                from_port = permission['FromPort']
                to_port  = permission['ToPort']
                protocol = permission['IpProtocol']
                if 'Description' in iprange:
                    description = iprange['Description']
                else:
                    description = ""
                print(protocol, from_port, to_port, cidr, description)
                item = {'IpRanges':[{'CidrIp': cidr,'Description':description}],'FromPort':from_port,'IpProtocol':protocol,'ToPort':to_port}
                ippermissions.append(item)
        try:
           response = dest_client.authorize_security_group_egress( GroupId=dest_sg, IpPermissions=ippermissions )
        except Exception as e:
           print(e)
        i += 1
