### **Understanding CPU Credits in AWS EC2**

AWS **EC2 instances** (specifically **T2** and **T3** instances) use a system called **CPU Credits** to manage performance. This system allows your instance to use extra computing power when needed while keeping costs low.

---

## **1. What Are CPU Credits?**
A **CPU Credit** represents **one minute of full CPU power**.

- **When your instance is idle (not doing much work),** it **earns CPU credits**.
- **When your instance is active (handling tasks),** it **spends CPU credits**.
- If your instance has credits saved up, it can **burst** (work faster than usual for a short time).
- If it runs out of credits, it **slows down** to its baseline performance.

---

## **2. How CPU Credits Work**
AWS **T2 and T3 instances** use a **credit-based system** to balance performance and cost.

👉 **Example: t2.micro Instance**
- It **earns** **6 CPU credits per hour** (when idle).
- It **spends** 1 CPU credit for every **1 minute** of full CPU usage.
- If it saves up credits, it can **burst** beyond its normal speed when needed.
- If it runs out of credits, it drops back to **baseline performance** (slow mode).

📌 **Credit Storage:**  
- Unused CPU credits can **accumulate** (save up) for **up to 7 days**.
- If the instance stays idle for a long time, it will have **extra credits** to use later.

---

## **3. CPU Credit Pricing**
- **CPU Credits are not free**—they are **billed separately** from your instance cost.
- If you use too many credits, you may **pay extra** beyond your normal instance pricing.

---

## **4. What is Unlimited Mode?**
By default, **T2 and T3 instances** run in **Standard Mode**, meaning they **stop bursting** when they run out of credits.

AWS offers **Unlimited Mode**, which allows an instance to **keep bursting** even after it runs out of credits.

💡 **How Unlimited Mode Works:**
- If your instance needs more CPU than it has credits for, AWS will **automatically charge you for extra CPU usage**.
- This ensures **consistent performance** but may **increase costs**.

📌 **When to Use Unlimited Mode?**
- If you need **stable performance** without slowdowns.
- If your application **occasionally needs more power** but can’t afford to be throttled.

---

## **5. Choosing the Right CPU Credit Model**
| Mode | How It Works | Best For |
|------|-------------|----------|
| **Standard Mode** (Default) | Stops bursting when credits run out | Small applications that don’t need high performance |
| **Unlimited Mode** | Continues bursting but charges extra | Applications that require consistent performance |

---

## **6. Real-World Example**
### **Scenario 1: Running a Small Website (Standard Mode)**
- You host a **personal blog** on a `t2.micro` instance.
- Most of the time, the traffic is **low**, so your instance **saves up CPU credits**.
- When visitors suddenly increase, it **uses saved CPU credits to handle traffic smoothly**.
- If traffic stays high for too long, the instance slows down because it **runs out of credits**.

### **Scenario 2: Running an E-commerce Site (Unlimited Mode)**
- You run an **online store** where **consistent speed** is important.
- Your instance uses **Unlimited Mode**, so even if CPU credits run out, it **keeps running at full speed**.
- AWS charges you for the **extra CPU usage**, but your customers experience **no slowdowns**.

---

### **Final Thoughts**
- **T2 and T3 instances** use **CPU credits** to balance cost and performance.
- **Credits are earned when idle and spent when active**.
- **Unlimited Mode** keeps performance high but may cost more.
- **Choose based on your application needs**—if performance is crucial, **Unlimited Mode** is a good option.


To check and manage **CPU Credits** for an AWS EC2 instance, follow these steps:

## **1. Check CPU Credit Balance in AWS Console**
1. **Log in** to your AWS account.
2. Navigate to **EC2 Dashboard**.
3. Click on **Instances** in the left menu.
4. Select the instance you want to check (e.g., `t2.micro`, `t3.medium`).
5. Scroll down to the **Monitoring** tab.
6. Look for the metric **"CPU Credit Balance"** (shows how many credits are available).
7. Look for **"CPU Credit Usage"** (shows how many credits are being consumed).

---

## **2. Check CPU Credits Using AWS CLI**
If you have the AWS CLI installed, run this command to check CPU credit balance:

```sh
aws cloudwatch get-metric-statistics \
    --namespace AWS/EC2 \
    --metric-name CPUCreditBalance \
    --dimensions Name=InstanceId,Value=i-xxxxxxxxxxxxxxxxx \
    --statistics Average \
    --period 3600 \
    --start-time $(date -u +"%Y-%m-%dT%H:%M:%SZ" -d "-1 day") \
    --end-time $(date -u +"%Y-%m-%dT%H:%M:%SZ")
```
Replace `i-xxxxxxxxxxxxxxxxx` with your **Instance ID**.

---

## **3. Enable or Disable Unlimited Mode**
To **enable Unlimited Mode** (prevents CPU throttling when credits run out):
1. Go to **EC2 Dashboard** > **Instances**.
2. Select your **T2 or T3 instance**.
3. Click **Actions** > **Modify Instance Credit Specification**.
4. Change the setting to **Unlimited**.
5. Click **Save**.

To **disable Unlimited Mode** (to avoid extra charges), follow the same steps but select **Standard** instead.

---

## **4. Set Up CPU Credit Alerts (Optional)**
If you want AWS to **notify you when credits are low**, set up a **CloudWatch Alarm**:
1. Go to **CloudWatch** in the AWS Console.
2. Click **Alarms** > **Create Alarm**.
3. Select **EC2 Metrics** > **CPUCreditBalance**.
4. Set a threshold (e.g., alert when balance is below `10`).
5. Configure an **email notification** via SNS.
6. Click **Create Alarm**.
