# 🧭 AWS Task Analytics Dashboard

A **serverless analytics pipeline** built using AWS services to analyze and visualize task data stored in **DynamoDB**.  
This project demonstrates how to integrate **Lambda, Athena, QuickSight, Glue, and S3** to build a real-time analytics dashboard.

---

## 📊 Architecture Overview

### **What We’re Building**
<img width="1896" height="918" alt="Screenshot 2025-10-08 215418" src="https://github.com/user-attachments/assets/6d28d3b3-c5a1-4acd-b99a-2804a322324e" />


**Workflow:**
1. **DynamoDB** stores structured task data (JSON-based items).
2. **AWS Lambda (DynamoDB Connector)** reads data and connects to Athena for querying.
3. **AWS Glue** catalogs the schema for Athena queries.
4. **Amazon S3** acts as the spill location for Athena query results.
5. **Amazon QuickSight** visualizes analytics insights through dynamic dashboards.

---

## 🧩 Components Used

| AWS Service | Purpose |
|--------------|----------|
| **DynamoDB** | Stores task data such as task name, category, and completion status. |
| **Lambda** | Acts as the connector to query DynamoDB data through Athena. |
| **Athena** | Enables SQL-like querying of DynamoDB data. |
| **AWS Glue** | Manages metadata catalog for querying structured data. |
| **S3 (Spill Bucket)** | Temporary storage for query results and metadata. |
| **QuickSight** | Creates dashboards for visualizing analytics and trends. |

---

## 📁 Project Files

| File | Description |
|------|--------------|
| `DynamoDB Items in JSON.txt` | JSON data to populate the DynamoDB table with 10 example task entries. |
| `IAM Policies.txt` | IAM policies for Lambda and S3 permissions. |
| `Screenshot 2025-10-08 215418.png` | Architecture diagram showing data flow and AWS service integration. |

---

## ⚙️ Setup Instructions

1. **Create DynamoDB Table**
   - Table Name: `TaskAnalytics`
   - Primary Key: `TaskID` (String)
   - Paste JSON entries from `DynamoDB Items in JSON.txt` one at a time into the table (use JSON view).

2. **Create AWS Glue Catalog**
   - Use Glue Crawler to infer schema from DynamoDB table.

3. **Set Up Lambda Function**
   - Function Name: `athenafederatedcatalog_mydynamodb`
   - Attach the **Lambda Invoke Policy** from `IAM Policies.txt`.
   - Configure Lambda to act as a federated connector for Athena.

4. **Configure Athena**
   - Set up a federated query connection using the Lambda function.
   - Define the **S3 spill bucket** in Athena settings.

5. **Create S3 Spill Bucket**
   - Name: `your-s3-spill-bucket-name`
   - Apply S3 access policy from `IAM Policies.txt`.

6. **Visualize in QuickSight**
   - Connect QuickSight to Athena dataset.
   - Build dashboards showing:
     - Task completion trends
     - Category distribution
     - Completion rate over time

---

🔒 IAM Policy References

* Lambda Invocation Policy
  Allows QuickSight and Athena to invoke the Lambda connector.

* S3 Access Policy
  Grants Athena permissions to access spill bucket for query results.

🚀 Future Enhancements

* Add API Gateway for task CRUD operations.

* Integrate with AWS Step Functions for workflow automation.

* Add notifications using SNS for overdue tasks.

## 📈 Example Queries (Athena)

```sql
-- 1. View all completed tasks
SELECT * FROM taskanalytics WHERE completionstatus = true;

-- 2. Count tasks per category
SELECT category, COUNT(*) AS total_tasks
FROM taskanalytics
GROUP BY category;

-- 3. Completion percentage
SELECT 
  (SUM(CASE WHEN completionstatus THEN 1 ELSE 0 END) * 100.0 / COUNT(*)) AS completion_rate
FROM taskanalytics;

