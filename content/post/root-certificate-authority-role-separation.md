---
title: "Root Certificate Authority Role Separation"
date: 2016-11-05
---

The thing I hate the most in the world is struggling with Windows limitations.
The second is being forced to do so.

One month ago I received a company-owned laptop and it was configured via Active Directory shits, but for me it makes no sense to have a device without being the full owner (at least in an administration way, not as Stallman would like for any code that runs on his machine).

Then I said, "I will try to find some oversight or strange configuration", and because I'm paranoid about being controlled, I immediately had a look at the root certificates installed on the machine.

Ta-dah! A certificate issued by the company! Of course I checked if they were performing man-in-the-middle on HTTPS... But without any positive results. Maybe it was used to trust internal domains? Neither.

As always, after a good nap, I was reminded that within the building, the Wi-Fi is protected by a WPA-Enterprise authentication grade. Bingo! The CA was used for that. So, what is the problem?

At first glance I was impressed by the integration of the OS in the management of certificates, but then I thought about the classic UNIX philosophy. MAKE A F***ING PROGRAM DO ONE THING WELL.

![NetworkManager](/root-certificate-authority-role-separation/1.png)
<sub>_NetworkManager on GNU/Linux handles certificates internally for each AP._</sub>

Why I say that? After much research on the internet, I discovered that the role necessary to select the certificate to use it to trust an access point, is "Server authentication". Which of course it is the same flag needed to trust HTTPS websites. That is cool Microsoft! I'm serious. (No, I am not).

I still believe in the trustworthiness of the software companies so I tried to write a nice mail to report this thing.

![Mail1](/root-certificate-authority-role-separation/2.png)

I definitely did not expect any CVE for a bug of this kind. Although it could cause security problems, it seems to have been completely ignored, as you can read in the Katie's reply.

![Mail2](/root-certificate-authority-role-separation/3.png)

Ok, maybe explaining the potential better I could get at least a "we will try to verify" ?

Never give up! At least is what people say...

![Mail3](/root-certificate-authority-role-separation/4.png)

**IGNORED, GAME OVER :(**

![Mail4](/root-certificate-authority-role-separation/5.png)

I understand that perhaps getting many emails as "security holes" you have to filter out radically someone, and maybe the 90% of the emails are just crap. In any case, where should a flaw like this be reported? Maybe it's me that I'm wrong, anyway they have not been able to give me the right directions as a customer. Or maybe they do not care of these things.

---

I expect that kind of behavior also from other operating systems, though is more a bad design from the beginning than anything else. (I left out the fact that in Windows you can only set one IP configuration per interface instead of per AP).

For example, on iOS, the CA of a EAP network is installed when you make your first connection to it.

Effective? Yes.
Simple? Yes.
Secure? Maybe.
Flexible? Nope.

I want to run my security in freedom and flexibility, so I will take the operating system head of this category. Android.

![Android1](/root-certificate-authority-role-separation/6.png)
<sub>_Android? Not a winner anyhow._</sub>

On Android, the CA can be installed for the EAP side separately as we expected. But we must not excite so quickly, the problem is outside that.

![Android2](/root-certificate-authority-role-separation/7.png)
<sub>_VPN and apps should be separated._</sub>

Trying to install a certificate you will immediately notice two categories combined. Why? Also in this case we will not have an answer, and as you can guess, the problem will be the same as the first case. With only two different categories.

You want to prevent someone spoofs your VPN certificate and at the same time you want to prevent someone to do a MITM having installed only trusted CA? You can't.

---

Encryption is needed, but it must also be reachable by everyone.

Perhaps after this little story someone will understand the importance of separating the keys verification and things will begin to change. I really hope in some fixes, being just small things.

---

**EDIT 1/12/2016:**

Google replied:

> Thanks for your report.  
> We'll take this into consideration for future Android release.  
> Android Security Team

**EDIT 14/12/2016:**

As pointed out by a friend, Microsoft did not cosidered this a bug because the correct use of X.509 is to issue a certificate with a specific domain filling the "subjectAltName" extension to limit the usage.

But it still remain an issue because the certificate providers doesn't always use it and you cannot add an extension to the certificate without re-issuing it.
