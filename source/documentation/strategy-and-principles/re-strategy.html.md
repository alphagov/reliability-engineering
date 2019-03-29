# RE Strategy

<b>This is the Reliability Engineering Strategy for 2019-2020 which aims to set a direction and provide guidance for RE to meet its objectives for convergence and efficiency.</b>

<i>Version: 1.0.1 - 2019/03/29</i>

## 1. Prefer single-cloud over multi-cloud architectures
RE focuses on one cloud hosting provider (AWS) but with an eye to remaining as agnostic as possible and tracking any switching costs. We believe using a broad range of tools provided by a single cloud provider is preferable to the lowest-common-denominator approach required by designing for multi-cloud environments.

<i><small>This re-emphasises the same point from the TechOps Principles:

[Prefer single cloud over multi-cloud architectures](re-principles.html#2-prefer-single-cloud-over-multi-cloud-architectures)

At present, AWS is our preferred cloud hosting provider as we have a large number of services running there already and we get economies of scale, consistent enterprise support and other advantages as a result of this consolidation.</small></i>

## 2. Build a new common hosting platform for GDS services
RE exists to help GDS services be more reliable, robust and secure at reasonable cost. We minimise hosting costs while enabling GDS delivery teams to deliver their services faster, safely and securely.

RE is standardising the way it hosts and supports those services. We use RE expertise efficiently to provide as much value as possible without having to scale our support effort.

RE will use Amazon EKS (Kubernetes) as our standard container scheduler and Amazon Fargate as our common container runtime. EKS on Fargate meets our strategic principles:

* Use fully-managed cloud-hosting services whenever possible
* Target a single cloud provider for all our platforms
* Develop, build, test and deploy with containers

By converging on EKS on Fargate we can focus our resources on improving the reliability of GDS’ major services and helping delivery teams deliver faster rather than on building our own platforms or managing virtual servers.

Using EKS as our container scheduler, as it is Kubernetes-based, has the added benefit of providing a layer of abstraction that would allow us to move more easily to another cloud hosting provider, should the need arise, whilst still enjoying the benefit of an Amazon-native service.

Kubernetes as a container orchestration environment has become an industry standard over the last few years, has huge community support, and is widely adopted in government and the private sector. There are benefits in efficiency, portability, fault-tolerance and reliability.

<i><small>This was the only specific technology called out in the strategy explicitly. EKS and Fargate is a long-term strategic goal over the next 2 years. While it is still unavailable from AWS, there’s good reason to believe that Fargate for EKS will be available well within the lifetime of the strategy and there’s a lot of work we can do to mature the platform on EKS with EC2 before Fargate support becomes available.

It is expected the path to getting there will be as follows:

<b>Kops on EC2 --> EKS and EC2 --> EKS with Fargate</b>

rather than using ECS/Fargate as an intermediate step as has been proposed previously.

The principle we’re invoking here says [use fully managed cloud services wherever possible](re-principles.html#3-use-fully-managed-cloud-services-by-default).

This combined with the fact that we’re committing to AWS for the period of this strategy means EKS and Fargate is a clear choice if we are committed to Kubernetes as a technology.

There will be cases where it will be necessary to run container workloads on EKS+EC2 and this decision will have to be made program by program depending on their requirements. We will remain as pragmatic as always, we will use this flexible approach to build a platform that works best for GDS services. The principle says "wherever possible" and there will be times when it is not possible and we must manage certain parts of the platform ourselves.

If fully-managed Kubernetes in any form (GKE, AKS or EKS/Fargate) is not suitable for any of our services then we must of course update and change our strategy. This is an aspirational document that represents our best intentions but it is not immutable. GDS is an agile organisation.</small></i>

## 3. Continue to invest in the GOV.UK Platform as a Service (PaaS), expanding its reach across the public sector
[GOV.UK Platform as a Service (PaaS)](https://cloud.service.gov.uk) is used by a number of GDS services and other government departments. We will continue to invest in this platform for services that have less need for operational support or where the service does not have complex architectural requirements or bespoke security concerns.

<i><small>GOV.UK PaaS is an extremely important part of GDS’s offering to government, has a vital role to play and is a unique selling point that any new platform will not be able to replace anytime soon - if ever. It has a clear roadmap, and is on-boarding new services.

GOV.UK PaaS has benefited greatly from hosting large, complex services (Notify, Digital Marketplace, Registers etc.) and we are hoping that by focusing the other large GDS services (GOV.UK, Pay and Verify) on the new platform, it will also benefit from this “dogfooding” approach. There is no intention to move any GDS products away from the PaaS and we may never do so, because the cost may not justify the process. If, however, there is a compelling argument to move something off the PaaS and on to the new platform then it will be considered.

PaaS is very suitable for smaller, less complex live services as well as prototypes, alphas etc. where the cost or overhead of running a dedicated Kubernetes cluster does not offer good value.</small></i>

## 4. Host all live GDS services on a single GDS supported fully managed platform by 2021
RE is:

* converging towards a single common platform for hosting GDS services
* adopting a standard approach to providing operational support to GDS service teams with a common approach to deployment, monitoring, logging, metrics, alerting, emergency response and incident handling
* providing standard developer tools to make it easier for development teams to build and support their services

<i><small>This strategy makes clear that GDS continues to consolidate its various programs and services towards one platform. This makes sense as an organisation grows for a number of reasons:

* The engineers responsible for the management of those services do not need to learn multiple technologies and variations in operation across the GDS estate. An engineer familiar with running, for instance, GOV.UK’s service can more easily also work on Verify’s service because they use the same platform and tooling. Similarly, a developer working on GOV.UK can more easily work on Pay because the developer experience is consistent. This reduces the number of specialisations in the engineering team and means support of the whole estate can more easily be shared by the whole of RE instead of siloed groups of support responsibility.
* A problem solved, feature added or performance improvement made on one service can be easily applied and rolled out to all services that share that same platform.

While the new platform is based upon Kubernetes with EKS/Fargate, that is not the platform on its own. The single GDS supported platform consists of a number of tools running on top and alongside:

* continuous integration and deployment pipeline
* container registry
* metrics, monitoring and alerting
* logging and tracing
* secrets management
* protective monitoring

and so on.

Deployments to the PaaS are not excluded here for those GDS services running on it and as such can be considered part of this platform. There is great value in, for instance, Notify or Digital Marketplace adopting the elements of the platform listed above and retaining their live environments on PaaS.</small></i>

## 5. Promote and use Continuous Deployment for GDS services
RE promotes and uses continuous deployment for GDS services.

The benefits of this to GDS are:

* Delivery teams can quickly deliver code into production.
* Individual deployments are smaller, less risky and easier to diagnose when problems occur.
* Deployments are controlled by GitHub pull requests and reviews. The workflow around GitHub reviews is well understood and can be enforced declaratively in code.
* Developers do not need to interact with CI/CD systems to trigger deployments, which means there are fewer credentials to manage and less need for deployer nodes to be accessible. It also removes the need for developers to carry out deployment tasks, freeing them up to focus on their services.
* Easier user and access management.

The GDS Supported Platform has a CI/CD toolset that implements this paradigm for use across the whole of GDS.

## 6. Use a shared responsibility model between RE and service delivery teams
Clear separation of responsibility is important for program teams running services on RE’s platform. Some aspects of the service must be managed and supported by the program itself and cannot be RE’s sole responsibility. Similarly the program team can not be responsible for those systems managed by RE. This boundary is clearly defined in the shared responsibility model.

See: [DRAFT Shared Responsibility Model PULL REQUEST](https://github.com/alphagov/gsp-team-manual/pull/22)

<i><small>This is not an attempt to create a barrier between programs and RE, commonly known as the “throw it over the wall” approach. We recognise that we are collectively responsible for the success of the service but there are areas of responsibility that need to be made clear, to avoid the finger-pointing of “it’s not my problem, ask them” but also to make visible all aspects of running a service.</small></i>

## 7. Full service stacks deployed as Functions as a Service (FaaS) are not yet supported
At the time of writing there are no cross-provider serverless standards for FaaS (Functions as a Service); therefore we are not basing a technical strategy around it at this time. We will review this decision regularly to determine whether this approach is viable for the future.

GDS has supporting services running on FaaS platforms, mainly AWS Lambda. RE supports AWS Lambda for supporting services that do not directly handle user requests, such as deployment or notification pipelines or as the ‘glue’ between AWS services, logging etc. When AWS Lambda is used, development teams must consider the risks of vendor lock-in and track switching costs carefully.

<i><small>We have not said “serverless” here because the term has become more and more ambiguous and can mean several things:

1. running services on fully managed platforms where there is no need to think about server provisioning, for instance AWS ECS/Fargate
2. using distributed or managed database services like AWS RDS, DynamoDB or Aurora
3. running functions as a service, eg. AWS Lambda

GDS is actively using the first two of those as called out in this strategy and the TechOps principles, we are however not yet in a position to run our current services fully as FaaS and this makes it clear.

However, there are several new or redesigned services being discussed at present with RE and the proposed solutions place FaaS at the centre of the stack. These will be where RE will develop the patterns and processes to be able to fully support this approach going forward.</small></i>

## 8. A standard approach to supporting GDS services
As we converge on a standard platform, we are focusing on being a Reliability Engineering team and not an infrastructure team. Having a standard approach across single RE support rota will remove the necessity for multiple infrastructure support teams supporting specific platforms.

<i><small>This is a natural progression from running GDS services on a single supported platform, the support requirements converge to a standard approach across the board.</small></i>
