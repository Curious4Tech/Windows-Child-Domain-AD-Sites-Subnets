# Windows-Child-Domain-AD-Sites-Subnets
Step-by-step guide on how to promote a Windows Server to a child domain and configure sites and subnets in Active Directory (AD). This repository includes detailed instructions for deploying a child domain, setting up efficient AD replication using sites and subnets, and ensuring optimal network performance in a multi-site environment.


# **Promote a Windows Server to a Child Domain and Configure Sites and Subnets in Active Directory**

This guide will walk you through the process of promoting a Windows Server to a **child domain** and configuring **sites** and **subnets** in **Active Directory (AD)** to improve network efficiency and directory replication.

---

## **Table of Contents**
1. [Prerequisites](#prerequisites)
2. [Constraints Before Promoting a Child Domain DC.](#constraints-before-promoting-a-child-domain-dc.)
3. [Promoting a Windows Server to a Child Domain](#promoting-a-windows-server-to-a-child-domain)  
4. [Creating Sites and Subnets in Active Directory](#creating-sites-and-subnets-in-active-directory)  
5. [Verification Steps](#verification-steps)  
6. [Best Practices](#best-practices)  
7. [Troubleshooting](#troubleshooting)
8. [Conclusion](#conclusion)

---

## **Prerequisites**
Before proceeding, ensure you have the following:

- A Windows Server with **Active Directory Domain Services (AD DS)** role installed.
- An existing **Active Directory Forest** with a parent domain.
- Domain Administrator credentials for both the **parent domain** and the **child domain**.
- Network details (IP ranges, site names, etc.) for your sites and subnets.

---


# ✅ Constraints Before Promoting a Child Domain DC.


## ✅ **Required Ports for Parent-Child Domain Communication**

Ensure the following ports are open between the child and parent DCs:

| **Protocol** | **Port** | **Description**             |
|--------------|----------|-----------------------------|
| TCP/UDP      | 53       | DNS                          |
| TCP          | 88       | Kerberos                     |
| TCP          | 135      | RPC                          |
| TCP/UDP      | 389      | LDAP                         |
| TCP/UDP      | 445      | SMB                           |
| TCP          | 636      | LDAP over SSL                |
| TCP/UDP      | 3268     | Global Catalog               |


## **✅ Example DNS Setup on Child Domain DC:**

🔧 The child domain DC must use the parent domain DC as its primary DNS server.

| **Server Role**     | **IP Address**   | **DNS Configuration**       |
|---------------------|------------------|-----------------------------|
| Parent Domain DC     | 192.168.1.1      | Self (192.168.1.1)          |
| Child Domain DC      | 192.168.2.1      | 192.168.1.1 (Parent DC IP)  |

---

## **Summary of Constraints:**

| **Constraint**            | **Required?** | **Description**                                |
|---------------------------|---------------|------------------------------------------------|
| DNS Server Configuration   | ✅ Yes        | Child DC must use the parent DC as DNS server. |
| Network Connectivity       | ✅ Yes        | Ensure stable connectivity between DCs.        |
| Trust Relationship         | ✅ Yes        | Automatic trust is created during promotion.   |
| Admin Credentials          | ✅ Yes        | Use Domain Admin or Enterprise Admin.          |
| Time Synchronization       | ✅ Yes        | Ensure time is synced between DCs.             |


---

## **1. Promoting a Windows Server to a Child Domain**

### **Step 1: Install AD DS Role**
1. Open **`Server Manager`** on your Windows Server.
2. Click **`Manage`** > **`Add Roles and Features`**.
3. Select **`Active Directory Domain Services`** and complete the wizard.

### **Step 2: Promote to a Child Domain**
1. After installing the role, click **`Notifications`** (the flag icon) in Server Manager.
2. Click **`Promote this server to a domain controller`**.
3. In the **`Deployment Configuration`** window:
   - Select **`Add a new domain to an existing forest`**.
   - Choose **`Child Domain`**.
   - Enter the parent domain name (e.g., `parent.com`) and the new child domain name (e.g., `child.parent.com`).

4. Provide **`Domain Administrator`** credentials for the parent domain.

5. Configure the following options:
   - **`Domain Controller Capabilities`**: Select **`DNS Server`** and **`Global Catalog (GC)`** as needed.
   - **`Site Name`**: Choose an appropriate site name for the child domain.

6. Complete the **`DNS options`** and **`Paths`** configuration.

7. Review the configuration and click **`Install`**.

8. Once the server restarts, the child domain will be created.

---

## **2. Creating Sites and Subnets in Active Directory**

### **Step 1: Open Active Directory Sites and Services**
1. On a domain controller, open **Active Directory Sites and Services** (`dssite.msc`).

### **Step 2: Create a New Site**
1. Right-click **`Sites`** and select **`New Site`**.
2. Enter a **Site Name** (e.g., `Site1`).
3. Choose an appropriate **`Link Object`** (usually `DEFAULTIPSITELINK`).

### **Step 3: Create a New Subnet**
1. In **`Active Directory Sites and Services`**, right-click **`Subnets`** and select **`New Subnet`**.
2. Enter the **`IP Address`** and **`Subnet Mask`** (e.g., `192.168.1.0/24`).
3. Associate the subnet with the site you created earlier.

### **Step 4: Move Domain Controllers to Sites**
1. Expand the **`Servers`** node under your site.
2. Right-click the **`domain controller`** and select **`Move`**.
3. Choose the correct site.

---

## **3. Verification Steps**

1. Run **`ipconfig /all`** on the child domain controller to ensure it is properly registered with DNS.
2. Use **`Active Directory Users and Computers`** to verify the new child domain.
3. Check the **`Event Viewer`** for any errors or warnings.

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


---

## **Conclusion**  

By following this guide, you have successfully promoted a Windows Server to a **`child domain`** and configured **`sites and subnets`** in **`Active Directory`**. This setup enhances the efficiency of your network by optimizing **`replication traffic`** and ensuring that **`clients authenticate`** to the nearest domain controllers.

Proper configuration of sites and subnets is essential for **`large, distributed networks`**, allowing for better performance, **`faster authentication`**, and **`efficient resource management`**. Remember to regularly monitor and maintain your AD infrastructure to ensure it remains secure, scalable, and resilient.

This repository will be updated with more Active Directory best practices and troubleshooting tips to help you maintain a well-structured AD environment. Feel free to share feedback! 😊
Happy learning! 🎉
