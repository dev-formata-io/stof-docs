# Stof vs. Temporal/Airflow

## **TL;DR**

* **Temporal, Airflow, and Prefect** are workflow orchestration platforms with servers, databases, UIs, and heavy infrastructure
* **Stof** is a lightweight document format where workflows are portable, executable data

**Key difference:** Temporal/Airflow require infrastructure to run. Stof workflows are just documents you can execute anywhere.

## Comparison

All let you:

* Define multi-step workflows
* Handle retries and error recovery
* Orchestrate distributed tasks
* Track workflow state and history

| Feature          | Temporal                     | Airflow                      | Perfect                      | Stof                                                    |
| ---------------- | ---------------------------- | ---------------------------- | ---------------------------- | ------------------------------------------------------- |
| Architecture     | Server + Workers             | Server + Scheduler + Workers | Server + Agents              | No server needed                                        |
| Infrastructure   | Required                     | Required                     | Required                     | Optional                                                |
| Format           | Code (Go, Python, Java)      | Python DAGs                  | Python flows                 | Stof documents                                          |
| Deployment       | Complex                      | Complex                      | Medium                       | Copy a file                                             |
| Portability      | Tied to Cluster              | Tied to Instance             | Tied to Cloud Server         | Run anywhere                                            |
| Size overhead    | \~100MB + runtime            | \~500MB + deps               | \~50MB + runtime             | \~4MB runtime                                           |
| State management | Database required            | Database required            | Database required            | Optional (in-document)                                  |
| UI/Monitoring    | :white\_check\_mark: Rich UI | :white\_check\_mark: Rich UI | :white\_check\_mark: Rich UI | :yellow\_circle: Basic via logs                         |
| Best for         | Enterprise workflows         | Batch data pipelines         | Modern data workflows        | Lightweight, open-source, portable workflow definitions |

### **The Core Difference**

**Temporal/Airflow/Prefect:**

> "Deploy a workflow platform, then deploy your workflows to it."

You need servers, databases, monitoring, and operational overhead.

**Stof:**

> "The workflow IS a document. Execute it wherever you need."

No platform required. The workflow is portable data. Open-source.

## **The Bottom Line**

#### **Use Temporal when:**

* You need mission-critical workflow durability
* Workflows run for days/weeks/months
* Your team needs rich monitoring and debugging
* You can afford the infrastructure and operational overhead

#### **Use Airflow when:**

* You're building batch data pipelines
* You need scheduling and backfill capabilities
* Your team is familiar with Python DAGs
* You need extensive plugin ecosystem

#### **Use Prefect when:**

* You want modern workflow orchestration
* You prefer Python-native flows over DAGs
* You want easier setup than Airflow
* You're okay with cloud-hosted or self-hosted server

#### **Use Stof when:**

* You need lightweight, open-source, portable workflows
* Workflows are simple to medium complexity
* You don't want to manage infrastructure
* You need workflows as API payloads
* You're building edge/IoT/embedded systems
* Cost and simplicity matter more than rich monitoring

#### **Use Multiple when:**

* You need Temporal/Airflow's orchestration + Stof's portability
* Core workflows in Temporal, transformation logic in Stof
* Large-scale scheduling (Airflow) + lightweight execution (Stof)
