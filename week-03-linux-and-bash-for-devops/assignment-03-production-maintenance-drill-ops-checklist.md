# Assignment 3 — Production Maintenance Drill (OPS Checklist)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will treat your already deployed React application (on Ubuntu VM with Nginx) as a live production system. You will perform structured operational checks covering network validation, service health, log analysis, resource monitoring, configuration verification, and incident simulation with recovery — mirroring real on-call DevOps responsibilities.

---

# Task 1 — Server Access & Networking Validation

## Goal

Verify that the deployed React application is reachable from the browser and confirm basic network connectivity of the Ubuntu VM.

### Evidence

#### Screenshot 1 — Browser showing the React app with your Full Name visible on the UI

![Screenshot1](\screenshots\Screenshot2-10.jpg)

---

#### Screenshot 2 — Output of `ip a`

![Screenshot2](\screenshots\Screenshot3-2.jpg)

---

#### Screenshot 3 — Output of `sudo ss -tulpen`

![Screenshot3](\screenshots\Screenshot3-3.jpg)

---

#### Screenshot 4 — Output of `sudo ufw status`

![Screenshot4](\screenshots\Screenshot3-4.jpg)

---

### Notes

Answer the following in your own words:

**1. What proves Nginx is listening on 0.0.0.0:80?**

The deployed website only displays when connected using http. Any attempt to use https displays an error response.

---

**2. What proves SSH is active on port 22?**

SSH being active is the reason i was able to connect to the EC2 instane from gitbash using the pem key that was created.

---

**3. Did you find any unexpected open ports? Explain briefly.**

Only the necessary ports were open, all other ports are disabled from the security group.

---

# Task 2 — Service Health & Systemd Validation (Nginx)

## Goal

Verify that Nginx is properly installed, running, enabled at boot, and safely configured.

### Evidence

#### Screenshot 1 — Output of `systemctl status nginx --no-pager`

![Screenshot](\screenshots\Screenshot3-5.jpg)

---

#### Screenshot 2 — Output of `sudo nginx -t`

![Screenshot](\screenshots\Screenshot3-6.jpg)

---

#### Screenshot 3 — Output of `sudo ss -lptn '( sport = :80 )'`

![Screenshot](\screenshots\Screenshot3-7.jpg)
---

### Notes

Answer the following in your own words:

**1. What happens if Nginx fails to restart in production?**

If Nginx fails to restart, the web server may become unavailable, causing users to be unable to access the application or website.

---

**2. What's your basic rollback plan?**

My rollback plan would be to revert the changes that caused the issue by restoring the previous working configuration or application version.

---

# Task 3 — Logs & Request Trace

## Goal

Verify real traffic flow and analyze logs to understand system behavior and errors.

### Evidence

#### Screenshot 1 — Output of `sudo tail -n 30 /var/log/nginx/access.log`

![Screenshot](\screenshots\Screenshot3-8.jpg)

---

#### Screenshot 2 — Output of `sudo tail -n 30 /var/log/nginx/error.log`

![Screenshot](\screenshots\Screenshot3-9.jpg)

---

#### Screenshot 3 — Output of `sudo journalctl -u nginx --no-pager -n 50`

![Screenshot](\screenshots\Screenshot3-10.jpg)

---

### Notes

Answer the following in your own words:

**1. Were there any errors in the logs?**

- If yes, mention 1–2 example error lines from the logs and explain what each one means in simple terms.
- If no, explain what it means if the error log is empty or shows no recent errors during your check.

The error log was empty because the server is yet to encounter ny errors in it's lifecycle.

---

**2. If there were no errors, what does that indicate about the system?**

It indicates that the system is healthy and running well.

---

**3. Based on the access logs, were your curl requests visible in the log entries? What does that prove about traffic flow?**

The access log showed several get requests, this means the nginx service is accessible over http.

---

# Task 4 — System Resource Health Check (Capacity Red Flags)

## Goal

Assess server capacity and detect potential performance or failure risks.

### Evidence

#### Screenshot 1 — Output of `uptime`

![Screenshot](\screenshots\Screenshot3-11.jpg)

---

#### Screenshot 2 — Output of `free -h`

![Screenshot](\screenshots\Screenshot3-12.jpg)

---

#### Screenshot 3 — Output of `df -h`

![Screenshot](\screenshots\Screenshot3-13.jpg)

---

#### Screenshot 4 — Output of `sudo du -sh /var/* | sort -h`

![Screenshot](\screenshots\Screenshot3-14.jpg)

---

### Notes

Answer the following in your own words:

**1. Which resource looks most critical right now? (CPU/load, memory, or disk) Explain why.**

Memory is the most critical resource right now. Disk spacee shows about 60% used but the RAM shows 220MB free out of 900MB, that's almost 80% occupied.

---

**2. What happens if disk becomes 100% full in a production server?**

When the disk space becomes full, all services that requires write access will not have space to work with, this will eventually cause the systm to freeze or crash.

---

# Task 5 — Configuration & Deployment Verification

