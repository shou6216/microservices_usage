# マイクロサービス学習用プロジェクト

## 目的

* 概念や思想をちゃんと理解する
* 不向きな部分、デメリットも抑える
* Spring Framework、AWSの構築方法を習得する

## マイクロサービス

### 1. マイクロサービスとは

* **ビジネスドメインに基づいてモデル化** された、 **独立してデプロイ可能** なサービス
* サービス同士はネットワークを介して相互に通信
* サービス指向アーキテクチャ（SOA）の一種
* **新しい技術を使う必要はない。** どんな言語で、どんなプラットフォームでも良い

#### 1.1. 独立デプロイ可能性

* 他のサービスに影響を与えることなくデプロイできる
* サービス同士が疎結合

#### 1.2. ビジネスドメインに基づくモデル化

##### モノリシック（従来は）

* 3層アーキテクチャ（Web、App、DB）に対して、層ごと開発チームを形成していた
    * 機能ではなく技術のまとまりでチームを形成
    * DBは機能やチーム間で共有することが多いため、変更の影響が大きい

##### マイクロサービス

* 技術ではなく機能のまとまりでチームを形成する
* サービスのカプセル化が可能
    * サービス内に小さな3層アーキテクチャができる   
    * 他サービスへの影響を減らし、変更が容易になる
    * **DBの共有は、独立デプロイ可能性を実現するうえで一番の弊害**

#### 1.3. マイクロサービスの利点

* 各サービスの作業を並行して行える
* 1機能=1サービスなので、担当サービスの理解が容易
* サービスごと異なる言語、DB、開発手法の選択が可能

#### 1.4. マイクロサービスの課題

* サービス間のやりとりがネットワーク通信になるため、レイテンシーやネットワーク障害をより考慮する必要がある

#### 1.5. マイクロサービスのポイント

* 段階的にマイクロサービス化する。一気に大量のマイクロサービスを作らない
* 1サービスは小規模で
* 技術単位ではなく、機能単位の組織づくり
    * プロダクトオーナーや運用チームは、各々サービスチームごと配置
    * それらサービスを束ねる技術リーダーは別途設ける

### [2. モノリス（モノリシック）とは](doc/2.monolithic.md)

### [3. 結合と凝集](doc/3.coupling_cohesion.md)

### [4. 移行計画](doc/4.migration_plan.md)

### [5. モノリスの分割](doc/5.monolithic_division.md)

### [6. データベースの分割](doc/6.database_division.md)

### [7. その他](doc/7.others.md)

## 構築

### Sock Shop

