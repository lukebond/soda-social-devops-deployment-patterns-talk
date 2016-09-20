<!--
$theme: gaia
template: invert
page_number: true
$size: 16:9
-->

# :fire: DevOps Deployment Patterns :fire:

##### :woman: SODA Social London, September 2016 :man:

###### Luke Bond

###### :wave: `T: @lukeb0nd | E: luke.n.bond@gmail.com | G: @lukebond`

---

# What You Will Learn From This Talk :books:

- Common application architectures & suitable deployment patterns therefore
- Process monitors (spoiler: use Linux)
- Scaling and service discovery considerations
- A little bit about containers along the way

The aspect of "deployment" I'm focused on is the process; what runs it, how it it looked after, etc.

---

# Application Deployment Patterns :rocket:

For simplicity, let's consider the following common application types:

- Simple web-app
- Simple web-app + API + websockets + cache + database
- Microservice-based distributed system with a message broker

Let's explore deployment patterns for these types of apps.

_TL;DR:_ don't overcomplicate; you probably don't need Kubernetes.

---

# Simple Web-App

# ![](https://github.com/lukebond/soda-social-devops-deployment-patterns-talk/images/DeploymentPatterns-Type1.png)

---

# Simple Web-App

- Easily horizontally scalable
- No communication between components or across hosts
  - Hence no service discovery needed
- For single-threaded runtimes like Node.js, run process for each core
- Scale boxes horizontally; every box is the same
- Load balance (e.g. _nginx_, _HAProxy_, _balance_)

_Recommendation_: use __systemd__ (or your preferred process monitor if you must!).

---

# `<Aside>`

---

# Process Monitors

There are various popular ones in use today:

- mon, nodemon, monit, forever
- PM2
- unicorn, gunicorn
- daemontools
- runit
- supervisord

These range from simple process monitors to almost full init systems.

---

# Use systemd as your Process Monitor

- IMHO the init system is the ultimate process monitor
  - But maybe you don't have root access to your Linux hosts
  - I wonder if this is why process monitoring tools have become popular
  - `<philosphising>`
    - A half-way house on the journey to DevOps?
  - `</philosphising>`

---

# Use systemd as your Process Monitor

- The features of process monitors can be easily recreated with _systemd_, plus a few other tools
- See my talk here where I did this in 20 minutes:
  - Video: https://opbeat.com/events/nodeconf-oslo-2016/#deploying-and-running-node-js-to-production-in-2016
  - Slides: https://github.com/lukebond/nodeconf-oslo-20160604/blob/master/nodeconf-oslo-20160604.pdf
- Read/watch this for the _how-to_; I won't go into it here

---

# You: I'm happy with my process monitor; why switch to systemd?

- The tooling is better (especially `journalctl`)
- You have more control; you can choose your components
- Run your Node.js apps the same way you run non-Node.js apps
- Any Linux sysadmin will understand it
- No need to reinvent the wheel
- Learn more about the OS on which your apps run
- What runs your process monitor on reboot? Your init system!

---

# `</Aside>`

---

# More Complex App w/ API + WS + DB

# ![](https://github.com/lukebond/soda-social-devops-deployment-patterns-talk/images/DeploymentPatterns-Type2.png)

---

# More Complex App w/ API + WS + DB

- Easily horizontally scalable
- Communication between components required
- Scale out WS & API by process and across hosts
  - Communication between hosts then required
  - Host affinity becomes important; e.g. Redis on same host as APIs
- How to do service discovery without too much complexity?

---

# More Complex App w/ API + WS + DB

_Recommendations_: you need service discovery but keep it simple

- Use __AWS CloudFormation__ and/or __Elastic Beanstalk__
- Use __Google App Engine__
- Use __Fleet__ and a dynamically configured __nginx__ or __HAProxy__
  - Easy to transition from using __systemd__ to __CoreOS Fleet__ :ok_hand:
- Use the new __Docker Swarm__ in Docker 1.12

---

# Distributed Microservices System

# ![](https://github.com/lukebond/soda-social-devops-deployment-patterns-talk/images/DeploymentPatterns-Type3.png)

---

# Distributed Microservices System

- You don't want to be thinking about any plumbing
- You don't want to be specifying exactly what runs where
- You want a declarative platform with service discovery
- You want a platform that will reschedule on app or host failures

---

# Distributed Microservices System

_Recommendation_:

- Use __Kubernetes__
- Use hosted Kubernetes such as __Tectonic__ or __Google App Engine__
- Use a PaaS such as __Deis__
- __Docker Swarm__ someday- not yet proven at scale IMO

---

# Conclusion & General Guidelines

- __Keep it simple__ or suffer the operational pain
  - No unnecessary complexity in stack or deployment
- Being __12-factor__ saves you operational pain & brings portability
- __Containers__ help if your application is sufficiently complex
  - Devs can test and debug whole system on their laptop
  - Homogeneous deployments even for heterogenous stacks
- Test, test, test; smoke test releases, ensure you can rollback easily
- __systemd__ over __PM2__ :muscle:

---

# Thanks!

###### :wave: `T: @lukeb0nd | E: luke@yld.io | G: @lukebond`
