---
layout: post
title: Serendipity and the Square
description: Why Instructors Should Never Use Piazza From the Coffee Shop.
---

{% highlight text %}
Note: This is a repost from my security review for my CS461 security class. And,
this article briefly explains why instructors or students should never user Piazza
from public wifi. I know that man in the middle attact is rare. But, it is still
possible. We can get this to work in Starbucks. So, beware for those intructors or
students using Piazza in Starbucks.
{% endhighlight %}

[Piazza][4] initially uses HTTPS to authenticate users, but afterward, HTTP is used for requests.

Furthermore, each request appears to contain an easily-identified Piazza session cookie.  As the requests are not encrypted, this cookie can be obtained by a third party observing on a WiFi network without encryption.

## How Session Spoofing Could Be Done

We noticed that there was no “HTTPS” browser indicator after we had logged onto Piazza.  We wanted to see if we could intentionally share session cookies between ourselves.  We discovered that not only would the requests be useful for a replay attack, but using another user’s cookie actually fools Piazza into misidentifying an authenticated user.
  
Siong was able to log onto Piazza as Derek using the session cookie of Derek.

We did so this way:

* Derek browsed to piazza.com, which redirects to `https://piazza.com/`
* Derek logged on normally to Piazza and arrived at `http://piazza.com/class#spring2012/cs461ece422`
* Derek clicked the favicon in the browser’s navigation bar:
  <img src="/images/piazza/1.png" />
* The “Page Info” window is displayed.  Derek navigates to the Security tab, and clicks on the “View Cookies” button.
  <img src="/images/piazza/2.png" />
* The “Cookies”  window opens, showing two cookies:
  <img src="/images/piazza/3.png" />
* The “piazza_session” cookie’s content is copied to the clipboard (“much of the cookie’s content is clipped off-screen”).
* Derek shared this cookie with Siong.
  * In a real attack, this content could be stolen by watching traffic on an unencrypted network.  See below.
* Siong logged out from his Piazza account. Then, he installed the [Edit This Cookie Chrome extension][1]  so that he could edit his browser cookies.
* With the Edit This Cookie extension, Siong pasted in the session cookie he got from Derek.
  <img src="/images/piazza/4.png" />
* Then, by going to `http://piazza.com` after the cookie modification, Siong was logged in as Derek on Piazza.
  <img src="/images/piazza/5.png" />

## Secretly Obtaining Piazza Session Cookie

However, in the case above, the session cookie value was knowingly shared between the two of us.  We suspected that one could easily obtain the session cookie value without the owner necessarily knowing.  By meeting at a coffee shop, and connecting to it’s unsecured network, we were able to observe and copy the session cookie.

We did so this way, this time with Derek logging on as Siong:

* Siong and Derek joined the same unsecured wifi network.
* Siong logged onto Piazza with his own credentials. (Keep-alive requests pass the needed session cookie without active use of Piazza.)
* Derek installed the [Cookies Manager+ Firefox Extension][2].
* Derek installed [Wireshark][3].
* Derek was logged in as himself:
  <img src="/images/piazza/6.png" />
* Derek logged out of Piazza.
* Derek launched Wireshark.
  <img src="/images/piazza/7.png" />
* Derek selected Capture Options, showing its configuration window
  <img src="/images/piazza/8.png" />
  * Derek selected en1 (the wireless interface) and made sure both “Capture packets in promiscuous mode” and “Capture packets in monitor mode” were checked.
    * “Promiscuous mode” refers to a mode of the network interface card that makes all packets-regardless of source or destination IP-available to the CPU.
* Derek clicked Start, and the trace began in the main window
* Derek entered the following into the Filter, and Applies the filter to the trace: `http.cookie contains piazza_session`
  <img src="/images/piazza/9.png" />
* Matching events were in green.  One such event was selected above, and a preview of the packet’s content was shown in the pane below.  Derek selected the “Hypertext Transfer Protocol’ node.
* Derek located the cookie field, which was labeled “[truncated] Cookie,” and copied it to the clipboard.
  * In the preview pane, the contents were truncated, however copying the value to the clipboard transferred the entire contents.
  <img src="/images/piazza/10.png" />
* In an editor window, Derek pasted and extracted the specific contents of the piazza_session to the clipboard.
  <img src="/images/piazza/11.png" />
* Returning to the browser, Derek opened the Cookies Manager+ tool.
  <img src="/images/piazza/12.png" />
* Derek located the piazza_session cookie.
  <img src="/images/piazza/13.png" />
* Derek replaced the cookie content with the value found while inspecting the network.
  <img src="/images/piazza/14.png" />
