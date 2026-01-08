- S3 security (resource policies & ACLs)
    - S3 is private by default.
    - The only identity which has any initial access to an s3 bucket is the account is the root user of the account which owns that bucket, so the account which created it
    - So any other permissions have to be explicitly granted, and there are a few ways this can be done.
    - s3 bucket policies
        
        ![Screenshot 2024-12-05 at 7.54.36 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9093c36a-b051-436e-9a55-bbb963b99104/8be0be7a-25ad-4013-b8d7-bbf71f13792c/Screenshot_2024-12-05_at_7.54.36_AM.png)
        
        - The first way is using an s3 bucket policy.
            - An an s3 bucket policy is a type of resource policy.
            - A resource policy is just like an identity policy, but as the name suggests, they're attached to resources instead of identities.
            - In this case, an s3 bucket resource policies provide a resource perspective on permissions. The difference between resource policies and identity policies is all about this perspective.
            - With identity policies, you're controlling what that identity can access.
            - With resource policies, you're controlling who can access that resource. So it's from an inverse perspective, one is identities and one is resources.
            - Now identity policies have one pretty significant limitation. You can only attach identity policies to identities in your own account, and so identity policies can only control security inside your account. With identity policies, you have no way of giving an identity in another account access to an s3 bucket. That would require an action inside that other account.
            - Resource policies allow this. They can allow access from the same account or different accounts, because the policy is attached to the resource and it can reference any other identities inside that policy by attaching the policy to the resource and then having flexibility to be able to reference any other identity, whether they're in the same account or different accounts, resource policies, therefore, are a great way of controlling access for a particular resource, no matter what the source of that access is.
            - Now think about that for a minute, because that's a major benefit of resource policies, the ability to grant other accounts access to resources inside your account.
            - They also have another benefit. Resource policies can allow or deny anonymous principles. Identity policies, by design, have to be attached to a valid identity in AWS. You can't have one attached to nothing.
            - Resource policies can be used to open a bucket to the world by referencing all principles, even those not authenticated by AWS. So that's anonymous principles. So bucket policies can be used to grant anonymous access. So two of the very common use of a bucket policies are to grant access to other AWS accounts and anonymous access to a bucket. Let's take a look at a simple visual example of a bucket policy.
        
        ![Screenshot 2024-12-05 at 7.58.25 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9093c36a-b051-436e-9a55-bbb963b99104/217ec205-b33e-48ba-b600-caa7050c821d/Screenshot_2024-12-05_at_7.58.25_AM.png)
        
        - Let's say that we have an AWS account, and inside this account is bucket called Secret cat project. Now I can't say What's Inside this bucket, because it's a secret, but I'm sure that you can guess. Attached to this bucket is a bucket policy.
        - Resource policies have one major difference to identity policies, and that's the presence of an explicit principle component. The principal part of a resource policy defines which principles are affected by the policy. So the policy is attached to a bucket in this case, but we need a way to say who is impacted by the configuration of that policy.
        - Because a bucket policy can contain multiple statements, there might be one statement which affects your account and one which affects another account, as well as one which affects a specific user.
        - The principal part of a policy, or, more specifically, the principal part of the statement in a policy defines who that statement applies to, which identities, which principles.
        - Now in an identity policy, this generally isn't there, because it's implied that the identity which the policy is applied to is the principle. Your identity policy, by definition, applies to you. So you are the principal. So a good way of identifying if a policy, a resource policy, or an identity policy, is the presence of this principal component. If it's there, it's probably a resource policy.
        - In this case, the principle is a wild card, a star, which means any principle. So this policy applies to anyone accessing the s3 bucket.
        - So let's interpret this policy well. First, the effect is allow. Principle is star, so any principle, so this effect allows any principle to perform the action s3 get object on any object inside the secret cat project s3 bucket. So in effect, it allows anyone to read any objects inside this bucket. So this would equally apply to identities in the same AWS account as the bucket. It could also apply to other AWS accounts, so a partner account, and crucially, it also applies to anonymous principles, so principals who haven't authenticated to AWS.
        - Bucket policies should be your default thought when it comes to granting anonymous access to objects in buckets, and they're one way of granting external accounts that access.
        - They can also be used to set the default permissions on a bucket If you want to grant everyone access to Boris's picture, for example, and then grant certain identities extra rights, or even deny certain rights, then you can do that. Bucket policies are really flexible. They can do many other things. So let's quickly just look at a couple of common examples.
        
        ![Screenshot 2024-12-05 at 7.59.53 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9093c36a-b051-436e-9a55-bbb963b99104/e1b139d3-9dee-401e-b686-260d3fc5f68d/Screenshot_2024-12-05_at_7.59.53_AM.png)
        
        - Bucket policies can be used to control who can access objects, even allowing conditions which block specific IP addresses. In this example, this bucket policy denies access to any objects in the secret cat project bucket unless your IP address is 1.3.3.7. The condition block here means this statement only applies if this condition is true. So if your IP address, the source IP address is not 1.3.3.7 then the statement applies and access is denied. If your IP address is 1.3.3.7 then this condition is not met because it's a “not IP address” condition. So if your IP address is this IP address, the condition is not matched, and you get any other access that's applicable. Essentially, this statement, which is a deny, does not apply.
        
        ![Screenshot 2024-12-05 at 8.00.28 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9093c36a-b051-436e-9a55-bbb963b99104/b0816838-3cb4-4a46-a77d-980b9063dbdb/Screenshot_2024-12-05_at_8.00.28_AM.png)
        
        - Now, bucket policies can be much more complex. In this example, one specific prefix in the bucket, remember, this is what a folder really is inside a bucket. So one specific prefix, called Boris, is protected with an MFA. It means that accesses to the Boris folder in the bucket are denied if the identity that you're using does not use MFA.
        - The second statement allows read access to objects in the whole bucket. Because an explicit deny overrides an allow the top statement applies to just that specific prefix in the bucket. So just Boris.
        - In summary, a resource policy is associated with a resource. A bucket policy, which is a type of resource policy is logically associated with a bucket, which is a type of resource.
        - There can only be one bucket policy on a bucket, but it can have multiple statements if an identity inside one AWS account is accessing a bucket also in that same account, and the effective access is a combination of all of the applicable identity policies, plus the resource policy, so the bucket policy.
        - For any anonymous access, so access by an anonymous principle, then only the bucket policy applies, because logically, if it's an anonymous principle, it's not authenticated, and so no identity policies apply.
        - Now, if the entity in an external AWS account attempts to access a bucket in your account. Your bucket policy applies as well as anything that's in their identity policies. So there's a two step process if you're doing cross account access. The identity in their account needs to be able to access s3 in general, and your bucket and then your bucket policy needs to allow access from that identity, so from that external account.
        
        ![Screenshot 2024-12-05 at 8.03.45 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9093c36a-b051-436e-9a55-bbb963b99104/1267160b-7488-4d9e-a1fa-40d006e2eefb/Screenshot_2024-12-05_at_8.03.45_AM.png)
        
        - ACLs - Now, there is another form of s3 security. It's used less often these days. Access Control Lists or ACLs are ways to apply security to objects or buckets. There is a resource of that object or of that bucket. Remember, in the s3 introduction lesson earlier in the course, I talked about sub resources. Well, this is one of those resources. Now, I almost didn't want to talk about ACLs, because they are legacy AWS. Don't even recommend their use, and prefer that you use bucket policies or identity policies.
        - Part of the reason they aren't used all that often, and that bucket policies have replaced much of what they do, is that they're actually inflexible and only allow very simple permissions. They can't have conditions like bucket policies, and so you're restricted from very broad conditions.
        - 
        
        ![Screenshot 2024-12-05 at 8.04.31 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9093c36a-b051-436e-9a55-bbb963b99104/7d57a486-b11e-42ff-a11e-e27327172f16/Screenshot_2024-12-05_at_8.04.31_AM.png)
        
        - this is an example of what permissions can be controlled using an ACL.There are five permissions which can be granted in an ACL, read, write, read ACP, write ACP and full control.
        - What these five things do depend on if they're applied to a bucket or an object. Read permissions, for example, on a bucket,  allow you to list all objects in that bucket. Whereas write permissions on a bucket, allow the grantee, which is the principle being granted those permissions, the ability to overwrite and delete any object in that bucket. Read permissions on an object, allow the grantee just to read the objects specifically as well as its metadata.
        - Now, with ACLs, you either configure an ACL on the bucket or you configure the ACL on an object, but you don't have the flexibility of being able to have a single ACL that affects a group of objects. You can't do that. That's one of the reasons that a bucket policy is significantly more flexible. It is honestly so much less flexible than a bucket policy to the extent where I won't waste your time with it anymore. It's legacy, and I suspect at some point it won't be used anymore.
        - It's best to almost ignore the fact that they exist.
    - Block public access settings
        
        ![Screenshot 2024-12-05 at 8.08.33 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9093c36a-b051-436e-9a55-bbb963b99104/2f7c2cd7-fa9b-4d63-8ffc-f588b863185f/Screenshot_2024-12-05_at_8.08.33_AM.png)
        
        - Now, before we finish up one final feature of s3 permissions, and that's the block public access settings. In the overall lifetime of the s3 product, this was actually added fairly recently, and it was added in response to lots of public PR disasters where buckets were being configured incorrectly and being set so that they were open to the world. This resulted in a lot of data leaks, and the root cause was a mixture of genuine mistakes or administrators who didn't fully understand the s3 permissions model. So consider this example, an s3 bucket with resource permissions granting public access until block public access was introduced. If you had public access configured, the public could logically access a bucket. Public Access, in this sense, is read only to any objects defined in a resource policy on a bucket, so there's no restrictions. Public Access is public access.
        - Block public Access added a further level of security, another boundary, and on this boundary is the block public access settings which apply no matter what the bucket policies say, but they apply to just the public access so not any other defined AWS identities. So these settings will only apply to an anonymous principal, somebody who isn't an AWS identity attempt to access a bucket using these public access configurations. Now, these settings can be set when you create bucket and adjusted afterwards. They're pretty simple to understand.
        - You choose the top option, which blocks any public access to the bucket no matter what the resource policy says. Its a full override, a fail safe
        - Or you can choose the second option, which allows any public access granted by any existing ACLs when you enable the setting, but it blocks any new ones.
        - The third option blocks any public access granted by ACLs no matter if it was enabled before or after the block public access settings were enabled.
        - The fourth option allows any existing public access granted by bucket policies or access point policies, so anything enabled at the time when you enable this specific block public access setting, they’re allowed to continue, but it blocks any new ones.
        - The fifth option blocks any new and existing from granting any public access.
        - If you're ever in a situation where you wanted public access and it doesn't work, these are probably the settings which are causing that inconsistency.
        
        ### **1. Block all public access**
        
        - **Explanation**: This setting combines all four options below. Turning it on ensures that no public access (via ACLs, bucket policies, or access point policies) is granted to the bucket or objects.
        - **Use Case**:
            - Ideal for private buckets storing sensitive data, such as backups, logs, or financial records.
            - Use this when you want a blanket denial of public access for compliance or security purposes.
        
        ### **2. Block public access to buckets and objects granted through new access control lists (ACLs)**
        
        - **Explanation**: Prevents public access granted by new ACLs. It doesn’t affect existing ACLs but blocks future attempts to make objects public via ACLs.
        - **Scenario**:
            - If your organization uses IAM policies or bucket policies for permissions and doesn't want accidental public access via ACLs.
        - **Use Case**:
            - When onboarding new developers, you want to enforce security best practices by restricting ACL use.
        
        ### **3. Block public access to buckets and objects granted through any access control lists (ACLs)**
        
        - **Explanation**: Ignores all existing ACLs that grant public access and blocks the creation of new ACLs that grant public access.
        - **Scenario**:
            - You’ve inherited a bucket with objects configured using ACLs but want to ensure the bucket is fully private moving forward.
        - **Use Case**:
            - A shared bucket used across teams where only bucket policies or IAM permissions should control access, not ACLs
        
        ### **4. Block public access to buckets and objects granted through new public bucket or access point policies**
        
        - **Explanation**: Prevents the addition of new bucket policies or access point policies that allow public access.
        - **Scenario**:
            - You’re managing a team that creates bucket policies frequently, and you want to ensure no new policies allow public access by mistake.
        - **Use Case**:
            - Static website hosting buckets where public access is required only for specific use cases. This ensures no accidental changes compromise security.
        
        ### **5. Block public and cross-account access to buckets and objects through any public bucket or access point policies**
        
        - **Explanation**: Blocks public and cross-account access even if it is explicitly allowed in the bucket policy or access point policy.
        - **Scenario**:
            - If you have a bucket containing sensitive internal data that should only be accessible by your account, regardless of policy changes.
        - **Use Case**:
            - A log bucket where external access (even cross-account) is strictly forbidden for regulatory compliance, like in healthcare or finance industries.
        
        ### **Key Scenarios to Consider:**
        
        1. **Public Website Hosting**:
            - Leave **Block all public access** turned off but carefully configure bucket policies to allow public access only to specific objects (e.g., images or HTML files).
        2. **Cross-Account Sharing**:
            - Use the **Block public and cross-account access** setting if no cross-account access is required.
            - Turn this off only when sharing specific resources with trusted accounts and monitor policies carefully.
        3. **Enterprise-Level Compliance**:
            - Turn on **Block all public access** to enforce private access for buckets used to store critical business data.
        4. **Dev/Test Environments**:
            - Turn off some public access blocks if testing requires public access, but apply strict monitoring and re-enable blocks afterward.
        5. **CloudTrail Logging or Backup Buckets**:
            - Use **Block all public access** to secure these buckets since they should remain private to your account.
        
        Let’s clarify how the **5th option ("Block public and cross-account access through any public bucket or access point policies")** differs from turning on **"Block all public access."**
        
        ### **Key Difference**
        
        - **"Block all public access"**:
            - A **blanket setting** that combines all four sub-options into one.
            - When you enable this, it overrides all public access, regardless of the mechanism (ACLs, bucket policies, or access point policies).
        - **"Block public and cross-account access through any public bucket or access point policies" (5th option)**:
            - More specific. It **only** prevents **public access** and **cross-account access** granted via bucket policies or access point policies.
            - It doesn’t impact ACLs or other forms of permissions unless explicitly configured.
    - Exam powerup
        
        ![Screenshot 2024-12-05 at 8.09.36 AM.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/9093c36a-b051-436e-9a55-bbb963b99104/39889940-5260-4e78-960d-c33ff071dec6/Screenshot_2024-12-05_at_8.09.36_AM.png)
        
        - So these are just some key points on how to remember all of the theory that I've discussed in this lesson. When I first started in AWS, I found it hard to know from instinct when to use identity policies versus resource policies versus ACLs.
        - choosing between resource policies and identity policies, much of the time, is a preference thing. Do you want to control permissions from the perspective of a bucket, or do you want to grant or deny access from the perspective of the identities accessing bucket? Are you looking to configure one user accessing 10 different buckets or 100 users in the same bucket? It's often personal choice, a choice on what makes sense for your situation and business. So there's often no right answer, but there are some situations where one makes sense over the other.
        - If you're granting or denying permissions of lots of different resources across an AWS account, then you need to use identity policies, because not every service supports resource policies, and besides, you would need a resource policy for each service. So that doesn't make sense if you're controlling lots of different resources.
        - If you have a preference for managing permissions all in one place, that single place needs to be IAM, so identity policies would make sense. IAM is the only single place in AWS, you can control permissions for everything. You can sometimes use resource policies, but you can use IAM policies all of the time if you're only working with permissions within the same account, so no external access, then identity policies within IAM are fine, because with IAM you can only manage permissions for identities that you control in your account.
        - So there are a wide range of situations where Iam makes sense, and that's why most permissions control is done within Iam.
        - But there are some situations which are different. You can use bucket policies or resource policies in general if you’re managing permissions on a specific product. So in this case, s3 if you want to grant a single permission to everybody accessing one resource or everybody in one account, then its much more efficient to use resource policies to control that base level permission. If you want to directly allow anonymous identities or external identities from other AWS accounts to access a resource, then you should use resource policies.
        - Never use ACLs unless you really need to, and even then, consider if you can use something else at this point.
        - If you are using an ACL you have to be pretty certain that you can't use anything else because their legacy and they're inflexible, and AWS are actively recommending against their use, so keep that in mind.