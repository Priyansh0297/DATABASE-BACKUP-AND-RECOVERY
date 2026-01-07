# DATABASE-BACKUP-AND-RECOVERY
COMPANY*: Codtech IT Solutions Private Limited
NAME*: Priyansh sahu
INTERN ID*: CTIS0111
DOMAIN*: SQL
DURATION*: 4 Weeks
MENTOR*: Neela Santhosh Kumar 


# **Report: MySQL Database Backup & Recovery**

** Automated Disaster Recovery Implementation for `test_db`

### **1. Executive Summary**

In the world of database management, data loss is not a question of "if," but "when." Whether caused by hardware failure, a software bug, or accidental deletion (human error), the loss of data can be catastrophic.

This report documents the implementation of a robust, automated backup and recovery system for the MySQL database `test_db`. Previously, we relied on manual command-line entries, which were susceptible to syntax errors and platform-specific conflicts (specifically within Windows PowerShell). To resolve this, we have transitioned to **Batch Automation**. This new system allows for "one-click" backups and secure restoration, ensuring that critical data is preserved with minimal manual intervention.

### **2. The Challenge: Moving Beyond Manual Commands**

During our initial testing, we identified two significant risks with the manual approach:

1. **Complexity & Repetition:** Typing complex commands like `mysqldump -u root -p...` every day is tedious. It invites typos, such as forgetting the database name or misplacing a flag.
2. **Platform Compatibility:** We encountered a specific technical hurdle using Windows PowerShell. The standard input redirection operator (`<`), which is essential for restoring data, is reserved for future use in PowerShell. This caused the restore command to fail with a `ParserError`, preventing us from loading our data back into the system.

To solve both issues simultaneously, we created **Batch Scripts (`.bat`)**. These scripts run in the native Windows Command Prompt environment, bypassing the PowerShell limitation entirely and automating the complex syntax.

### **3. The Solution Components**

We have delivered two distinct automation tools. Think of these as the "Save" and "Undo" buttons for your database.

#### **A. The Backup Script (`backup_script.bat`)**

This script is designed to run silently and efficiently. Its primary goal is version control. If we simply named our backup file `backup.sql`, every new backup would overwrite the previous one. If you made a mistake on Tuesday and backed it up, you would lose the clean version from Monday.

To prevent this, our script includes a **Timestamping Logic**.

* **How it works:** When you double-click the script, it asks the system for the current date and time.
* **The Output:** It generates a file named uniquely, such as `backup_test_db_20260107_2330.sql`.
* **The Benefit:** You can keep a history of your data. You can go back to how the database looked yesterday, last week, or last month.

#### **B. The Recovery Script (`restore_script.bat`)**

Restoring a database is a dangerous operation because it overwrites existing data. If you accidentally restore an old file over your current work, you lose your recent progress.

To mitigate this risk, we added a **Safety Interlock**:

* **User Prompt:** The script does not run immediately. It asks you to verify the filename you want to load.
* **Confirmation Step:** It explicitly asks, *"Are you sure? (Type Y to proceed)."* If you do not type "Y", the script aborts. This small pause forces the user to think twice before making irreversible changes.

### **4. Technical Deep Dive: The "Redirection" Fix**

The core technical achievement of this implementation is resolving the "Input Redirection" error.

In standard MySQL documentation, the command to restore data is often written as:
`mysql ... < backup.sql`

As we discovered, Windows PowerShell interprets the `<` symbol differently than the Command Prompt (cmd.exe). It sees it as a reserved operator rather than a "feed from file" instruction. By wrapping our commands in a **Batch file**, we force Windows to execute the instructions using the legacy Command Prompt interpreter. This ensures the `<` operator functions exactly as intended, seamlessly feeding the SQL instructions from our text file back into the database engine without any syntax errors.

### **5. Operational Guide (Step-by-Step)**

**Prerequisite:** Ensure both `.bat` scripts are saved in the same folder where you want your backups to reside.

**Procedure 1: Creating a Safety Net (Backup)**

1. Navigate to your script folder.
2. Double-click `backup_script.bat`.
3. A black window will appear. The script will connect to MySQL using the credentials stored inside the file.
4. Upon completion, look for the "SUCCESS" message.
5. **Verification:** Check the folder. You should see a new `.sql` file stamped with today's date.

**Procedure 2: Recovering from Disaster (Restore)**

1. Locate the specific backup file you wish to restore (e.g., `backup_test_db_20260107.sql`). Copy this filename.
2. Double-click `restore_script.bat`.
3. When prompted, paste the filename and press Enter.
4. Review the warning message. If you are certain, type `Y` and press Enter.
5. The script will read the file and rebuild your tables and data.

### **6. Conclusion**

We have successfully transformed a manual, error-prone technical task into a reliable, automated workflow. By implementing these scripts, we have not only fixed the immediate syntax errors associated with PowerShell but also established a professional data governance practice.

This system provides peace of mind. You no longer need to worry about remembering complex commands or fearing accidental data loss. Your data is now safe, versioned, and easily recoverable.

---
