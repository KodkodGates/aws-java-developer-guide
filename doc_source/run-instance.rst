.. Copyright 2010-2016 Amazon.com, Inc. or its affiliates. All Rights Reserved.

   This work is licensed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0
   International License (the "License"). You may not use this file except in compliance with the
   License. A copy of the License is located at http://creativecommons.org/licenses/by-nc-sa/4.0/.

   This file is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
   either express or implied. See the License for the specific language governing permissions and
   limitations under the License.

#####################
Run an |EC2| Instance
#####################

Use the following procedure to launch one or more identically configured EC2 instances from the same
Amazon Machine Image (AMI). After you create your EC2 instances, you can check their status. After
your EC2 instances are running, you can connect to them.

**To launch an Amazon EC2 instance**

1.  Create and initialize a :java-api:`RunInstancesRequest <services/ec2/model/RunInstancesRequest>`
    instance. Make sure that the AMI, key pair, and security group that you specify exist in the
    region that you specified when you created the client object.

    .. code-block:: java

        RunInstancesRequest runInstancesRequest =
              new RunInstancesRequest();

          runInstancesRequest.withImageId("ami-4b814f22")
                             .withInstanceType("m1.small")
                             .withMinCount(1)
                             .withMaxCount(1)
                             .withKeyName("my-key-pair")
                             .withSecurityGroups("my-security-group");

    :java-ref:`withImageId <com/amazonaws/services/ec2/model/RunInstancesRequest.html#withImageId(java.lang.String)>`
        The ID of the AMI. For a list of public AMIs provided by Amazon, see Amazon Machine Images.

    :java-ref:`withInstanceType <com/amazonaws/services/ec2/model/RunInstancesRequest.html#withInstanceType(java.lang.String)>`
        An instance type that is compatible with the specified AMI. For more information, see
        :ec2-ug:`Instance Types <instance-types>` in the |EC2-ug|.

    :java-ref:`withMinCount <com/amazonaws/services/ec2/model/RunInstancesRequest.html#withMinCount(java.lang.Integer)>`
        The minimum number of EC2 instances to launch. If this is more instances than |EC2| can
        launch in the target Availability Zone, |EC2| launches no instances.

    :java-ref:`withMaxCount <com/amazonaws/services/ec2/model/RunInstancesRequest.html#withMaxCount(java.lang.Integer)>`
        The maximum number of EC2 instances to launch. If this is more instances than |EC2| can
        launch in the target Availability Zone, |EC2| launches the largest possible number of
        instances above :code:`MinCount`. You can launch between 1 and the maximum number of
        instances you're allowed for the instance type. For more information, see How many instances
        can I run in Amazon EC2 in the |EC2| General FAQ.

    :java-ref:`withKeyName <com/amazonaws/services/ec2/model/RunInstancesRequest.html#withKeyName(java.lang.String)>`
        The name of the EC2 key pair. If you launch an instance without specifying a key pair, you
        can't connect to it. For more information, see :doc:`create-key-pair`.

    :java-ref:`withSecurityGroups <com/amazonaws/services/ec2/model/RunInstancesRequest.html#withSecurityGroups(java.util.Collection)>`
        One or more security groups. For more information, see :doc:`create-security-group`.

2.  Launch the instances by passing the request object to the :java-ref:`runInstances
    <com/amazonaws/services/ec2/AmazonEC2Client.html#runInstances(com.amazonaws.services.ec2.model.RunInstancesRequest)>`
    method. The method returns a :java-api:`RunInstancesResult
    <services/ec2/model/RunInstancesResult>` object, as follows:

    .. code-block:: java

        RunInstancesResult runInstancesResult =
              amazonEC2Client.runInstances(runInstancesRequest);

After your instance is running, you can connect to it using your key pair. For more information, see
:ec2-ug:`Connect to Your Linux Instance <AccessingInstances>`. in the |EC2-ug|.

