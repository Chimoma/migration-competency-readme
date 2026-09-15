# Migration & Modernization Competency — Candidate Solutions

Four candidate solutions for this competency, pending selection of a real customer account. Each solution addresses three requirement areas: **Discovery & Planning**, **Business Case Analysis**, and **Infrastructure Automation**. Application stack selection is not fixed here and will be determined during implementation.

---

## The four candidates

| Account | Solution | Key AWS Services | Assigned |
|---|---|---|---|
| Jaramarket | **Internal Ticketing & Request Portal** — employees log an issue; support tracks and resolves it. | AWS Transform, Amazon EC2, CloudFormation/CDK | John |
| Africa Prudential | **Approval & Workflow System** — staff submit a request; a manager approves or rejects it. | AWS Transform, Amazon EC2, AWS CDK | Chijioke |
| Accion | **Internal Operations & Asset Tracking** — staff check equipment in/out; a background job reconciles counts. | AWS Transform, Amazon ECS (Fargate), Amazon EC2, Terraform | Samuel |
| Toyota | **Financial Approval & Reimbursement Workflow** — staff submit a request with documentation; finance reconciles it. | AWS Transform, Amazon EC2, CloudFormation/CDK | Cynthia |

---

## How the migration works (same for all four)

1. **Discover** — AWS Transform's discovery tool scans the source servers (VMware, Hyper-V, or bare metal) and collects server specs, OS, database, and dependency data.
2. **Plan** — that data feeds an assessment that recommends right-sized EC2 instances and storage.
3. **Business case** — the assessment produces a cost report (on-prem vs. AWS, licensing, Reserved Instance/Savings Plan savings) plus productivity and sustainability gains.
4. **Migrate** — replicate → test → cutover (or containerize, where that's the plan).
5. **Automate** — the landing zone and workload ship as infrastructure-as-code (CloudFormation/CDK, or Terraform for one solution), with rollback and drift detection built in.

---

## Simulating the on-prem environment (no real VMware/Hyper-V required)

Standard EC2 instances stand in for the on-prem servers. AWS Transform's discovery tool supports a connection mode that requires only SSH/WinRM access to a server, not a real hypervisor — so a small number of EC2 instances, tagged as the "on-prem" app and database servers, produce real, live-collected discovery data. Everything downstream (assessment, business case, migration, IaC) then proceeds exactly as it would with a real source environment. For a larger business-case scenario without provisioning additional instances, a prepared spreadsheet of server specs can be fed into the assessment step directly instead of live discovery.

Each scenario migrates the application and the database as two separate components.

---

## The four solutions

### 1. Internal Ticketing & Request Portal *(baseline reference solution)*
- **Source:** 2 on-prem VMware VMs (app + database)
- **Target:** Two-tier on AWS — app EC2 behind a load balancer, database EC2 behind that. CloudFormation/CDK.
- **Approach:** Rehost. Discovery identifies both VMs and the connection between them; the business case shows a direct on-prem-vs-AWS cost comparison; migration proceeds as a single replicate/test/cutover wave.
- **Simulated environment:** 2 EC2 instances (`sim-appserver-01`, `sim-dbserver-01`). Application migrates to a new EC2 instance behind a load balancer. Database migrates to its own EC2 instance.

### 2. Approval & Workflow System *(includes application modernization)*
- **Source:** 2 on-prem Hyper-V VMs, Windows (app + licensed database)
- **Target:** Two-tier — modernized app on EC2 or ECS Fargate, database rehosted as-is. AWS CDK.
- **Approach:** Same discovery/business-case flow as above, with the licensing cost (Windows/SQL Server) as the primary cost saving. The application is modernized to a supported runtime rather than migrated as-is.
- **Simulated environment:** 2 Windows EC2 instances (`sim-appserver-02`, `sim-dbserver-02`). Application is modernized, then migrates to EC2 or ECS Fargate. Database migrates as-is to its own EC2 instance.

### 3. Internal Operations & Asset Tracking *(Terraform-based infrastructure automation)*
- **Source:** 3 on-prem VMware VMs (app, background worker, database)
- **Target:** Three-tier, containerized — app and worker as ECS Fargate services, database rehosted to EC2. Terraform.
- **Approach:** Same discovery flow as above, with containerization as the migration plan rather than a straight rehost. The Terraform path requires additional configuration for drift detection and rollback that CloudFormation/CDK provides natively.
- **Simulated environment:** 3 EC2 instances (`sim-appserver-03`, `sim-worker-03`, `sim-dbserver-03`). Application and worker each migrate to their own ECS Fargate service. Database migrates as-is to its own EC2 instance.

### 4. Financial Approval & Reimbursement Workflow *(bare-metal source environment)*
- **Source:** 2 physical servers, no hypervisor
- **Target:** Two-tier — same architecture as Solution 1. CloudFormation/CDK.
- **Approach:** With no hypervisor present, discovery connects directly to the servers rather than through vCenter/Hyper-V; the rest of the flow is unchanged. Given the financial nature of this workload, the business case incorporates ongoing vulnerability scanning and patching as part of the deployment pipeline.
- **Simulated environment:** 2 EC2 instances (`sim-appserver-04`, `sim-dbserver-04`). Since discovery already connects directly to physical servers, this environment requires no substitution of connection method. Application migrates to a new EC2 instance behind a load balancer. Database migrates to its own EC2 instance.

---

## Notes

- Full control-by-control detail (which specific checklist requirement each part of the solution satisfies) is provided in `AWS_Transform_Competency_Gap_Analysis.docx`.
- Account and assignee are TBD until a real customer is selected.
