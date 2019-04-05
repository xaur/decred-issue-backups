## tl;dr

Make solo voting easier by automating away most sysadmin steps.

STATUS: design complete, asking for feedback before starting implementation.

## Proof of Stake Voting

Decred's [Proof of Stake Voting] allows stakeholders to control block evaluation, consensus changes and proposal outcomes by getting tickets and using them to vote. Five tickets are picked randomly each block: their voting wallets are asked to evaluate the block and have to reply quickly, lest they [lose their turn].

Therefore a voting wallet not only has to constantly be online, but has to exhibit low latency to requests coming from anywhere in the world. To optimally satisfy this requirement, at least three voting wallets in different continents are needed.

[Proof of Stake Voting]: https://docs.decred.org/proof-of-stake/proof-of-stake/
[lose their turn]: https://docs.decred.org/faq/proof-of-stake/solo-mining/

### Voting Service Providers

To allow stakeholders to vote without having to set up such a distributed infrastructure, a number of [Voting Service Providers] (VSPs) have been established. Stakeholders create accounts on them and confer them voting rights on their tickets (while keeping complete controls of their funds).

When a ticket is selected, one of its VSP-based voting wallet evaluates the candidate block and approves or rejects it. In exchange for managing stakeholders' voting wallets, VSPs withhold a small percentage from the block's voting reward.

### Drawbacks of VSPs

VSPs provide an important service, but they have drawbacks.

In addition to the fees (that can become significant if a stakeholder has many tickets), a VSP handling a significant proportion of stakeholders could potentially change the consensus code and approve invalid blocks. For this reason the VSP web page suggests that each VSP should not serve more that 5% of all stakeholders. Unfortunately several VSPs are over that quota.

There are also privacy implications. Currently each VSP requires creating an account using email and password. Furthermore all tickets purchased, and rewards earned, are connected to a single Decred address, in turn connected to the account: therefore each VSP knows the tickets and the rewards of each stakeholder account.

[Voting Service Providers]: https://decred.org/stakepools/

## Solo voting

The [solo voting guide] describes the steps required to set up the solo voting infrastructure. It has the following requisites:

- availability of at least three dedicated or virtual servers, in different continents:
    - accounts with one or more online server providers
    - expense for the servers and associated services
- sysadmin experience
- time and effort to set up and maintain the servers

The expense is quite small these days: a virtual server able to run the `dcrd` and `dcrwallet` daemons goes for $10/month or less.

The other two requirements are more onerous: not many stakeholders feel confident to manage several Internet-connected servers taking care of security and maintenance, or are willing to do so, even if they can.

[solo voting guide]: https://docs.decred.org/advanced/solo-proof-of-stake-voting/

### Automating solo voting

It would be nice if server deployment and maintenance could be automated... [DevOps] to the rescue! The steps in the mentioned guide can be automated by using provisioning tools, integrated with the APIs offered by contemporary cloud providers.

The solution will only require stakeholders to create an account with a supported cloud provider, and to run a command that will set up the servers with all the configured programs needed to solo vote.

(Templates and scripts do have to be tailored to each specific provider, because they do not offer a common API: attempts at standardization do not seem to be going that well.)

[DevOps]: https://en.wikipedia.org/wiki/DevOps

### Benefits

Increasing solo voting affords both individual and collective benefits.

By solo voting stakeholders avoid paying VSP fees, paying instead the hopefully lower cloud provider bill.

The network as a whole benefits from increased decentralization due to stakeholders voting directly, without delegating to VSPs.

Another collective benefit is the increase in the number of full nodes. The Decred network currently relies on about [200 listening nodes], while almost 10,000 stakeholders use VSPs. If only 1% of them move to solo voting, the network will see 300 new listening nodes, more than doubling the current number.

[200 listening nodes]: https://dcrstats.com/map

## Implementation

### Provisioning tools

I chose [Packer] for server image creation and [Terraform] for deployment; evaluation of a daemon monitoring tool is ongoing.

Packer and Terraform are [FOSS] tools from [Hashicorp], and they belong to the new generation of Go-based orchestration tools. They are powerful and versatile enough but still limited in scope, so not to become unwieldy. [Both][] [integrate] with many cloud providers and are made of one executable file each.

(Before them came the configuration management tools: Chef, Puppet, Ansible and Salt. After came the Kubernetes monster, quite complex and only supporting container orchestration. A short historical overview is [DevOps Concepts: Pets vs Cattle]).

[Packer]: https://packer.io/
[Terraform]: https://www.terraform.io/
[both]: https://packer.io/docs/builders/index.html
[integrate]: https://www.terraform.io/docs/providers/index.html
[FOSS]: https://en.wikipedia.org/wiki/Free_and_open-source_software
[Hashicorp]: https://www.hashicorp.com/
[DevOps Concepts: Pets vs Cattle]: https://medium.com/@Joachim8675309/devops-concepts-pets-vs-cattle-2380b5aab313

### Deployment environment

We are only going to need the `dcrd` and `dcrwallet` daemons and the SSH server, so the added complexity of containerization is probably not needed.

A local development/testing environment will be supported, where each server will be a [KVM] virtual machine on a Linux host.

For simplicity the initial production deployment will be to virtual servers on cloud providers: later I will look into deploying to bare metal/dedicated servers.
[KVM]: http://www.linux-kvm.org/

### Operating system

The guest operating system will be [Alpine Linux]. It is a small distribution with a focus on simplicity and security. It uses `musl`, `OpenRC` and `busybox`, so we will avoid the complexity associated with `glibc` and `systemd`, and keep the installed code to a minimum.

[Alpine Linux]: https://www.alpinelinux.org/

## Cloud providers

Many cloud providers today offer on-demand servers at a reasonable price, and an API integrated with the above-mentioned tools. Here are some of them as of February 2019 (no affiliation).

### The big ones

The three main cloud providers are [Amazon Web Services], [Google Cloud Platform], and [Microsoft Azure], all based in USA. They have many data centers all over the world, offer a huge number of services and tend to be somewhat expensive.

In addition to the higher cost they limit the operating systems that can run on their servers, and there are centralization and privacy concerns too.

[Amazon Web Services]: https://aws.amazon.com/
[Google Cloud Platform]: https://cloud.google.com/
[Microsoft Azure]: https://azure.microsoft.com/

### USA

Several other USA providers have good prices, tools integration and at least three data centers in different continents. Prominent ones are [DigitalOcean], [Linode], and [Vultr].

[DigitalOcean]: https://www.digitalocean.com/
[Linode]: https://www.linode.com/
[Vultr]: https://www.vultr.com/

### Europe

Overall, European cloud providers are less developed. I could only find a suitable one with data centers in three different continents: [UpCloud] in Finland.

[OVH] in France currently has data centers in Europe and Canada, and has announced a forthcoming one in Singapore. [CloudSigma] in Switzerland has data centers in four continents but no integration with the chosen tools, and is expensive.

Two other good cloud providers are [Hetzner] in Germany and [Scaleway] in France. Unfortunately both only have data centers in Europe, with no announced plans to add other continents.

[UpCloud]: https://upcloud.com/
[CloudSigma]: https://www.cloudsigma.com/
[OVH]: https://www.ovh.it/cloud/
[Hetzner]: https://www.hetzner.com/cloud
[Scaleway]: https://www.scaleway.com/

### Rest of the world

[TBD]
