# Lloyds


TODO:

- TRAINING
- INSTALL ICP TOOLS

## Brief

Product=Scottish Widows

ADVISOR TEAM
application support for developers
deploying the product
Team KMOT - keep me on track (Members: Mel,...)
Build scripts/pipelines (on Jenkins; running inside k8s)
All on Kubernetes ICP --> moving to GCP
sandbox & on-prem
- sandbox is playtime, most commits & testing
- on-prem less builds, mini-production env

HelmCharts? Used to track/monitors k8s deployments
Istio Service Mesh [used heaviliy]

multiple testing stages: SIT (system integration testing), NFT (non-functional testing); on multiple data centers.
Vault secrets manager

## Onboarding

**Credentials for most things**
USERID: 8532919
pass: :6a1Q?tA

**Creds for MPW related requests**
USER: MPW8532919
pass: provided via CyberArk

local admin access --> open powershell with admin

request dev tool access = access to all apps relevat to be installed
install all dev tools

request GitHub enterprise access

setup WSL

access IBM private cloud


## LBG All hands meetup 27/05/22

- KMOT continuing with monthly releases
- I am in Team Adviser; filling in for Espen's role
- Ex-members:
  andy ninnes
  josh gibson
  rory macdonald
  Espen 
- Current members:
  James Brown
- current issues (Adviser)
  - Pipeline issues
  - Dev environment issues
  - Working across 3 releases
  - Gap between devs and QE


## R1b deployment

6 APIs 

Need:
- change request token
- cyberark provided password

Make change request on ServiceNow:
- New-record
- 

1. Go to UrbanCodeDeploy -> applications
2. Create deployment; select existing deployment
3. Choose prod(s) for chosen deployment -> request 
next
select service & version
next
confirm deployment
4. enter icp creds (MPW8532919 + cyberark password)
5. monitor deployment happening on cluster (monitor Dynatrace to see everything up & running)


[two legs of deployment; Lon02 & Lon06]
route traffic to Lon02 (draining method for Lon02? = F5; by F5 team)
deploy Lon06
route traffic to Lon06
redeploy Lon02

*F5 = hardware-based load balancer*

*CH61244131 = change request ticketing id*
