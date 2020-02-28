# RE Strategy

<b>This is the Reliability Engineering Strategy for 2019-2020 which aims to set a direction and provide guidance for RE to meet its objectives for convergence and efficiency.</b>

<i>Version: 2.0 - 2020/01/01</i>

## 1. Bring GDS services together to a common TechOps platform of tools and services running on GOV.UK PaaS
RE exists to help GDS services be more reliable, robust and secure at reasonable cost. We minimise hosting costs while enabling GDS delivery teams to deliver their services fast, safely and securely.

RE is standardising the way it hosts and supports those services. We use RE expertise to provide as much value as possible without having to scale our support effort.

[GOV.UK Platform as a Service (PaaS)](https://cloud.service.gov.uk) is used by many GDS services and other Government departments already and we will increase our investment in this platform.

For GDS services yet to replatform and new projects, GOV.UK PaaS is to be the preferred option given its proven developer experience and operational maturity.

The PaaS team can focus over the coming quarters on creating or improving supporting tooling. Metrics, monitoring, logging, CI/CD, workflow best practices and other developer tools make it easier for development teams to build and support their services.

We will work with service teams to simplify and improve GDS services so they can migrate to the PaaS.

We want to extend the PaaS’s marketplace offerings to accommodate the common requirements of GDS services wherever possible and maintainable. This can then benefit all current and future PaaS tenants.

We aim to have moved all GDS services from older legacy hosting providers or platforms by 2021

This strategy makes clear that GDS continues to consolidate its various programs and services towards this one platform. This makes sense as an organisation grows for a number of reasons:

* The engineers responsible for the management of those services do not need to learn many different technologies and variations across the GDS estate. For instance, an engineer familiar with running, GOV.UK’s service can also work on Verify’s service because they use the same platform and tooling. Similarly, a developer working on GOV.UK can work on Pay because the developer experience is consistent. This reduces the number of specialisations in the engineering team and means support of the whole estate can be shared by the whole of RE instead of siloed groups of support responsibility.
* A problem solved, feature added or performance improvement made on one service can be applied and rolled out to all services that share that same platform.
* Cost benefits associated with sharing the same tool.

## 2. Promote and use Continuous Deployment for GDS services
RE promotes and uses continuous deployment for GDS services.

The benefits of this to GDS are:

* Delivery teams can quickly deliver code into production.
* Individual deployments are smaller, less risky and easier to diagnose when problems occur.
* Deployments are controlled by GitHub pull requests and reviews. The workflow around GitHub reviews is well understood and can be enforced declaratively in code.
* Developers do not need to interact with CI/CD systems to trigger deployments, which means there are fewer credentials to manage and less need for deployer nodes to be accessible. It also removes the need for developers to carry out deployment tasks, freeing them up to focus on their services.
* Easier user and access management.

If feasible and pragmatic, continuous deployment should be implemented through Concourse, which is available as a managed service from [reliability engineering](/continuous-deployment.html#continuous-deployment).

## 3. Look ahead to the latest generation of “serverless” Functions as a Service (FaaS) technology
GDS has supporting services running on FaaS platforms, mainly AWS Lambda. RE supports AWS Lambda for supporting services that do not directly handle user requests, such as deployment or notification pipelines or as the ‘glue’ between AWS services, logging.

However it is increasingly important that developers can implement their services using these serverless methods for the advantages in performance, security and cost efficiency they bring. There is work to do to validate this is true for GDS services and work towards a standard way to support services built in this way.

## 4. Use a shared responsibility model between RE and service delivery teams
Clear separation of responsibility is important for program teams running services on RE’s platform. Some aspects of the service must be managed and supported by the program itself and cannot be RE’s sole responsibility. Similarly the program team can not be responsible for those systems managed by RE. This boundary is clearly defined in the shared responsibility model.

See: [Shared Responsibility Model](techops-shared-responsibility-model.html)

## 5. A standard approach to supporting GDS services
As we converge on a standard platform, we are focusing on being a Reliability Engineering team and not an infrastructure team. Having a standard approach across single RE support rota will remove the necessity for multiple infrastructure support teams supporting specific platforms.
