# Windows-Child-Domain-AD-Sites-Subnets
Step-by-step guide on how to promote a Windows Server to a child domain and configure sites and subnets in Active Directory (AD). This repository includes detailed instructions for deploying a child domain, setting up efficient AD replication using sites and subnets, and ensuring optimal network performance in a multi-site environment.


# **Promote a Windows Server to a Child Domain and Configure Sites and Subnets in Active Directory**

This guide will walk you through the process of promoting a Windows Server to a **child domain** and configuring **sites** and **subnets** in **Active Directory (AD)** to improve network efficiency and directory replication.

---

## **Table of Contents**
1. [Prerequisites](#prerequisites)
2. [Promoting a Windows Server to a Child Domain](#promoting-a-windows-server-to-a-child-domain)  
3. [Creating Sites and Subnets in Active Directory](#creating-sites-and-subnets-in-active-directory)  
4. [Verification Steps](#verification-steps)  
5. [Best Practices](#best-practices)  
6. [Troubleshooting](#troubleshooting)
7. [Conclusion](#conclusion)

---

## **Prerequisites**
Before proceeding, ensure you have the following:

- A Windows Server with **Active Directory Domain Services (AD DS)** role installed.
- An existing **Active Directory Forest** with a parent domain.
- Domain Administrator credentials for both the **parent domain** and the **child domain**.
- Network details (IP ranges, site names, etc.) for your sites and subnets.
---

## **1. Promoting a Windows Server to a Child Domain**

### **Step 1: Install AD DS Role**
1. Open **`Server Manager`** on your Windows Server.
2. Click **`Manage`** > **`Add Roles and Features`**.
3. Select **`Active Directory Domain Services`** and complete the wizard.

### **Step 2: Promote to a Child Domain**
1. After installing the role, click **`Notifications`** (the flag icon) in Server Manager.
2. Click **`Promote this server to a domain controller`**.

![image](https://github.com/user-attachments/assets/869aef74-30f0-42f4-8b7a-a0a3cf439798)

3. In the **`Deployment Configuration`** window:
   - Select **`Add a new domain to an existing forest`**.
   - Choose **`Child Domain`**.
   - Enter the parent domain name (e.g., `parent.com`) and the new child domain name (e.g., `child`).

![image](https://github.com/user-attachments/assets/7c2fc07e-b87b-4b54-bcc3-f9ee495f205b)


5. Click on **`Change`** and provide **`Domain Administrator`** credentials for the parent domain.

![image](https://github.com/user-attachments/assets/4aa5954b-66d8-4c5d-b3c2-6c1b09b27e16)

6. Now, everything is ok, you can click on **`Next`** to continue.

![image](https://github.com/user-attachments/assets/92f6c3a4-11bf-4398-8986-dc0a2ea4e3da)


7. Configure the following options:
   - **`Domain Controller Capabilities`**: Select **`DNS Server`** and **`Global Catalog (GC)`** as needed.
   - **`Site Name`**: Choose an appropriate site name for the child domain or let the default name.
   - Provide the **`DSRM`** password and click on **`Next`**

![image](https://github.com/user-attachments/assets/2408035b-546a-422a-9922-e29b75c17701)


9. Complete the **`DNS options`** and **`Paths`** configuration.

![image](https://github.com/user-attachments/assets/2ecccc9c-6862-44c7-9c9f-8e860c0e14ac)

- For  **`NetBIOS`** and **`Paths`** let the default informations.

10. Review the configuration and click **`Install`**.

11. Once the server restarts, the child domain will be created and restart automatically your DC.

![image](https://github.com/user-attachments/assets/b230f3ab-2ced-42ae-adcc-21f4ceab5a2f)

12. Now, provide your credentials to connect.

![image](https://github.com/user-attachments/assets/3e643ba4-ce1b-47ff-9c54-0ff36683a86a)

---

## **2. Creating Sites and Subnets in Active Directory**

For this part, let's use our parent domain DC. Signin with your parent domain credentials.

![image](https://github.com/user-attachments/assets/7cca2dbe-33b0-4308-bb66-a742ede4d2fc)


### **Step 1: Open Active Directory Sites and Services**
1. On a domain controller, open **`Active Directory Sites and Services`** by clicking on **`Windows + R`** and then  **`dssite.msc`**.

![image](https://github.com/user-attachments/assets/0ccad542-364a-4d31-a491-e24b464859f3)

### **Step 2: Create a New Site**
1. Right-click **`Sites`** and select **`New Site`**.

![image](https://github.com/user-attachments/assets/1366a10d-7227-4d2f-b7b4-dd5f6c3254b3)

3. Enter a **Site Name** (e.g., `Paris`).
4. Choose an appropriate **`Link Object`** (usually `DEFAULTIPSITELINK`).

![image](https://github.com/user-attachments/assets/c4c1b3fa-cb93-4519-b9ec-ce1a5f8a5f50)

5. Let's us rename our default site.
   
  - Right-click on the **`default site name`** > **`rename`**

![image](https://github.com/user-attachments/assets/16d1224e-81eb-4c3a-a6e5-bd7ccf1a0b6f)

  - Give it a new name (e.g., `London`) and then click **`OK`**.

![image](https://github.com/user-attachments/assets/ae566f5d-2087-4a80-b056-dc85827ba7b6)


### **Step 3: Create a New Subnet**
1. In **`Active Directory Sites and Services`**, right-click **`Subnets`** and select **`New Subnet`**.

![image](https://github.com/user-attachments/assets/718123bb-e1f6-403d-989c-5d21a1c17186)

3. Enter the **`IP Address`** and **`Subnet Mask`** (e.g., `192.168.2.0/24`), depending on your IP adress range.
4. Associate the subnet with the site you created earlier and click on **`OK`**.

![image](https://github.com/user-attachments/assets/a763f2b3-6311-4f08-8ae5-a15b71aa5a19)

You can repeat the same process for **`London`** site (e.g., `192.168.0.0/16`), depending on your network adress space range.

### **Step 4: Move Domain Controllers to Sites**
1. Expand the **`Servers`** node under your site (default site which we renamed `London`).
2. Right-click the **`domain controller`** and select **`Move`**.

![image](https://github.com/user-attachments/assets/d2ab34d2-c0b3-457c-83aa-ad19c9db5b48)

4. Choose the correct site (e.g., `Paris`). In this example we want to move the DC2 to **`Paris`** site.

![image](https://github.com/user-attachments/assets/55370690-924b-445e-88c3-28372500fd84)

5. Everything is setup now.

![image](https://github.com/user-attachments/assets/86ab2bf0-9a86-4a05-8354-dab39e172c89)

---

## **3. Verification Steps**

1. Run **`ipconfig /all`** on the child domain controller to ensure it is properly registered with DNS.
2. Use **`Active Directory Users and Computers`** to verify  domains (parent and child).

- Parent domain

![image](https://github.com/user-attachments/assets/34b14688-a1d4-4d29-b1d5-bb8d06726227)

  
- Child domain

![image](https://github.com/user-attachments/assets/21b9a357-4e0a-4cc9-aea6-5357f35f84ed)


4. Check the **`Event Viewer`** for any errors or warnings.

---

## **4. Best Practices**

- Ensure all domain controllers have static IP addresses.
- Place **`Domain Controllers`** in appropriate sites to optimize replication.
- Use **`subnets`** to ensure clients authenticate with the closest domain controller.
- Regularly monitor **`replication health`** using tools like **`repadmin`**.

---

## **5. Troubleshooting**

| **Issue**                     | **Solution**                                                        |
|--------------------------------|----------------------------------------------------------------------|
| Child domain promotion fails   | Ensure the parent domain controller is reachable and DNS is correctly configured. |
| Replication issues             | Use **`repadmin /showrepl`** to check replication status.            |
| Site not functioning correctly | Verify that the subnet is correctly linked to the site.              |



## ✅ **Key Constraints Before Promoting a Child Domain DC:**

## ❌ **Common Issues if Constraints Are Not Met:**

| **Issue**                          | **Cause**                                                | **Solution**                                                |
|------------------------------------|----------------------------------------------------------|-------------------------------------------------------------|
| DNS errors during promotion         | Child DC cannot locate parent DC                         | Set the **parent DC’s IP** as the **primary DNS server**.    |
| Network connectivity errors         | Firewall or routing issues between DCs                   | Ensure **ports are open** and **DCs can ping each other**.   |
| Authentication errors               | Incorrect credentials or trust issues                    | Use **Domain Admin** or **Enterprise Admin** credentials.    |
| Time sync errors                    | Time mismatch between parent and child DCs               | Sync time using **NTP server** or **parent DC**.             |


## **✅ Example DNS Setup on Child Domain DC:**

🔧 The child domain DC must use the parent domain DC as its primary DNS server.

| **Server Role**     | **IP Address**   | **DNS Configuration**       |
|---------------------|------------------|-----------------------------|
| Parent Domain DC     | 192.168.1.1      | Self (192.168.1.1)          |
| Child Domain DC      | 192.168.2.1      | 192.168.1.1 (Parent DC IP)  |


## **Summary of Constraints:**

| **Constraint**            | **Required?** | **Description**                                |
|---------------------------|---------------|------------------------------------------------|
| DNS Server Configuration   | ✅ Yes        | Child DC must use the parent DC as DNS server. |
| Network Connectivity       | ✅ Yes        | Ensure stable connectivity between DCs.        |
| Trust Relationship         | ✅ Yes        | Automatic trust is created during promotion.   |
| Admin Credentials          | ✅ Yes        | Use Domain Admin or Enterprise Admin.          |
| Time Synchronization       | ✅ Yes        | Ensure time is synced between DCs.             |

---

## **6. Conclusion**  

By following this guide, you have successfully promoted a Windows Server to a **`child domain`** and configured **`sites and subnets`** in **`Active Directory`**. This setup enhances the efficiency of your network by optimizing **`replication traffic`** and ensuring that **`clients authenticate`** to the nearest domain controllers.

Proper configuration of sites and subnets is essential for **`large, distributed networks`**, allowing for better performance, **`faster authentication`**, and **`efficient resource management`**. Remember to regularly monitor and maintain your AD infrastructure to ensure it remains secure, scalable, and resilient.

This repository will be updated with more Active Directory best practices and troubleshooting tips to help you maintain a well-structured AD environment. Feel free to share feedback! 😊
Happy learning! 🎉