* Derek saved the result and closed the tool. Then Derek simply navigated to piazza.com, and without prompting, Derek was logged onto Piazza as Siong.
  <img src="/images/piazza/15.png" />

Thus, with no specific action by Siong - aside from having a logged-in Piazza window open, Derek was able to log onto Piazza as Siong.

## Implications

There are several opportunities for the attacker once logged onto Piazza as the victim.

* Viewing anonymous posts
  * If enabled by an Instructor, users can post anonymously to an Instructor.  The attacker can discover and unmask such posts if they were posted previously by the victim.
* Disable alerts
  * The victim may rely on alerts for certain events in Piazza, and may logically assume that if no alert is received, then no corresponding event has occurred.
  * This might cause the user to miss class announcements or critical updates to homework assignments, for example.
* Damaging reputation/credibility
  * The attacker could post irresponsible or embarrassing messages.
* Damaging academic career
  * There are University guidelines for academic integrity.  The attacker could post information that would violate such guidelines (such as early solutions to homework that is to be done individually).
  * Even if the victim were acquitted of wrongdoing, the victim would no longer be considered to have been above suspicion.
* Password change
  * Piazza lets a user change the password directly, but prompts for the existing password, which is not known to the attacker in this case.
  * However, Piazza lets a user add extra email addresses, and these may be used to send a password link with the password recovery feature.  The main email address does not appear removable.
  * The password recovery feature prompts a visitor to enter any email address to have a password reset link sent to that address.
  * We found that this process does not send the link to every address on the account.  This means the true owner of the account would not be notified of a password reset request.
  * This procedure could eventually reveal the hack, if the account owner discovers that the password has been reset.
    * However, email accounts created for this express purpose, through anonymous networks (for example, TOR), could protect help the identity of the attacker.

## What could we do as the instructor? 

According to the “Instructor FAQ”:

<blockquote>
“Piazza puts instructors in control of their class by giving them complete editing ability over class content. You can edit or delete any question or answer. If you approve of a student answer, you can endorse it to encourage students to take notice.
</blockquote>

<blockquote>
As an instructor, you can also manage other settings like course details, who is enrolled in your class, and whether students can make their posts anonymous to instructors. Edit these options under Settings in your dashboard toolbar.
</blockquote>

<blockquote>
In addition, you can monitor individual instructor and student participation under Statistics in your dashboard toolbar. Here you can get an idea of who the top contributors in your class are.”		
</blockquote>		
											
* View messages sent to instructor.
  * This violates the confidentiality for the instructor and student.
* Delete messages already posted
  * The power to delete posts (including Security Reviews), may invalidate participation grades as proof that they were submitted by students could be erased.
* Announce new exam times, etc.
  * These sorts of things could cause all kinds of confusion in the class.  Ultimately, it calls into question the integrity of all information posted by the instructors to Piazza
* Give misguided advice on homework assignments or endorse incorrect posts
  * If close to a deadline, students could take their work in a wrong direction, resulting in incorrect answers.
  * Action would likely have to be taken to correct this, but it could invalidate the assignments as a measure of students’ mastery of subjects.  Additional assignments or a revised grading policy could be needed, which causes more work for the instructors and would help undermine the grading system.
* Disable possibility anonymity of posts to instructors 
  * Students may not be aware anonymous posts are welcomed, and this may influence how students perform in the class.
  * Disabling anonymous posts to instructors would remove an incentive to report observed academic academic violations (for fear of retaliation).
  * Instructors may not realize these posts have been disabled.
  * However, we do not think this would reveal the authors of posts previously anonymous.

## Lessons Learned

* Use a secured WiFi connection when using Piazza in a public space-especially instructors.
  * VPN (Virtual Private Network). Many schools provide VPNs for their students and instructors. Students and instructors should learn how to set up such connection on their laptops and do so when connecting to unsecured WiFi network.
* When implementing services with sensitive communications, force HTTPS or some other way to ensure confidentiality of data via encryption.
  * Piazza is not the first website with such vulnerability. Facebook and Twitter were once targets of such vulnerability when the release of FireSheep, a browser extension to sniff users cookies on open and unsecured WiFi.
  * HTTPS should be the standard for all websites that have to handle sensitive information especially for a site like Piazza that facilitates communication between students and instructors.
  * Browser extensions such as Force-TLS can help to automatically use secure protocols of known sites.

[1]: https://chrome.google.com/webstore/detail/fngmhnnpilhplaeedifhccceomclgfb
[2]: https://addons.mozilla.org/en-US/firefox/addon/cookies-manager-plus/
[3]: http://www.wireshark.org/
[4]: http://piazza.com/