* マイクロサービスのデモサイト
* https://microservices-demo.github.io/
    * [Github](https://github.com/microservices-demo)
* Java、Go、Python、C#などいろんな言語のマイクロサービスがある
* `docker-compose`すると各自環境で起動できる

### AWS

* [AWS App Mesh](https://aws.amazon.com/jp/app-mesh/)
* マイクロサービスで必要なサービス間の通信を管理するためのサービス
   * 大規模なマイクロサービスだと必須
* 以下 **構築するためにはAWSアカウントおよび環境が必要**
* https://www.youtube.com/watch?v=Nmgl2LhezTc

#### AWSコンソールで構築

* https://docs.aws.amazon.com/app-mesh/latest/userguide/getting-started-ecs.html

##### シナリオ

* `serviceA`と`serviceB`の2つのサービスがある


Scenario
To illustrate how to use App Mesh, assume that you have an application with the following characteristics:

Consists of two services named serviceA and serviceB.

Both services are registered to a namespace named apps.local.

ServiceA communicates with serviceB over HTTP/2, port 80.

You have already deployed version 2 of serviceB and registered it with the name serviceBv2 in the apps.local namespace.

You have the following requirements:

You want to send 75 percent of the traffic from serviceA to serviceB and 25 percent of the traffic to serviceBv2 to validate that serviceBv2 is bug free before you send 100 percent of the traffic from serviceA to it.

You want to be able to easily adjust the traffic weighting so that 100 percent of the traffic goes to serviceBv2 once it is proven to be reliable. Once all traffic is being sent to serviceBv2, you want to discontinue serviceB.

You do not want to have to change any existing application code or service discovery registration for your actual services to meet the previous requirements.

To meet your requirements, you have decided to create an App Mesh service mesh with virtual services, virtual nodes, a virtual router, and a route. After implementing your mesh, you update your services to use the Envoy proxy. Once updated, your services communicate with each other through the Envoy proxy rather than directly with each other.

Prerequisites
An existing understanding of App Mesh concepts. For more information, see What Is AWS App Mesh?.

An existing understanding of Amazon ECSs concepts. For more information, see What is Amazon ECS in the Amazon Elastic Container Service Developer Guide.

App Mesh supports Linux services that are registered with DNS, AWS Cloud Map, or both. To use this getting started guide, we recommend that you have three existing services that are registered with DNS. The procedures in this topic assume that the existing services are named serviceA, serviceB, and serviceBv2 and that all services are discoverable through a namespace named apps.local.

You can create a service mesh and its resources even if the services don't exist, but you cannot use the mesh until you have deployed actual services. For more information about service discovery on Amazon ECS, see Service Discovery. To create an Amazon ECS service with service discovery, see Tutorial: Creating a Service Using Service Discovery. If you don't already have services running, you can Create an Amazon ECS service with service discovery.

Step 1: Create a mesh and virtual service
A service mesh is a logical boundary for network traffic between the services that reside within it. For more information, see Service Meshes. A virtual service is an abstraction of an actual service. For more information, see Virtual services.

Create the following resources:

A mesh named apps, since all of the services in the scenario are registered to the apps.local namespace.

A virtual service named serviceb.apps.local, since the virtual service represents a service that is discoverable with that name, and you don't want to change your code to reference another name. A virtual service named servicea.apps.local is added in a later step.

You can use the AWS Management Console or the AWS CLI version 1.18.116 or higher or 2.0.38 or higher to complete the following steps. If using the AWS CLI, use the aws --version command to check your installed AWS CLI version. If you don't have version 1.18.116 or higher or 2.0.38 or higher installed, then you must install or update the AWS CLI. Select the tab for the tool that you want to use.

AWS Management Console
AWS CLI
Open the App Mesh console first-run wizard at https://console.aws.amazon.com/appmesh/get-started.

For Mesh name, enter apps.

For Virtual service name, enter serviceb.apps.local.

To continue, choose Next.

Step 2: Create a virtual node
A virtual node acts as a logical pointer to an actual service. For more information, see Virtual nodes.

Create a virtual node named serviceB, since one of the virtual nodes represents the actual service named serviceB. The actual service that the virtual node represents is discoverable through DNS with a hostname of serviceb.apps.local. Alternately, you can discover actual services using AWS Cloud Map. The virtual node will listen for traffic using the HTTP/2 protocol on port 80. Other protocols are also supported, as are health checks. You will create virtual nodes for serviceA and serviceBv2 in a later step.

AWS Management Console
AWS CLI
For Virtual node name, enter serviceB.

For Service discovery method, choose DNS and enter serviceb.apps.local for DNS hostname.

Under Listener configuration, choose http2 for Protocol and enter 80 for Port.

To continue, choose Next.

Step 3: Create a virtual router and route
Virtual routers route traffic for one or more virtual services within your mesh. For more information, see Virtual routers and Routes.

Create the following resources:

A virtual router named serviceB, since the serviceB.apps.local virtual service does not initiate outbound communication with any other service. Remember that the virtual service that you created previously is an abstraction of your actual serviceb.apps.local service. The virtual service sends traffic to the virtual router. The virtual router will listen for traffic using the HTTP/2 protocol on port 80. Other protocols are also supported.

A route named serviceB. It will route 100 percent of its traffic to the serviceB virtual node. You will change the weight in a later step once you have added the serviceBv2 virtual node. Though not covered in this guide, you can add additional filter criteria for the route and add a retry policy to cause the Envoy proxy to make multiple attempts to send traffic to a virtual node when it experiences a communication problem.

AWS Management Console
AWS CLI
For Virtual router name, enter serviceB.

Under Listener configuration, choose http2 for Protocol and specify 80 for Port.

For Route name, enter serviceB.

For Route type, choose http2.

For Virtual node name under Target configuration, select serviceB and enter 100 for Weight.

Under Match configuration, choose a Method.

To continue, choose Next.

Step 4: Review and create
Review the settings against the previous instructions.

AWS Management Console
AWS CLI
Choose Edit if you need to make changes in any section. Once you are satisfied with the settings, choose Create mesh.

The Status screen shows you all of the mesh resources that were created. You can see the created resources in the console by selecting View mesh.

Step 5: Create additional resources
To complete the scenario, you need to:

Create one virtual node named serviceBv2 and another named serviceA. Both virtual nodes listen for requests over HTTP/2 port 80. For the serviceA virtual node, configure a backend of serviceb.apps.local, since all outbound traffic from the serviceA virtual node is sent to the virtual service named serviceb.apps.local. Though not covered in this guide, you can also specify a file path to write access logs to for a virtual node.

Create one additional virtual service named servicea.apps.local, which will send all traffic directly to the serviceA virtual node.

Update the serviceB route that you created in a previous step to send 75 percent of its traffic to the serviceB virtual node and 25 percent of its traffic to the serviceBv2 virtual node. Over time, you can continue to modify the weights until serviceBv2 receives 100 percent of the traffic. Once all traffic is sent to serviceBv2, you can shut down and discontinue the serviceB virtual node and actual service. As you change weights, your code does not require any modification, because the serviceb.apps.local virtual and actual service names don't change. Recall that the serviceb.apps.local virtual service sends traffic to the virtual router, which routes the traffic to the virtual nodes. The service discovery names for the virtual nodes can be changed at any time.

AWS Management Console
AWS CLI
In the left navigation pane, select Meshes.

Select the apps mesh that you created in a previous step.

In the left navigation pane, select Virtual nodes.

Choose Create virtual node.

For Virtual node name, enter serviceBv2, for Service discovery method, choose DNS, and for DNS hostname, enter servicebv2.apps.local.

For Listener configuration, select http2 for Protocol and enter 80 for Port.

Choose Create virtual node.

Choose Create virtual node again. Enter serviceA for the Virtual node name. For Service discovery method, choose DNS, and for DNS hostname, enter servicea.apps.local.

For Enter a virtual service name under New backend, enter servicea.apps.local.

Under Listener configuration, choose http2 for Protocol, enter 80 for Port, and then choose Create virtual node.

In the left navigation pane, select Virtual routers and then select the serviceB virtual router from the list.

Under Routes, select the route named ServiceB that you created in a previous step, and choose Edit.

Under Targets, Virtual node name, change the value of Weight for serviceB to 75.

Choose Add target, choose serviceBv2 from the dropdown list, and set the value of Weight to 25.

Choose Save.

In the left navigation pane, select Virtual services and then choose Create virtual service.

Enter servicea.apps.local for Virtual service name, select Virtual node for Provider, select serviceA for Virtual node, and then choose Create virtual service.

Mesh summary

Before you created the service mesh, you had three actual services named servicea.apps.local, serviceb.apps.local, and servicebv2.apps.local. In addition to the actual services, you now have a service mesh that contains the following resources that represent the actual services:

Two virtual services. The proxy sends all traffic from the servicea.apps.local virtual service to the serviceb.apps.local virtual service through a virtual router.

Three virtual nodes named serviceA, serviceB, and serviceBv2. The Envoy proxy uses the service discovery information configured for the virtual nodes to look up the IP addresses of the actual services.

One virtual router with one route that instructs the Envoy proxy to route 75 percent of inbound traffic to the serviceB virtual node and 25 percent of the traffic to the serviceBv2 virtual node.

Step 6: Update services
After creating your mesh, you need to complete the following tasks:

Authorize the Envoy proxy that you deploy with each Amazon ECS task to read the configuration of one or more virtual nodes. For more information about how to authorize the proxy, see Proxy authorization.

Update each of your existing Amazon ECS task definitions to use the Envoy proxy.

Credentials

The Envoy container requires AWS Identity and Access Management credentials for signing requests that are sent to the App Mesh service. For Amazon ECS tasks deployed with the Amazon EC2 launch type, the credentials can come from the instance role or from a task IAM role. Amazon ECS tasks deployed with the Fargate launch type don't have access to the Amazon EC2 metadata server that supplies instance IAM profile credentials. To supply the credentials, you must attach an IAM task role to any tasks deployed with the Fargate launch type.

If a task is deployed with the Amazon EC2 launch type and access is blocked to the Amazon EC2 metadata server, as described in the Important annotation in IAM Role for Tasks, then a task IAM role must also be attached to the task. The role that you assign to the instance or task must have an IAM policy attached to it as described in Proxy authorization.

To update your task definitions using the AWS Management Console

The following steps only show updating the taskB task for the scenario. You also need to update the taskBv2 and taskA tasks by changing the values appropriately.

Open the Amazon ECS console at https://console.aws.amazon.com/ecs/.

From the navigation bar, choose the Region that contains your task definition.

In the navigation pane, choose Task Definitions.

On the Task Definitions page, select the box to the left of the task definition to revise. From the pre-requisites and previous steps, you might have task definitions named taskA, taskB, and taskBv2. Select taskB and choose Create new revision.

On the Create new revision of Task Definition page, make the following changes to enable App Mesh integration.

For Service Integration, to configure the parameters for App Mesh integration choose Enable App Mesh integration and then do the following:

For Application container name, choose the container name to use for the App Mesh application. This container must already be defined within the task definition.

For Envoy image, complete the following task and enter the value that is returned.

All supported Regions other than me-south-1 and ap-east-1, eu-south-1, af-south-1. You can replace Region-code with any Region other than me-south-1 and ap-east-1, eu-south-1, af-south-1.

840364872350.dkr.ecr.region-code.amazonaws.com/aws-appmesh-envoy:v1.19.1.0-prod
me-south-1 Region:

772975370895.dkr.ecr.me-south-1.amazonaws.com/aws-appmesh-envoy:v1.19.1.0-prod
ap-east-1 Region:

856666278305.dkr.ecr.ap-east-1.amazonaws.com/aws-appmesh-envoy:v1.19.1.0-prod
eu-south-1 Region:

422531588944.dkr.ecr.eu-south-1.amazonaws.com/aws-appmesh-envoy:v1.19.1.0-prod
af-south-1 Region:

924023996002.dkr.ecr.af-south-1.amazonaws.com/aws-appmesh-envoy:v1.19.1.0-prod
Public repository

public.ecr.aws/appmesh/aws-appmesh-envoy:v1.19.1.0-prod
Important
Only version v1.9.0.0-prod or later is supported for use with App Mesh.

For Mesh name, choose the App Mesh service mesh to use. In this topic, the name of the mesh that was created is apps.

For Virtual node name, choose the App Mesh virtual node to use. For example, for the taskB task, you would choose the serviceB virtual node that you created in a previous step.

The value for Virtual node port is pre-populated with the listener port that you specified when you created the virtual node.

Choose Apply, and then choose Confirm. A new Envoy proxy container is created and added to the task definition, and the settings to support the container are also created. The Envoy proxy container then pre-populates the App Mesh Proxy Configuration settings for the next step.

For Proxy Configuration, verify all of the pre-populated values.

For Network Mode, make sure that awsvpc is selected. To learn more about the awsvpc network mode, see Task Networking with the awsvpc Network Mode.

Choose Create.

Update your service with the updated task definition. For more information, see Updating a service.

The console creates the task definition's json specification. You can modify some of the settings, but not others. for more information, expand the following section.

Task definition json

#### CLI + CloudFormationで構築

* AWSワークショップ及びサンプルを元に構築
   * **実行環境を直接見てもらおうと思ったけど、稼働し続けてたら料金発生してしまったので止めます**

* https://github.com/harunobukameda/AWS-App-Mesh-AWS-Cloud-Map
* https://github.com/aws/aws-app-mesh-examples/tree/master/examples

## 参考

* https://www.oreilly.co.jp/books/9784873119311/
