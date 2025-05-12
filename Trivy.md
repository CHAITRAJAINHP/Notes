# Notes


What is Trivy (Simple Explanation)
Trivy is an open-source security scanner made by Aqua Security. It helps you find vulnerabilities in:

Docker images (e.g., if your image contains outdated packages with security issues)

Kubernetes clusters

IaC (Infrastructure as Code) like Terraform, Helm, etc.

Git repositories

SBOMs (Software Bill of Materials)

Think of Trivy like a security detective that checks your code, containers, and infrastructure for anything risky or outdated.

🚀 Why Trivy is Important in DevOps / SRE
Lightweight and fast — very easy to integrate into CI/CD.

Supports multiple targets — scans containers, filesystems, code repos, etc.

Provides CVE details — CVEs (Common Vulnerabilities and Exposures) with severity and fixes.

Can scan both OS packages (e.g., Ubuntu, Alpine) and app dependencies (like Python’s pip, Node’s npm, etc.)

Helps maintain DevSecOps best practices.

🔥 Key Features Interviewers Expect You to Know
Feature	Explanation
Image scanning	Scans Docker images for vulnerabilities
File system scanning	Scans directories (like your codebase or production host)
Git repo scanning	Detects secrets and vulnerable dependencies
Infrastructure as Code	Scans Terraform, Helm charts for misconfigurations
SBOM support	Reads and scans CycloneDX, SPDX, etc.
Trivy Kubernetes Operator	Automatically scans running workloads
Trivy CLI and GitHub Actions	Easily integrates into pipelines
Severity filters	Focus on critical/high issues
Output formats	JSON, table, SARIF, etc.

🧠 Interview Questions and Answers (Trivy)
1. What is Trivy?
Answer: Trivy is an open-source vulnerability and misconfiguration scanner developed by Aqua Security. It can scan Docker images, Kubernetes clusters, filesystems, IaC tools, and Git repositories for security issues.

2. What can Trivy scan?
Answer: Trivy can scan:

Container images

Filesystems

Git repositories

Kubernetes clusters

IaC files like Terraform, Helm

SBOM (CycloneDX, SPDX)

3. What types of vulnerabilities does Trivy detect?
Answer: Trivy detects:

OS package vulnerabilities (e.g., apt, apk, yum)

Application dependency vulnerabilities (e.g., pip, npm, Maven)

Misconfigurations in IaC

Hardcoded secrets

4. What are the output formats supported by Trivy?
Answer: Trivy supports:

Table (default)

JSON

SARIF (for GitHub integration)

CycloneDX SBOM

5. How do you scan a Docker image using Trivy?
bash
Copy
Edit
trivy image nginx:latest
6. How do you scan a file system (like source code folder)?
bash
Copy
Edit
trivy fs .
7. How to scan a GitHub repo for secrets and vulnerabilities?
bash
Copy
Edit
trivy repo https://github.com/my-org/my-repo
8. Can Trivy scan IaC files like Terraform or Helm?
Answer: Yes, using:

bash
Copy
Edit
trivy config .
9. What’s the Trivy Operator in Kubernetes?
Answer: It’s a Kubernetes operator that automatically scans running workloads (pods/deployments) for vulnerabilities and misconfigurations.

10. How can Trivy be used in CI/CD pipelines?
Answer: Trivy CLI can be added as a step in pipelines (Jenkins, GitHub Actions, GitLab, etc.) to scan images, code, or configs before deployment.

11. What does a typical Trivy scan report include?
Answer: It shows:

Vulnerability ID (CVE)

Severity (CRITICAL, HIGH, etc.)

Package name & version

Fixed version (if available)

Description of the issue

12. How do you filter results by severity?
bash
Copy
Edit
trivy image --severity CRITICAL,HIGH nginx:latest
13. What is CVE?
Answer: CVE stands for Common Vulnerabilities and Exposures — a standardized identifier for known security issues.

14. How do you integrate Trivy with GitHub Actions?
Answer: Use the official Trivy GitHub Action:

yaml
Copy
Edit
- name: Scan image
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: 'myapp:latest'
15. What’s the difference between Trivy and tools like Clair or Grype?
Answer:

Trivy is easier to use and faster.

Trivy scans more types of targets (not just containers).

Trivy includes secret detection and config scanning.

16. Can Trivy scan non-containerized apps?
Answer: Yes, using trivy fs for filesystems or trivy repo for repositories.

17. What databases does Trivy use for CVEs?
Answer: Trivy uses vulnerability databases from:

Debian, Alpine, Ubuntu, RedHat, etc.

GitHub Security Advisory

NVD (National Vulnerability Database)

🧪 Sample Real Use Case
Let’s say your team builds a Docker image. Before pushing it to production, you run:

bash
Copy
Edit
trivy image myapp:latest
This scan shows that one package has a critical CVE, and the output gives you the fixed version. You fix it before deployment — this is how DevSecOps works in practice.

Here's a Jenkins Pipeline example that integrates Trivy for vulnerability scanning of a Docker image before deployment.

✅ Jenkins Pipeline (Declarative) with Trivy
This script does the following:

Builds a Docker image

Scans it with Trivy

Fails the build if vulnerabilities are critical/high

Saves Trivy report as an artifact

📜 Jenkinsfile
groovy
Copy
Edit
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'myapp:latest'
        TRIVY_SEVERITY = 'CRITICAL,HIGH'
        TRIVY_REPORT = 'trivy-report.json'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-org/your-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(IMAGE_NAME)
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                sh '''
                    # Install Trivy if not already installed
                    if ! command -v trivy &> /dev/null; then
                      echo "Installing Trivy..."
                      curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin
                    fi

                    # Run Trivy scan
                    trivy image --severity ${TRIVY_SEVERITY} --format json -o ${TRIVY_REPORT} ${IMAGE_NAME}

                    # Fail if any CRITICAL/HIGH vulnerability is found
                    if grep -q '"Vulnerabilities":' ${TRIVY_REPORT}; then
                      echo "Vulnerabilities found! Failing the build."
                      exit 1
                    else
                      echo "No critical/high vulnerabilities found."
                    fi
                '''
            }
        }

        stage('Archive Trivy Report') {
            steps {
                archiveArtifacts artifacts: "${TRIVY_REPORT}", allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            echo 'Cleanup or notifications can go here.'
        }
    }
}
📌 What You Need
Jenkins agent with Docker installed

Internet access to install/run Trivy

Permission to build Docker images in Jenkins

Optional: Install Trivy once on the agent and cache it

✅ Output Example
Build fails if CRITICAL/HIGH vulnerabilities are found.

trivy-report.json is archived in Jenkins for review.

You can view exact CVEs, affected packages, and fixed versions.