## Goal

Ensure the correct React build is deployed and Nginx is serving it properly.

### Evidence

#### Screenshot 1 — Output of `ls -lah /var/www/html | head -n 20`

![Screenshot](\screenshots\Screenshot3-15.jpg)

---

#### Screenshot 2 — Output of `grep -R "Deployed by" -n /var/www/html 2>/dev/null | head`

Add your screenshot here.

---

#### Screenshot 3 — Output of `grep -n "try_files" /etc/nginx/sites-available/default`

![Screenshot](\screenshots\Screenshot3-16.jpg)

---

### Notes

Answer the following in your own words:

**1. How do you confirm that the correct version of the application is deployed?**

We can check the correctness of the app deployed by comparing the web output to the uploaded index.html file

---

# Task 6 — Nginx Configuration Failure Simulation

## Goal

Simulate a real-world Nginx misconfiguration and recover the service safely.

### Evidence

#### Screenshot 1 — Output of `sudo nginx -t` showing the syntax error (broken config)

![Screenshot](\screenshots\Screenshot3-17.jpg)

---

#### Screenshot 2 — Output of `sudo nginx -t` showing syntax ok (fixed config)

![Screenshot](\screenshots\Screenshot3-18.jpg)

---

#### Screenshot 3 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

![Screenshot](\screenshots\Screenshot3-19.jpg)

---

### Notes

Answer the following in your own words:

**1. What caused the configuration failure?**

The syntax for the /etc/nginx/sites-available/default file was altered causing the nginx server to respond with an error response

---

**2. How did you fix the issue?**

The altered line was corrected and nginx was restarted to update the new config.

---

**3. How can you avoid this kind of issue in real production systems?**

This issue can be avoided by ensuring all config files are properly written and mistakes are duly corrected when observed.

---

# Task 7 — Web Application Failure Simulation

## Goal

Simulate missing deployment content and recover the application safely.

### Evidence

#### Screenshot 1 — Output of `curl -I http://<public-ip>` showing failure (non-200 response)

![Screenshot](\screenshots\Screenshot3-20.jpg)

---

#### Screenshot 2 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

![Screenshot](\screenshots\Screenshot3-21.jpg)

---

### Notes

Answer the following in your own words:

**1. What caused the application to break in this scenario?**

The /var/www/html directory was empty so the service couldn't find any configuration file.

---

**2. How did you fix the issue and restore the application?**

I restored the files in the directory and restarted the nginx service

---

**3. What steps would you take to prevent this kind of issue in real production systems?**

Ensure all configuration files are present before deploying any app. 

---

# Task 8 — Security & Reliability Review

## Goal

Review and reflect on the security and reliability practices applied during this assignment.

### Security & Reliability Notes

Answer the following in your own words:

**1. Why is SSH key-based authentication more secure than sharing passwords?**

SSH key-based authentication is more secure because it replaces vulnerable, guessable passwords with mathematically complex public-private key pairs that are immune to brute-force attacks.

---

**2. Why should only required ports be open on a production server?**

Leaving unnecessary ports open on a production server expands the attack surface, giving hackers more entry points to scan and exploit.

---

**3. Why is it important for Nginx to be enabled on boot?**

It is important for Nginx to be enabled on boot to guarantee high availability and automated recovery. If the production server crashes or experiences a cloud provider outage, Nginx will start automatically without requiring manual human intervention. This prevents prolonged, unexpected downtime and ensures your users can always access your services. 

---

**4. What are the risks of sharing secrets, keys, or credentials publicly?**

Exposing secrets and keys to the public leaves the systm at risk of being accessed by threat actors.

---

**5. Why should cloud resources be stopped or terminated when they are no longer needed?**

Leaving cloud resources active while they're not needed makes the company or user incur unnecessary costs, it also exposes the whole infrastructure to potential security attacks.

---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`__________________________`

---

#### Screenshot — Published LinkedIn post

Add your screenshot here.

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [ ] Task 1: Screenshots (browser, ip a, ss -tulpen, ufw status) + Notes answered
- [ ] Task 2: Screenshots (nginx status, nginx -t, ss port 80) + Notes answered
- [ ] Task 3: Screenshots (access log, error log, journalctl) + Notes answered
- [ ] Task 4: Screenshots (uptime, free -h, df -h, du -sh) + Notes answered
- [ ] Task 5: Screenshots (ls html, grep deployed by, grep try_files) + Notes answered
- [ ] Task 6: Screenshots (nginx -t fail, nginx -t pass, curl recovery) + Notes answered
- [ ] Task 7: Screenshots (curl failure, curl recovery) + Notes answered
- [ ] Task 8: Security & Reliability Notes answered
- [ ] LinkedIn post published and URL submitted
- [ ] Full Name visible in all required screenshots
- [ ] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://pravinmishra.com/dmi  
- 🎓 DevOps for Beginners (Udemy): https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 Agentic AI DevOps with Claude Code: https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/  
- 🎓 DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm: https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*