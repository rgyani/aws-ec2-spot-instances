# EC2 Spot Instances

An AWS EC2 Spot Instance is an unused EC2 instance which is available for less than the On-Demand price.   
Spot instances are up to 90% cheaper than On-Demand instances, which can significantly reduce your EC2 costs.  
A Spot Price is the hourly rate for a Spot instance.   
AWS sets the Spot price for each instance type in each availability zone based on the evolving supply and demand for Spot instances. Spot instances are cost-effective when you can be flexible with your application’s availability and when your applications can be interrupted after a two-minute warning notification. 

Spot instances are ideal for **stateless, error-tolerant, or flexible applications** like **data analysis, batch jobs, background processing**, and optional tasks. 

These instances are closely integrated with AWS services like **Auto Scaling, EMR, ECS, CloudFormation, Data Pipeline, and AWS Batch**. You can easily combine Spot instances with On-Demand, RI, and Savings Plans instances to optimize workload costs and performance.  


### Spot blocks
The biggest problem of using Spot instances is that, the instances can be reclaimed (terminated) by AWS when capacity is no longer available, the prices exceed your budgeted max rate, or the demand for Spot instances increases.
AWS Introduced **Spot Blocks**, such that Spot instances can be created with a defined duration , which are designed to be uninterrupted and run continuously for the duration you choose. 

However, Spot Instances with a defined duration (also known as Spot blocks) are **no longer available to new customers from July 1, 2021.** For customers who have previously used the feature, AWS will continue to support Spot Instances with a defined duration until December 31, 2022.

### Spot instances bidding
Currently, You don’t have to bid for Spot Instances in the new pricing model, and you just pay the Spot price that’s in effect for the current hour for the instances that you launch. Now you can request Spot capacity just like you would request On-Demand capacity, without having to spend time analyzing market prices or setting a maximum bid price.

Previously, Spot Instances were terminated in ascending order of bids, and the Spot price was set to the highest unfulfilled bid. The market prices fluctuated frequently because of this. In the new model, the Spot prices are more predictable, updated less frequently, and are determined by supply and demand for Amazon EC2 spare capacity, not bid prices. You can find the price that’s in effect for the current hour in the EC2 console.


### Strategy for Using Spot Instances

We should always **Maintain a minimum number of compute resources by launching a core group of On-Demand Instances** and supplementing them with Spot Instances when required.

### Spot Instance Best Practices 
* **Prepare individual instances for interruptions**: The best way to be fault-tolerant is to handle Spot instance interruptions with smooth workload rebalancing. Use EC2 instance rebalance recommendations and Spot Instance interruption notices to avoid feeling any hard cutoffs. 
* **Be flexible in terms of instance types and availability zones**: A Spot instance pool consists of unused EC2 instances with the same instance type and availability zone. You should be flexible about which instance types you request and in which Availability Zones. However, be mindful of **AWS Data Transfer Pricing** to consider the additional cost of data transfers.
* **Use EC2 Auto Scaling groups or Spot Fleet to manage your total capacity**: Spot allows total capacity with vCPUs, storage, storage or network throughput. Auto Scaling groups and Spot Fleet allow you to start and maintain a target capacity and automatically request resources.
* **Use the capacity-optimized allocation strategy**: Allocation strategies in auto-scaling groups help you deploy your target capacity without manually searching for the Spot instance pools with free capacity. 
* **Use proactive capacity balancing**: Capacity rebalancing helps maintain availability by adding a new Spot instance to the fleet before a running Spot instance receives the two-minute notification. It balances the capacity-optimized allocation strategy and the policy of mixed entities.
* **Use integrated AWS services to manage your Spot instances**: Other AWS services like EMR, ECS, AWS Batch, EKS, SageMaker, AWS Elastic Beanstalk, and GameLift integrate with Spot to reduce total invoice costs without having to manage individual instances or fleets. 


### Spot Instance Interruptions
There is a possibility that your Spot instance will be interrupted, so you need to make sure that your application is prepared to handle an interruption. 

### Instance Rebalance Recommendations
An EC2 Instance Rebalance Recommendations is a signal that notifies you when a Spot Instance is at high risk of interruption. The notification comes earlier than the two-minute interruption of the Spot instance, giving you the ability to manage the Spot instance proactively. This allows you to rebalance your workload across new or existing Spot instances that are not at a high risk of interruption.
However,
* It is not always possible for Amazon EC2 to send the rebalance recommendation signal before the two-minute Spot Instance interruption notice. Therefore, the rebalance recommendation signal can arrive along with the two-minute interruption notice.
* Rebalance recommendations are made available as a CloudWatch event and as an item in the instance metadata on the Spot Instance. Events are emitted on a best effort basis.

#### Rebalance actions you can take
These are some of the possible rebalancing actions that you can take:
* Graceful shutdown  
When you receive the rebalance recommendation signal for a Spot Instance, you can start your instance shutdown procedures, which might include ensuring that processes are completed before stopping them. For example, you can upload system or application logs to Amazon Simple Storage Service (Amazon S3), you can shut down Amazon SQS workers, or you can complete deregistration from the Domain Name System (DNS). You can also save your work in external storage and resume it at a later time.
* Prevent new work from being scheduled   
When you receive the rebalance recommendation signal for a Spot Instance, you can prevent new work from being scheduled on the instance, while continuing to use the instance until the scheduled work is completed.
* Proactively launch new replacement instances  
You can configure Auto Scaling groups, EC2 Fleet, or Spot Fleet to automatically launch replacement Spot Instances when a rebalance recommendation signal is emitted.
