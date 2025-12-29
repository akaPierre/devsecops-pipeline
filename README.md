# 🔒 Secure Node.js DevSecOps Pipeline

A production-ready CI/CD pipeline demonstrating security automation and shift-left security principles for Node.js applications using GitHub Actions.

[![CI/CD Pipeline](https://github.com/akaPierre/secure-nodejs-pipeline/actions/workflows/devsecops-pipeline.yml/badge.svg)](https://github.com/akaPierre/secure-nodejs-pipeline/actions)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 📋 Table of Contents

- [Overview](#overview)
- [Security Features](#security-features)
- [Pipeline Architecture](#pipeline-architecture)
- [Technologies Used](#technologies-used)
- [Getting Started](#getting-started)
- [Pipeline Stages](#pipeline-stages)
- [Security Scan Results](#security-scan-results)
- [Local Development](#local-development)
- [Docker Usage](#docker-usage)
- [Project Structure](#project-structure)
- [Future Enhancements](#future-enhancements)
- [Learning Resources](#learning-resources)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This project implements a comprehensive DevSecOps pipeline that automates security testing throughout the software development lifecycle. By integrating multiple security tools into the CI/CD process, vulnerabilities are detected and addressed early—before deployment to production.

**Key Objectives:**

- Demonstrate shift-left security practices
- Automate vulnerability detection across multiple layers (code, dependencies, containers)
- Enforce security gates that prevent vulnerable code from reaching production
- Showcase practical DevSecOps skills for enterprise environments

## 🛡️ Security Features

### Multi-Layer Security Scanning

| Security Layer                                 | Tool             | Purpose                                                          | Severity Threshold |
| ---------------------------------------------- | ---------------- | ---------------------------------------------------------------- | ------------------ |
| **SAST** (Static Application Security Testing) | Semgrep          | Scans source code for security vulnerabilities and coding issues | High               |
| **SCA** (Software Composition Analysis)        | npm audit + Snyk | Identifies vulnerable dependencies and licenses                  | Moderate           |
| **Container Security**                         | Trivy            | Scans Docker images for OS and application vulnerabilities       | Critical           |
| **Secret Detection**                           | TruffleHog       | Detects accidentally committed secrets and credentials           | Any                |

### Security Gates

- ✅ Pipeline fails automatically on critical/high severity vulnerabilities
- ✅ Dependency vulnerabilities must be resolved before merge
- ✅ Container base images scanned for known CVEs
- ✅ Security findings integrated into GitHub Security tab
- ✅ Pull request blocking on security check failures

## 🏗️ Pipeline Architecture

```
┌─────────────────────────────────────────────────────────────┐
│ GitHub Push/PR Event │
└────────────────────┬────────────────────────────────────────┘
│
┌────────────┼────────────┐
│ │ │
▼ ▼ ▼
┌──────────────┐ ┌──────────┐ ┌─────────────┐
│ Dependency │ │ SAST │ │ Secret │
│ Scanning │ │ Scanning │ │ Scanning │
│ │ │ │ │ │
│ npm audit │ │ Semgrep │ │ TruffleHog │
│ Snyk │ │ │ │ │
└──────┬───────┘ └────┬─────┘ └──────┬──────┘
│ │ │
└──────────────┼───────────────┘
│
▼
┌─────────────────┐
│ Build & Scan │
│ Docker Image │
│ │
│ Trivy │
└────────┬────────┘
│
▼
┌─────────────────┐
│ Upload Results │
│ to GitHub │
│ Security Tab │
└─────────────────┘
```

### Workflow Execution

1. **Parallel Security Scans**: Dependency, SAST, and secret scanning run simultaneously for speed
2. **Docker Build & Scan**: Executes only after initial scans pass
3. **Results Aggregation**: All findings uploaded to GitHub Security dashboard
4. **Automated Blocking**: Pull requests blocked if critical issues detected

## 🔧 Technologies Used

### Application Stack

- **Runtime**: Node.js 18 LTS
- **Framework**: Express.js
- **Containerization**: Docker (multi-stage builds)

### DevSecOps Tools

- **CI/CD Platform**: GitHub Actions
- **SAST**: Semgrep (open-source)
- **SCA**: npm audit, Snyk
- **Container Scanning**: Aqua Security Trivy
- **Secret Detection**: TruffleHog OSS
- **Container Registry**: Docker Hub / GitHub Container Registry

### Security Standards

- OWASP Top 10 compliance checks
- CWE (Common Weakness Enumeration) detection
- CVE (Common Vulnerabilities and Exposures) tracking

## 🚀 Getting Started

### Prerequisites

- Node.js 18+ and npm
- Docker and Docker Compose
- Git
- GitHub account
- Snyk account (free tier) for enhanced dependency scanning

### Installation

1. **Clone the repository**

```
git clone https://github.com/akaPierre/secure-nodejs-pipeline.git

cd secure-nodejs-pipeline
```

2. **Install dependencies**

```
npm install
```

3. **Configure environment variables**

```
cp .env.example .env
```

_Edit .env with your configuration_

4. **Set up GitHub Secrets**

Navigate to your repository Settings → Secrets and variables → Actions:

- `SNYK_TOKEN`: Get from [snyk.io](https://snyk.io) → Account Settings → API Token
- Add any additional secrets needed for deployment

5. **Run locally**

```
npm start
```

_Application available at http://localhost:3000_

## 📊 Pipeline Stages

### Stage 1: Dependency Security Scan

**Tools**: npm audit, Snyk

Scans `package.json` and `package-lock.json` for known vulnerabilities in third-party dependencies.

### Run locally

```
npm audit --audit-level=moderate
```

**What it detects:**

- Outdated packages with security patches
- Dependencies with known CVEs
- Malicious packages
- License compliance issues

### Stage 2: SAST Code Scanning

**Tool**: Semgrep

Analyzes source code for security vulnerabilities, bugs, and anti-patterns.

### Run locally

```
docker run --rm -v "${PWD}:/src" returntocorp/semgrep semgrep --config=auto /src
```

**What it detects:**

- SQL injection vulnerabilities
- Cross-Site Scripting (XSS)
- Insecure cryptography usage
- Hard-coded credentials
- Command injection risks

### Stage 3: Secret Scanning

**Tool**: TruffleHog

Scans git history and current files for accidentally committed secrets.

### Run locally

```
docker run --rm -v "${PWD}:/repo" trufflesecurity/trufflehog:latest filesystem /repo
```

**What it detects:**

- API keys and tokens
- Database credentials
- Private keys
- AWS/Azure/GCP credentials

### Stage 4: Container Image Scanning

**Tool**: Trivy

Scans Docker images for vulnerabilities in OS packages and application dependencies.

### Run locally

```
docker build -t secure-nodejs-app:test .

trivy image secure-nodejs-app:test
```

**What it detects:**

- OS package vulnerabilities
- Application dependency issues
- Misconfigurations in container
- Exposed secrets in layers

## 📈 Security Scan Results

### Sample Findings Dashboard

| Scan Type    | Vulnerabilities Found | Critical | High | Medium | Low |
| ------------ | --------------------- | -------- | ---- | ------ | --- |
| Dependencies | 12                    | 0        | 2    | 5      | 5   |
| SAST         | 3                     | 0        | 1    | 2      | 0   |
| Container    | 8                     | 0        | 0    | 4      | 4   |
| Secrets      | 0                     | 0        | 0    | 0      | 0   |

**Status**: ✅ All critical and high vulnerabilities resolved

View detailed security reports in the [GitHub Security tab](https://github.com/akaPierre/secure-nodejs-pipeline/security).

## 💻 Local Development

### Running the Application

### Development mode with hot reload

```
npm run dev
```

### Production mode

```
npm start
```

### Run tests

```
npm test
```

### API Endpoints

- `GET /` - Health check endpoint
- `GET /health` - Application status
- `GET /api/info` - Application information

### Running Security Scans Locally

### Install security tools

```
npm install -g snyk
```

### Authenticate Snyk

```
snyk auth
```

### Run all scans

```
npm run security:scan
```

## 🐳 Docker Usage

### Build the Image

```
docker build -t secure-nodejs-app:latest .
```

### Run the Container

```
docker run -p 3000:3000 secure-nodejs-app:latest
```

### Docker Compose

```
docker-compose up -d
```

### Security Features in Dockerfile

- ✅ Multi-stage builds to minimize image size
- ✅ Non-root user for runtime
- ✅ Minimal Alpine base image
- ✅ Only production dependencies included
- ✅ .dockerignore to prevent sensitive files

## 📁 Project Structure

```
secure-nodejs-pipeline/
├── .github/
│ └── workflows/
│ └── devsecops-pipeline.yml # CI/CD pipeline configuration
├── src/
│ ├── app.js # Express application
│ ├── routes/ # API routes
│ └── middleware/ # Custom middleware
├── tests/
│ └── app.test.js # Unit tests
├── .dockerignore # Docker ignore rules
├── .gitignore # Git ignore rules
├── Dockerfile # Multi-stage container build
├── docker-compose.yml # Local development setup
├── package.json # Dependencies and scripts
├── package-lock.json # Locked dependency versions
└── README.md # Project documentation
```

## 🚧 Future Enhancements

### Planned Features

- [ ] **DAST Implementation**: Add OWASP ZAP for dynamic application security testing
- [ ] **Deployment Pipeline**: Automated deployment to AWS ECS/Fargate or Kubernetes
- [ ] **Infrastructure as Code**: Terraform with security scanning (Checkov, tfsec)
- [ ] **Secrets Management**: Integration with HashiCorp Vault or AWS Secrets Manager
- [ ] **Security Monitoring**: Runtime security with Falco or Sysdig
- [ ] **Compliance Automation**: CIS benchmark validation and compliance reports
- [ ] **Performance Testing**: Load testing with k6 or Artillery
- [ ] **Code Coverage**: Integration with Codecov or SonarQube
- [ ] **API Security Testing**: OWASP API Security Top 10 validation
- [ ] **Supply Chain Security**: SBOM generation and verification

### Advanced Security Features

- [ ] Kubernetes security policies (Pod Security Standards)
- [ ] Network segmentation and zero-trust architecture
- [ ] Container runtime protection with Falco
- [ ] Security chaos engineering tests
- [ ] Automated security regression testing

## 📚 Learning Resources

### DevSecOps Concepts

- [OWASP DevSecOps Guideline](https://owasp.org/www-project-devsecops-guideline/)
- [DevSecOps Manifesto](https://www.devsecops.org/)
- [NIST Secure Software Development Framework](https://csrc.nist.gov/Projects/ssdf)

### Security Tools Documentation

- [Semgrep Rules](https://semgrep.dev/explore)
- [Snyk Vulnerability Database](https://security.snyk.io/)
- [Trivy Documentation](https://aquasecurity.github.io/trivy/)
- [GitHub Security Features](https://docs.github.com/en/code-security)

### Related Projects

- [Awesome DevSecOps](https://github.com/TaptuIT/awesome-devsecops)
- [DevSecOps Pipeline Examples](https://github.com/topics/devsecops-pipeline)

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the project
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

**Note**: All contributions must pass security scans before being merged.

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author

**Your Name**

- GitHub: [@akaPierre](https://github.com/akaPierre)
- LinkedIn: [Daniel Pierre Fachini](https://www.linkedin.com/in/daniel-pierre-fachini)
- Portfolio: [danielpierre.tech](https://danielpierre.tech)

## 🙏 Acknowledgments

- [GitHub Actions](https://github.com/features/actions) for CI/CD platform
- [Aqua Security](https://www.aquasec.com/) for Trivy container scanner
- [Semgrep](https://semgrep.dev/) for static analysis
- [Snyk](https://snyk.io/) for dependency scanning
- DevSecOps community for best practices and tools

---

**⭐ If you find this project useful, please consider giving it a star!**

_Built with security in mind | Powered by open-source tools | Ready for production_
