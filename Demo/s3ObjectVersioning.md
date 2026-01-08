- **Object Versioning Overview**:
    - Controlled at the bucket level.
    - Default state: **Disabled**.
    - Once **enabled**, it **cannot be disabled** again.
    - You can **suspend** versioning on a bucket after enabling it.
    - A **suspended bucket** can later be **re-enabled**.
- **State Transitions**:
    - **Disabled → Enabled**: Possible.
    - **Enabled → Suspended**: Possible.
    - **Suspended → Enabled**: Possible.
    - **Enabled → Disabled**: **Not possible**.
- **Key Exam Tip**:
    - Remember the state transitions explicitly:
        - **Enabled buckets cannot return to a disabled state.**
    - Expect trick questions testing this concept.
- Use-Case Scenarios
    1. **Versioning Enabled**:
        - Use when you need to retain multiple versions of objects (e.g., for rollback or data recovery).
        - Ideal for sensitive or critical data to prevent accidental deletions or overwrites.
    2. **Versioning Suspended**:
        - Use when versioning is temporarily unnecessary but might be needed again in the future.
        - Helps reduce cost by halting versioning temporarily while retaining the option to re-enable.

![Screenshot 2024-12-26 at 7.57.41 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9093c36a-b051-436e-9a55-bbb963b99104/12745c17-91f6-4402-a7f5-e149c06b2407/Screenshot_2024-12-26_at_7.57.41_PM.png)

- **Object Identification Without Versioning**:
    - Each object is identified by a **unique key** (its name) within the bucket.
    - Modifying or deleting an object **replaces the original version**.
    - Objects in buckets without versioning have an **ID of `null`**.
- **Object Identification With Versioning**:
    - **Versioning enabled** allows multiple versions of the same object to coexist in a bucket.
    - Each object version is assigned a **unique ID**.
    - Modifying an object creates a **new version** with a new ID, leaving the original version intact.
- **Current Version**:
    - The most recent version of an object is called the **current version**.
    - Accessing an object without specifying a version ID defaults to retrieving the **current version**.
- **Accessing Specific Versions**:
    - You can request a specific object version by providing its **unique ID** to S3.
    - If no ID is specified, the **current version** is assumed and returned.
- **Example Scenario**:
    - A bucket contains an object `Winky.jpeg`.
        - Initially:
            - Versioning **disabled**: Object ID = `null`.
        - After enabling versioning:
            - Uploads or modifications generate unique IDs for new versions (e.g., `111111`, `222222`).
            - If `Winky.jpeg` is overwritten with a dog picture:
                - Original version remains (`ID: 111111`).
                - New version becomes the **current version** (`ID: 222222`).
- Use-Case Scenarios
    1. **Versioning Disabled**:
        - **Scenario**: A bucket storing transient data with no need for recovery or version tracking.
        - **Implication**: Modifications or deletions overwrite the object permanently.
    2. **Versioning Enabled**:
        - **Scenario**: A bucket storing critical data where accidental overwrites or deletions need to be reversible.
        - **Implication**: S3 retains previous versions of objects. The current version is always returned unless a specific version ID is requested.
    3. **Accessing Specific Versions**:
        - **Scenario**: A user accidentally overwrites `Report.pdf` but needs the earlier version.
        - **Solution**: Use the unique ID of the previous version to retrieve it from S3.
    4. **Default Version Access**:
        - **Scenario**: A system retrieves an image `logo.png` from S3 without specifying a version.
        - **Result**: The system will automatically get the **current version** of `logo.png`.

![Screenshot 2024-12-26 at 8.00.22 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9093c36a-b051-436e-9a55-bbb963b99104/f5755608-a0e2-42cf-a757-8d29a096ad89/Screenshot_2024-12-26_at_8.00.22_PM.png)

- **Impact of Versioning on Deletions**:
    - **Deleting without specifying a version ID**:
        - S3 adds a **delete marker**, a special version of the object.
        - The delete marker hides all previous versions, making the object appear deleted.
        - Previous versions still exist and can be accessed by their **unique version IDs**.
