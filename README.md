# Email dispatch checklist

After a number of iterations and sweating decided to write a checklist for
an email dispatch from within an application. Decided to be language/framework
agnostic and write in language specific notes in separate files.

## Steps

1. Introduce email dispatch trigger.
2. Create "resend email" functionality within the (usually web) GUI.
3. Background process that creates and dispatches the email. Even if a 3rd party service is used, the extra call should be initiated from a background process,
as it is a potential point of failure.
4. If necessary a flag on the database that would shield from multiple email dispatches.
5. Error logging and retry mechanisms, specially if the environment does not
provide a default.
6. Decide on email subject. Try to add some entropy to make subjects unique.
7. Write and test a dummy text and incorporate: recipient(s), CCs and BCCs for
monitoring etc.
7. Get email's copy and create the text version.
8. Debug it with a person other than the developer that created it.
9. Decide if an HTML version should be dispatched/is necessary. If so write one
with HTML elements - without styling. That should be enough for a big number
of cases.
10. If necessary introduce styling/skinning. This is more difficult as it sounds.
11. Decide on list of supported email clients.
12. Test on clients identified on previous step.
13. Consider including tracking which will feed with email client signatures.
These can be used to dictate the inclusion of items in the list of step (11).

Steps 1 to 5 are preparatory/"boilerplate". It might make sense to bundle a
number of steps and introduce each bunch in a separate delivery. There might
be the necessity to have emails being dispatched and skin them afterwards. Some
steps can occur in parallel: while someone is reviewing the copy, someone
else can start writing the HTML.

## Notes

Point *(2)* might seem strange but:

* Some emails might not be dispatched.
* People might accidentally delete them and call back complaining that they
have not been dispatched in the first place;
* Or an evil Spam filter.
* Is ultra useful in debugging/development sessions.

Point *(4)* has to do with emails triggered by a GUI such as submitting a button.
If that's the case there should be a mechanism that ensures that it is
dispatched at most once. This will shield from potential multiple submissions,
such as the user waiting too long and pushing the button again and again. The
above might have bad side effects such as dispatching multiple emails at the
same time to the same address with the same subject and content, something that
might annoy a Spam filter.

One way to tackle this is with a database flag "email_dispatched_for_abc" that defaults to "null" or "false". Successful dispatch should flip it... Also
dispatches should be in a queue for the same reason, checking that field before
dispatching.

Subjects *(6)*: "*Order confirmation (Reference: XZ234239).*" is preferable to
"*MegaCorp Order confirmation.*", as it:

* Can create different rendering of discussions per order on user's email client
if the customer responds/forwards.
* Might help filters distinguish from a sender going amok/Spam.

Styling *(10)*: Responsive emails are difficult because email clients remove stuff
(in order to protect from viruses) and try to optimize stuff. It has been better
documented by others, one good read is:
[http://designinstruct.com/tool/responsive-html-email-framework-zurb-ink/](http://designinstruct.com/tool/responsive-html-email-framework-zurb-ink/ "ZURB Ink: Responsive HTML Email Framework")

Generally it seems that the common email HTML denominator is HTML-tables
which makes things difficult.

Testing (12): There services that do it, have heard of but not used
[https://litmus.com/](https://litmus.com/ "Test and track your emails")
