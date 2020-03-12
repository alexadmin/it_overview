# deployment environment

### production
Servers in DC

### staging
- full copy of production servers, but DB may be anonymized
- may connect to other production services and data, such as databases
- performance testing
- load testing
- test all the installation/configuration/migration scripts and procedures before they're applied to a production environment

https://en.wikipedia.org/wiki/Deployment_environment

https://softwareengineering.stackexchange.com/questions/355103/whats-the-difference-between-staging-and-uat-environments

### test 
servers for QA
> The purpose of the test environment is to allow human testers to exercise new and changed code via either automated checks or non-automated techniques.
>
> -- <cite>https://en.wikipedia.org/wiki/Deployment_environment</cite>

### dev
servers for developers

### local
developer desktops
> Developer's desktop/workstation
>
> -- <cite>https://en.wikipedia.org/wiki/Deployment_environment</cite>

Environments should be abolutely equals. Even by CPU manufactures, app can run in test env, but will fail in prod because of some bug in libC cooperation with kernel. 

# networks

Create separate network for each segment. For example if you have rack in DC and DC provider provide LAN to cloud, keep rack servers and cloud VM in different network to avoid loop. If you aimed to configure VLAN from HQ to DC you will get loop with high probability.
