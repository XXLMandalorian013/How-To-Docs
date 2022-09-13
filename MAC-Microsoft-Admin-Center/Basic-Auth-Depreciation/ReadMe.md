# Exchange Online: Basic Auth Depreciation

## Prerequisites/Notes:

1. Outlook must be versions 2013 or newer to be in compliance.
2. Must use Exchange Online PowerShell V2 Modules to access Exchange remotely via PowerShell going forward.
3. Must be an [Exchange Admin or higher](https://docs.microsoft.com/en-us/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide) to make these changes.
4. MFA enabled
5. Modern Authentication must be enabled.
6. Conditional
   Access Policies can take up 24 hours to take effect.

## Background:

In early 2019 MS announced retirement for Basic Auth for Legacy protocols. (MAPI,RPC, (OAB) Offline Address Book, (EWS) Exchange Web Services, POP, IMAP,(EAS)Exchange ActiveSync, and Remote PowerShell.) but not for SMTPAUTH.

Starting
 October 1st, 2022 MS will start randomly turning off Basic Auth for the above protocols. They state they will also notify the Tenancy a week ahead as well as on the day via the [ServiceHealth Dashboard](https://admin.microsoft.com/AdminPortal/home#/servicehealth). MS also stated they will allow post October 1st, 2020 a onetime re-enablement. You also can pre-opt out of having a proto disabled if you do so before October 1st, 2022, but once 2023 rolls around you will no longer be able to re-enable or opt-out again.

If you have already blocked Basic Auth Proto dependencies then you have nothing left to do. If you are not sure, check your "Monthly Usage Reports" in the [&#34;Massage Center&#34;](https://admin.microsoft.com/AdminPortal/home#/MessageCenter).

Search
 for "Basic Auth"

![]()

![]()

This
 section will let you know what you need to change.

![]()

Anything
 with a number grater then 0 will need addressed.

This
 will also clear up User reports in Azure A/D, should someone gain access to
 their password but not full access due to MFA.

![]()

Objective:

1. Enable MFA,
   Enabled Modern Auth, and all Basic Auth protocols are disabled/blocked
   ASAP.

Enable MFA:

1. See [Enabling MFA Doc](onenote:Microsoft%20365%20and%20Apps.one#Enable Multi-Factor Authentication for Non-Admins in Azure A&section-id={16DE7393-0E05-4F6B-8875-A8525168C34E}&page-id={9474D2F6-9C30-4CFD-BBDB-459FA53B9079}&end&base-path=https://d.docs.live.net/0e010ec6c3079840/Documents/Drew's%20Notebook).

Make sure Modern Auth is Enabled

1. In the [MAC (Microsoft Admin Center) go to &#34;Settings&#34;
   &gt; &#34;Org Settings&#34;](https://admin.microsoft.com/#/Settings/Services/:/Settings/L1/ModernAuthentication),

![]()

and
 search for "Modern Auth.

![]()

![]()

1. Then click on it
   and make sure all boxes are enabled. If not, enable them. As the tan box states, make sure before enabling Modern Auth on a
   proto, that it is not being used. refer to the "Basic Auth Monthly
   Report" above.

![]()

Enable Basic Auth (Not Recommended EOL 2023)

1. In the [MAC
   (Microsoft Admin Center) home page](https://admin.microsoft.com/#/homepage), click on "Help and Support" in the
   lower left.

![]()

1. Search for
   "Basic Auth Enable".

![]()

1. Then run the
   test,

![]()

1. Then pick the
   proto you wish to enable.

![]()

Disable/Block Basic Auth

1. First lets double
   check to make sure users are not still using some Basic Auth methods. [In Azure A/D](https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/Overview), go to "All Services",

![]()

Under
 the "Identity" section, click "Users"

![]()

Then
 under the "Activity" section, click "Sign-in Logs"

![]()

1. On the "Add
   Filters" button, select "Client App".

![]()

1. Then on the new
   Colum filter check Exchange ActiveSync, Exchange Online PowerShell,
   Exchange Web Service, IMAP, and POP.

![]()

![]()

1. Unfortunately you
   will need to open each Login type and view its protocol. Comb through to
   make sure no actual users are using Basic Auth.

![]()

![]()

1. Now that we have
   double checked ourselves, lets create a Conditional Access Policy to
   block Basic Auth methods directly. On the [Azure A/D homepage](https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/Overview), go to the "Security" button.

![]()

Click
 on the "Conditional Access" button

![]()

And
 then click on the "New Policy" tab.

!['O!10d ]()

1. Under the
   "Name" pulldown let's call it "Basic Auth Block".

![]()

1. On the
   "Assignment" pulldown, select "Users and Groups",
   then pick "All Users".

![]()

1. Then on the
   "Cloud Apps or Actions" pulldown, pick Cloud apps.

![]()

1. Under the
   "Conditions" pull down, select "Client Apps". We have
   two ways of setting this up. Indirectly blocking Basic Auth or Directly
   blocking Basic Auth.

 Directly blocking Basic Auth: This is
 if you know you don’t have anything using Basic Auth, that you should have
 confirmed above. A side note when checking "Other Clients", this
 blocks "Exchange Online PowerShell" and "Dynamics 365"
 using Basic Auth as well as older devices that do not support MFA.

Slide
 the "Configure" button to "Yes" and make sure
 "Exchange Active Sync Clients" and "Other Clients" are the
 only ones checked.

![]()

Indirectly
 blocking Basic Auth: This is if you know you have legacy devices that
 still require Basic Auth aka don’t support MFA and you want to not block it.
 Not shaming here but this is considered "Technical Debt" and should
 be addressed sooner than later.

Keep
 the Slider set to "No".

![]()

1. On "Access
   Control" selectet "Block Access" as we are trying to block
   Basic Auth and leave "Require all the selected Controls" as
   default.

![]()

![]()

1. Then don’t
   selected anything for "Session Control".
2. Now make sure you
   create your Conditional Access Policy as a "Report only" and
   review it with the What if Tool" before turning it on. There is also
   a new feature on excluding the person who created the "Conditional
   Access Policy" Keep in mind what option you make. Report only will not
   take effect until changed to "On".

![]()

"What If" tool to test a
 Conditional Access Policy

1. Now that our
   "Block Basic Auth Conditional Access Policy" is in read only
   mode, let see how it effects a user. First click on the "What
   If" button. (Azure A/D Home > Security > Conditional Access
   > What If)
   >

![]()

1. Then under the
   "Users and Workload Identities" section select
   "Users" and pick a user you know will be effected in your Org.

![]()

1. On the
   "Cloud Apps, Actions, or Authentication Context" section, make
   sure the pull down is set to "Cloud Apps" and select "Any
   Cloud App" and leave the other sections blank.

![]()

Depending
 on how you blocked Basic Auth you can also check one or multiple Apps
 specifically.

![]()

1. Now press the
   "What If" button,

![]()

You
 should now see our Conditional Access Policy blocking Basic Auth.

![]()

If
 your Policy is showing under "Policies that don’t Apply" go back
 over the Disable/Block Basic Auth section again.

If
 you are new to Conditional Access or What If, I encourage you to create a fake
 Org and External User to test signing in to Apps in various ways when the
 Policy is turned on post What If testing to see how things act by signing in
 as them.

![]()

Links:

1. Basic
   Auth depreciation:

[https://techcommunity.microsoft.com/t5/exchange-team-blog/basic-authentication-deprecation-in-exchange-online-september/ba-p/3609437](https://techcommunity.microsoft.com/t5/exchange-team-blog/basic-authentication-deprecation-in-exchange-online-september/ba-p/3609437)

[https://docs.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/deprecation-of-basic-authentication-exchange-online?WT.mc_id=365AdminCSH_SupportCentral](https://docs.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/deprecation-of-basic-authentication-exchange-online?WT.mc_id=365AdminCSH_SupportCentral)

1. MAC Home Page: [https://admin.microsoft.com/#/homepage](https://admin.microsoft.com/#/homepage)
2. MAC Service
   Health Dashboard: [https://admin.microsoft.com/AdminPortal/home#/servicehealth](https://admin.microsoft.com/AdminPortal/home#/servicehealth)
3. MAC Message
   Center : [https://admin.microsoft.com/AdminPortal/home#/MessageCenter](https://admin.microsoft.com/AdminPortal/home#/MessageCenter)
4. Disable/Enable
   Basic Auth in Exchange Online : [https://docs.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online](https://docs.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online)

[https://docs.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/authenticated-client-smtp-submission](https://docs.microsoft.com/en-us/exchange/clients-and-mobile-in-exchange-online/authenticated-client-smtp-submission)

[https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/block-legacy-authentication](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/block-legacy-authentication)

1. PowerShell
   Remoting V2 Module: [https://docs.microsoft.com/en-us/powershell/exchange/connect-to-exchange-online-powershell?view=exchange-ps](https://docs.microsoft.com/en-us/powershell/exchange/connect-to-exchange-online-powershell?view=exchange-ps)
2. MAC Admin Roles: [https://docs.microsoft.com/en-us/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide](https://docs.microsoft.com/en-us/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)
3. Azure A/D Portal
   : [https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/Overview](https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/Overview)
