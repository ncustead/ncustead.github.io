
## Scenario

Many large organizations will acquire new companies over time and bring them into the fold. One way this is done for ease of use is to establish a trust relationship with the new domain. In doing so, you can avoid migrating all the established objects, making integration much quicker. This trust can also introduce weaknesses into the customer's environment if they are not careful. A subdomain with an exploitable flaw or vulnerability can provide us with a quick route into the target domain. Companies may also establish trusts with other companies (such as an MSP), a customer, or other business units of the same company (such as a division of the company in another geographical region). Let's explore domain trusts more and how we can abuse built-in functionality during our assessments.

---

## Domain Trusts Overview

A [trust](https://social.technet.microsoft.com/wiki/contents/articles/50969.active-directory-forest-trust-attention-points.aspx) is used to establish forest-forest or domain-domain (intra-domain) authentication, which allows users to access resources in (or perform administrative tasks) another domain, outside of the main domain where their account resides. A trust creates a link between the authentication systems of two domains and may allow either one-way or two-way (bidirectional) communication. An organization can create various types of trusts:

- `Parent-child`: Two or more domains within the same forest. The child domain has a two-way transitive trust with the parent domain, meaning that users in the child domain `corp.inlanefreight.local` could authenticate into the parent domain `inlanefreight.local`, and vice-versa.
- `Cross-link`: A trust between child domains to speed up authentication.
- `External`: A non-transitive trust between two separate domains in separate forests which are not already joined by a forest trust. This type of trust utilizes [SID filtering](https://www.serverbrain.org/active-directory-2008/sid-history-and-sid-filtering.html) or filters out authentication requests (by SID) not from the trusted domain.
- `Tree-root`: A two-way transitive trust between a forest root domain and a new tree root domain. They are created by design when you set up a new tree root domain within a forest.
- `Forest`: A transitive trust between two forest root domains.
- [ESAE](https://docs.microsoft.com/en-us/security/compass/esae-retirement): A bastion forest used to manage Active Directory.

When establishing a trust, certain elements can be modified depending on the business case.

Trusts can be transitive or non-transitive.

- A `transitive` trust means that trust is extended to objects that the child domain trusts. For example, let's say we have three domains. In a transitive relationship, if `Domain A` has a trust with `Domain B`, and `Domain B` has a `transitive` trust with `Domain C`, then `Domain A` will automatically trust `Domain C`.
- In a `non-transitive trust`, the child domain itself is the only one trusted.

![image](https://academy.hackthebox.com/storage/modules/143/transitive-trusts.png)

Adapted from [here](https://zindagitech.com/wp-content/uploads/2021/09/Picture2-Deepak-4.png.webp)

#### Trust Table Side By Side

|Transitive|Non-Transitive|
|---|---|
|Shared, 1 to many|Direct trust|
|The trust is shared with anyone in the forest|Not extended to next level child domains|
|Forest, tree-root, parent-child, and cross-link trusts are transitive|Typical for external or custom trust setups|

An easy comparison to make can be package delivery to your house. For a `transitive` trust, you have extended the permission to anyone in your household (forest) to accept a package on your behalf. For a `non-transitive` trust, you have given strict orders with the package that no one other than the delivery service and you can handle the package, and only you can sign for it.

Trusts can be set up in two directions: one-way or two-way (bidirectional).

- `One-way trust`: Users in a `trusted` domain can access resources in a trusting domain, not vice-versa.
- `Bidirectional trust`: Users from both trusting domains can access resources in the other domain. For example, in a bidirectional trust between `INLANEFREIGHT.LOCAL` and `FREIGHTLOGISTICS.LOCAL`, users in `INLANEFREIGHT.LOCAL` would be able to access resources in `FREIGHTLOGISTICS.LOCAL`, and vice-versa.

Domain trusts are often set up incorrectly and can provide us with critical unintended attack paths. Also, trusts set up for ease of use may not be reviewed later for potential security implications if security is not considered before establishing the trust relationship. A Merger & Acquisition (M&A) between two companies can result in bidirectional trusts with acquired companies, which can unknowingly introduce risk into the acquiring company’s environment if the security posture of the acquired company is unknown and untested. If someone wanted to target your organization, they could also look at the other company you acquired for a potentially softer target to attack, allowing them to get into your organization indirectly. It is not uncommon to be able to perform an attack such as Kerberoasting against a domain outside the principal domain and obtain a user that has administrative access within the principal domain. I have performed many penetration tests where this was the case: I was unable to find a foothold in the principal domain, but was able to find a flaw in a trusted domain which, in turn, gave me a foothold, or even full admin rights in the principal domain. This type of "end-around" attack could be prevented if security is considered as paramount before establishing any kind of domain trust. As we examine trust relationships, keep these thoughts in mind for reporting. Often, we will find that the larger organization is unaware that a trust relationship exists with one or more domains.

Below is a graphical representation of the various trust types.

![image](https://academy.hackthebox.com/storage/modules/143/trusts-diagram.png)

---

## Enumerating Trust Relationships

We can use the [Get-ADTrust](https://docs.microsoft.com/en-us/powershell/module/activedirectory/get-adtrust?view=windowsserver2022-ps) cmdlet to enumerate domain trust relationships. This is especially helpful if we are limited to just using built-in tools.

#### Using Get-ADTrust

  Using Get-ADTrust

```powershell-session
PS C:\htb> Import-Module activedirectory
PS C:\htb> Get-ADTrust -Filter *

Direction               : BiDirectional
DisallowTransivity      : False
DistinguishedName       : CN=LOGISTICS.INLANEFREIGHT.LOCAL,CN=System,DC=INLANEFREIGHT,DC=LOCAL
ForestTransitive        : False
IntraForest             : True
IsTreeParent            : False
IsTreeRoot              : False
Name                    : LOGISTICS.INLANEFREIGHT.LOCAL
ObjectClass             : trustedDomain
ObjectGUID              : f48a1169-2e58-42c1-ba32-a6ccb10057ec
SelectiveAuthentication : False
SIDFilteringForestAware : False
SIDFilteringQuarantined : False
Source                  : DC=INLANEFREIGHT,DC=LOCAL
Target                  : LOGISTICS.INLANEFREIGHT.LOCAL
TGTDelegation           : False
TrustAttributes         : 32
TrustedPolicy           :
TrustingPolicy          :
TrustType               : Uplevel
UplevelOnly             : False
UsesAESKeys             : False
UsesRC4Encryption       : False

Direction               : BiDirectional
DisallowTransivity      : False
DistinguishedName       : CN=FREIGHTLOGISTICS.LOCAL,CN=System,DC=INLANEFREIGHT,DC=LOCAL
ForestTransitive        : True
IntraForest             : False
IsTreeParent            : False
IsTreeRoot              : False
Name                    : FREIGHTLOGISTICS.LOCAL
ObjectClass             : trustedDomain
ObjectGUID              : 1597717f-89b7-49b8-9cd9-0801d52475ca
SelectiveAuthentication : False
SIDFilteringForestAware : False
SIDFilteringQuarantined : False
Source                  : DC=INLANEFREIGHT,DC=LOCAL
Target                  : FREIGHTLOGISTICS.LOCAL
TGTDelegation           : False
TrustAttributes         : 8
TrustedPolicy           :
TrustingPolicy          :
TrustType               : Uplevel
UplevelOnly             : False
UsesAESKeys             : False
UsesRC4Encryption       : False
```

The above output shows that our current domain `INLANEFREIGHT.LOCAL` has two domain trusts. The first is with `LOGISTICS.INLANEFREIGHT.LOCAL`, and the `IntraForest` property shows that this is a child domain, and we are currently positioned in the root domain of the forest. The second trust is with the domain `FREIGHTLOGISTICS.LOCAL,` and the `ForestTransitive` property is set to `True`, which means that this is a forest trust or external trust. We can see that both trusts are set up to be bidirectional, meaning that users can authenticate back and forth across both trusts. This is important to note down during an assessment. If we cannot authenticate across a trust, we cannot perform any enumeration or attacks across the trust.

Aside from using built-in AD tools such as the Active Directory PowerShell module, both PowerView and BloodHound can be utilized to enumerate trust relationships, the type of trusts established, and the authentication flow. After importing PowerView, we can use the [Get-DomainTrust](https://powersploit.readthedocs.io/en/latest/Recon/Get-DomainTrust/) function to enumerate what trusts exist, if any.

#### Checking for Existing Trusts using Get-DomainTrust

  Checking for Existing Trusts using Get-DomainTrust

```powershell-session
PS C:\htb> Get-DomainTrust 

SourceName      : INLANEFREIGHT.LOCAL
TargetName      : LOGISTICS.INLANEFREIGHT.LOCAL
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : WITHIN_FOREST
TrustDirection  : Bidirectional
WhenCreated     : 11/1/2021 6:20:22 PM
WhenChanged     : 2/26/2022 11:55:55 PM

SourceName      : INLANEFREIGHT.LOCAL
TargetName      : FREIGHTLOGISTICS.LOCAL
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : FOREST_TRANSITIVE
TrustDirection  : Bidirectional
WhenCreated     : 11/1/2021 8:07:09 PM
WhenChanged     : 2/27/2022 12:02:39 AM
```

PowerView can be used to perform a domain trust mapping and provide information such as the type of trust (parent/child, external, forest) and the direction of the trust (one-way or bidirectional). This information is beneficial once a foothold is obtained, and we plan to compromise the environment further.

#### Using Get-DomainTrustMapping

  Using Get-DomainTrustMapping

```powershell-session
PS C:\htb> Get-DomainTrustMapping

SourceName      : INLANEFREIGHT.LOCAL
TargetName      : LOGISTICS.INLANEFREIGHT.LOCAL
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : WITHIN_FOREST
TrustDirection  : Bidirectional
WhenCreated     : 11/1/2021 6:20:22 PM
WhenChanged     : 2/26/2022 11:55:55 PM

SourceName      : INLANEFREIGHT.LOCAL
TargetName      : FREIGHTLOGISTICS.LOCAL
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : FOREST_TRANSITIVE
TrustDirection  : Bidirectional
WhenCreated     : 11/1/2021 8:07:09 PM
WhenChanged     : 2/27/2022 12:02:39 AM

SourceName      : FREIGHTLOGISTICS.LOCAL
TargetName      : INLANEFREIGHT.LOCAL
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : FOREST_TRANSITIVE
TrustDirection  : Bidirectional
WhenCreated     : 11/1/2021 8:07:08 PM
WhenChanged     : 2/27/2022 12:02:41 AM

SourceName      : LOGISTICS.INLANEFREIGHT.LOCAL
TargetName      : INLANEFREIGHT.LOCAL
TrustType       : WINDOWS_ACTIVE_DIRECTORY
TrustAttributes : WITHIN_FOREST
TrustDirection  : Bidirectional
WhenCreated     : 11/1/2021 6:20:22 PM
WhenChanged     : 2/26/2022 11:55:55 PM
```

From here, we could begin performing enumeration across the trusts. For example, we could look at all users in the child domain:

#### Checking Users in the Child Domain using Get-DomainUser

  Checking Users in the Child Domain using Get-DomainUser

```powershell-session
PS C:\htb> Get-DomainUser -Domain LOGISTICS.INLANEFREIGHT.LOCAL | select SamAccountName

samaccountname
--------------
htb-student_adm
Administrator
Guest
lab_adm
krbtgt
```

Another tool we can use to get Domain Trust is `netdom`. The `netdom query` sub-command of the `netdom` command-line tool in Windows can retrieve information about the domain, including a list of workstations, servers, and domain trusts.

#### Using netdom to query domain trust

  Using netdom to query domain trust

```cmd-session
C:\htb> netdom query /domain:inlanefreight.local trust
Direction Trusted\Trusting domain                         Trust type
========= =======================                         ==========

<->       LOGISTICS.INLANEFREIGHT.LOCAL
Direct
 Not found

<->       FREIGHTLOGISTICS.LOCAL
Direct
 Not found

The command completed successfully.
```

#### Using netdom to query domain controllers

  Using netdom to query domain controllers

```cmd-session
C:\htb> netdom query /domain:inlanefreight.local dc
List of domain controllers with accounts in the domain:

ACADEMY-EA-DC01
The command completed successfully.
```

#### Using netdom to query workstations and servers

  Using netdom to query workstations and servers

```cmd-session
C:\htb> netdom query /domain:inlanefreight.local workstation
List of workstations with accounts in the domain:

ACADEMY-EA-MS01
ACADEMY-EA-MX01      ( Workstation or Server )

SQL01      ( Workstation or Server )
ILF-XRG      ( Workstation or Server )
MAINLON      ( Workstation or Server )
CISERVER      ( Workstation or Server )
INDEX-DEV-LON      ( Workstation or Server )
...SNIP...
```

We can also use BloodHound to visualize these trust relationships by using the `Map Domain Trusts` pre-built query. Here we can easily see that two bidirectional trusts exist.

#### Visualizing Trust Relationships in BloodHound

![image](https://academy.hackthebox.com/storage/modules/143/BH_trusts.png)

---

## Onwards

In the following few sections, we will cover common attacks that we can perform against child --> parent domain trusts and across bidirectional forest trusts. These types of attacks should not be overlooked, but we should always check with our client to ensure that any trusts we uncover during our enumeration are in scope for the assessment and we are not going outside the Rules of Engagement.

  

VPN Servers

Warning: Each time you "Switch", your connection keys are regenerated and you must re-download your VPN connection file.

All VM instances associated with the old VPN Server will be terminated when switching to a new VPN server.  
Existing PwnBox instances will automatically switch to the new VPN server.

 Select Vpn Server  eu-academy-1 eu-academy-2 us-academy-1 us-academy-2 us-academy-3 

PROTOCOL

UDP 1337

TCP 443

DOWNLOAD VPN CONNECTION FILE

  Full Screen  Terminate   Reset 

Life Left: 45m

Connected to htb-qyogwlzaeu:1 (htb-ac-548426)

#### Questions

Answer the question(s) below to complete this Section and earn cubes!

Target: Click here to spawn the target system!  

Cheat Sheet

[

Download VPN Connection File

](https://academy.hackthebox.com/vpn/key)

 RDP to with user "htb-student" and password "Academy_student_AD!"

+ 0  What is the child domain of INLANEFREIGHT.LOCAL? (format: FQDN, i.e., DEV.ACME.LOCAL)

 Submit

+ 0  What domain does the INLANEFREIGHT.LOCAL domain have a forest transitive trust with?

 Submit

+ 0  What direction is this trust?

 Submit

 [Previous](https://academy.hackthebox.com/module/143/section/1276)

[Next](https://academy.hackthebox.com/module/143/section/1457) 

 Cheat Sheet Resources [Go to Questions](https://academy.hackthebox.com/module/143/section/1488#questionsDiv)

##### Table of Contents

###### Setting The Stage

[Introduction to Active Directory Enumeration & Attacks](https://academy.hackthebox.com/module/143/section/1262)[Tools Of The Trade](https://academy.hackthebox.com/module/143/section/1517)[Scenario](https://academy.hackthebox.com/module/143/section/1263)

###### Initial Enumeration

  [External Recon and Enumeration Principles](https://academy.hackthebox.com/module/143/section/1264)  [Initial Enumeration of the Domain](https://academy.hackthebox.com/module/143/section/1265)

###### Sniffing out a Foothold

  [LLMNR/NBT-NS Poisoning - from Linux](https://academy.hackthebox.com/module/143/section/1272)  [LLMNR/NBT-NS Poisoning - from Windows](https://academy.hackthebox.com/module/143/section/1420)

###### Sighting In, Hunting For A User

[Password Spraying Overview](https://academy.hackthebox.com/module/143/section/1424)  [Enumerating & Retrieving Password Policies](https://academy.hackthebox.com/module/143/section/1490)  [Password Spraying - Making a Target User List](https://academy.hackthebox.com/module/143/section/1455)

###### Spray Responsibly

  [Internal Password Spraying - from Linux](https://academy.hackthebox.com/module/143/section/1271)  [Internal Password Spraying - from Windows](https://academy.hackthebox.com/module/143/section/1422)

###### Deeper Down the Rabbit Hole

[Enumerating Security Controls](https://academy.hackthebox.com/module/143/section/1459)  [Credentialed Enumeration - from Linux](https://academy.hackthebox.com/module/143/section/1269)  [Credentialed Enumeration - from Windows](https://academy.hackthebox.com/module/143/section/1421)  [Living Off the Land](https://academy.hackthebox.com/module/143/section/1360)

###### Cooking with Fire

  [Kerberoasting - from Linux](https://academy.hackthebox.com/module/143/section/1274)  [Kerberoasting - from Windows](https://academy.hackthebox.com/module/143/section/1423)

###### An ACE in the Hole

  [Access Control List (ACL) Abuse Primer](https://academy.hackthebox.com/module/143/section/1456)  [ACL Enumeration](https://academy.hackthebox.com/module/143/section/1485)  [ACL Abuse Tactics](https://academy.hackthebox.com/module/143/section/1486)  [DCSync](https://academy.hackthebox.com/module/143/section/1489)

###### Stacking The Deck

  [Privileged Access](https://academy.hackthebox.com/module/143/section/1275)[Kerberos "Double Hop" Problem](https://academy.hackthebox.com/module/143/section/1573)  [Bleeding Edge Vulnerabilities](https://academy.hackthebox.com/module/143/section/1484)  [Miscellaneous Misconfigurations](https://academy.hackthebox.com/module/143/section/1276)

###### Why So Trusting?

  [Domain Trusts Primer](https://academy.hackthebox.com/module/143/section/1488)  [Attacking Domain Trusts - Child -> Parent Trusts - from Windows](https://academy.hackthebox.com/module/143/section/1457)  [Attacking Domain Trusts - Child -> Parent Trusts - from Linux](https://academy.hackthebox.com/module/143/section/1508)

###### Breaking Down Boundaries

  [Attacking Domain Trusts - Cross-Forest Trust Abuse - from Windows](https://academy.hackthebox.com/module/143/section/1487)  [Attacking Domain Trusts - Cross-Forest Trust Abuse - from Linux](https://academy.hackthebox.com/module/143/section/1509)

###### Defensive Considerations

[Hardening Active Directory](https://academy.hackthebox.com/module/143/section/1277)  [Additional AD Auditing Techniques](https://academy.hackthebox.com/module/143/section/1460)

###### Skill Assessment - Final Showdown

  [AD Enumeration & Attacks - Skills Assessment Part I](https://academy.hackthebox.com/module/143/section/1278)  [AD Enumeration & Attacks - Skills Assessment Part II](https://academy.hackthebox.com/module/143/section/1279)  [Beyond this Module](https://academy.hackthebox.com/module/143/section/1267)

##### My Workstation

  Interact   Terminate   Reset 

Life Left: 45m

Powered by   [![](https://academy.hackthebox.com/images/logo-htb.svg)](https://www.hackthebox.com/)

![](https://downloads.intercomcdn.com/i/o/369812/618f59eb6f3b1f6de8e11a97/efef1192e4fa386f159825fbf792ed52.png)