- **Delete Marker**:
    - A **new version** that acts as a placeholder to indicate deletion.
    - Makes the object appear as if it is deleted, but it is only hidden.
    - Removing the delete marker effectively "undeletes" the object:
        - The previous **current version** becomes active again.
        - All earlier versions remain accessible by their version IDs.
- **Permanently Deleting a Specific Version**:
    - To truly delete an object version:
        - Specify the **version ID** during the deletion operation.
        - S3 **permanently removes** the specified version.
    - If the deleted version is the **current version**:
        - The next most recent version becomes the **new current version**.
- Use-Case Scenarios
    1. **Delete Marker Usage**:
        - **Scenario**: A user deletes `Document.pdf` in a version-enabled bucket without specifying a version ID.
        - **Result**:
            - A delete marker is added.
            - The document is hidden, but earlier versions remain accessible by their IDs.
    2. **Undoing a Deletion**:
        - **Scenario**: A user mistakenly deletes `Photo.jpg` and wants to restore it.
        - **Solution**:
            - Delete the delete marker.
            - The most recent version prior to the deletion becomes the active version again.
    3. **Permanent Deletion of Specific Versions**:
        - **Scenario**: A compliance policy requires permanent removal of old object versions.
        - **Solution**:
            - Identify the version IDs to be removed.
            - Delete those specific versions from the bucket.
    4. **Changing Current Version After Deletion**:
        - **Scenario**: The current version of `Report.docx` (ID: `333333`) is deleted.
        - **Result**:
            - The next most recent version (e.g., ID: `222222`) becomes the new **current version**.

![Screenshot 2024-12-26 at 8.03.56 PM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9093c36a-b051-436e-9a55-bbb963b99104/a191a9a2-e878-4498-a6ea-19a27db8d1d7/Screenshot_2024-12-26_at_8.03.56_PM.png)

- **Key Points on Object Versioning**:
    - **Versioning cannot be disabled** once enabled; it can only be **suspended**.
    - When versioning is enabled:
        - All versions of an object are retained in the bucket.
        - Retaining multiple versions consumes storage space.
            - Example: A 5 GB object with 5 versions consumes **25 GB of space**.
        - Costs are incurred for all stored versions.
    - **Suspending versioning**:
        - Does not delete old versions.
        - You continue to incur costs for the old versions.
- **Reducing Costs**:
    - The only way to eliminate costs related to old versions:
        - Delete the bucket and re-upload objects to a bucket **without versioning enabled**.
- **MFA Delete**:
    - A feature within the **versioning configuration** of a bucket.
    - When enabled, MFA is required for:
        1. **Changing the versioning state** of the bucket (e.g., from enabled to suspended).
        2. **Deleting specific object versions**.
- **How MFA Delete Works**:
    - During API calls to:
        - Change bucket versioning states.
        - Delete specific object versions.
    - You must provide:
        1. The **serial number** of your MFA token.
        2. The **code generated** by the MFA device.
    - These values are concatenated and passed with the API call
- Use Case scenarios:
    - **Versioning Enabled**:
        - **Scenario**: A development team stores critical logs in a version-enabled bucket.
        - **Implication**:
            - Logs are retained even if accidentally overwritten.
            - However, the team must monitor storage costs due to multiple retained versions.
    - **Suspending Versioning**:
        - **Scenario**: A company no longer needs to track object versions but wants to retain the existing bucket setup.
        - **Solution**:
            - Suspend versioning to stop creating new versions while keeping old versions accessible.
    - **Using MFA Delete**:
        - **Scenario**: A finance department secures sensitive reports stored in S3 to prevent unauthorized version deletions or state changes.
        - **Solution**:
            - Enable **MFA Delete** to require an MFA token for any versioning changes or deletions.
    - **Cost Management**:
        - **Scenario**: A bucket with high storage costs due to old object versions.
        - **Solution**:
            - Assess the bucket’s contents, delete unnecessary versions, or migrate objects to a non-versioned bucket to reset costs.