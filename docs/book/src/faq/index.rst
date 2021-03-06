===
FAQ
===

Frequently Asked Questions:

    * :ref:`general_volatility`
    * :ref:`troubles_upgrade`
    * :ref:`troubles_problem`


General Questions
=================

.. _general_volatility:

Can I use Volatility with Cuckoo?
---------------------------------

Cuckoo does not provide support for Volatility by default. If you want to perform
additional forensics on the analysis machine, at the moment you'll have to implement
such support by yourself.
In the future we might support a full memory dump of the virtual machines, but it's
not in our short term plans at the moment.

Please also consider that we don't particularly encourage this: since Cuckoo employs
some rootkit-like technologies to perform its operaitons, the results of a forensic
analysis would be polluted by the sandbox's components.

Despite being highly customizable, please also consider that Cuckoo has been designed
for full automation. If you're planning to perform manual analysis of your
malwares, probably Cuckoo is not the best choice.

Troubleshooting
===============

.. _troubles_upgrade:

After upgrade Cuckoo stops to work
----------------------------------

Probably you upgraded it in a wrong way.
It's not a good practice to rewrite the files due to Cuckoo's complexity and
quick evolution.

Please follow the upgrade steps described in :doc:`../installation/upgrade`.

.. _troubles_problem:

Cuckoo stumbles in some error I don't understand
------------------------------------------------

Cuckoo is a young and still evolving project, it might definitely happen that
you will occur in some problems while running it, but before you rush into
sending emails to everyone make sure to read what follows.

Cuckoo is not meant to be a point-and-click tool: it's designed to be a highly
customizable and configurable solution for somewhat experienced users and
malware analysts.

It requires you to have a decent understanding of your operating systems, Python,
the concepts behind virtualization and sandboxing.
We try to make it as easy to use as possible, but you have to keep in mind that
it's not a technology meant to be accessible to just anyone.

That being said, if a problem occurs you have to make sure that you did everything
you could before asking for time and efforts from our developers and users.
We just can't help everyone, we have limited time and it has to be dedicated to
the development and fixing actual bugs.

    * We have an extensive documentation, read it carefully. You can't just skip parts
      of it.
    * We have a mailing list archive, search through it for previous threads where
      your same problem could have been already addressed and solved.
    * We have a blog, read it.
    * We have lot of users producing content on Internet, `Google`_ it.
    * Spend some of your own time trying fixing the issues before asking ours, you
      might even get to learn and understand Cuckoo better.

Long story short: use the existing resources, put some efforts into it and don't
abuse people.

If you still can't figure out your problem, you can ask help on our online communities
(see :doc:`../finalremarks/index`).
Make sure when you ask for help to:

    * Use a clear and explicit title for your emails: "I have a problem", "Help me" or
      "Cuckoo error" are **NOT** good titles.
    * Explain **in details** what you're experiencing. Try to reproduce several
      times your issue and write down all steps to achieve that.
    * Use no-paste services and link your logs, configuration files and details on your
      setup.
    * Eventually provide a copy of the analysis that generated the problem.

.. _`Google`: http://www.google.com

Check and restore current snapshot with KVM
-------------------------------------------

If something goes wrong with virtual machine it's best practice to check curent snapshot
status.
You can do that with the following::

    $ virsh snapshot-current "<Name of VM>"

If you got a long XML as output your current snapshot is configured and you can skip
the rest of this chapter; anyway if you got an error like the following your current
snapshot is broken::

    $ virsh snapshot-current "<Name of VM>"
    error: domain '<Name of VM>' has no current snapshot

To fix and create a current snapshot firt list all machine's snapshots::

    $ virsh snapshot-list "<Name of VM>"
     Name                 Creation Time             State
     ------------------------------------------------------------
     1339506531           2012-06-12 15:08:51 +0200 running

Choose one snapshot name and set it as current::

    $ snapshot-current "<Name of VM>" --snapshotname 1339506531
    Snapshot 1339506531 set as current

Now the virtual machine state is fixed.

Check and restore current snapshot with VirtualBox
--------------------------------------------------

If something goes wrong with virtual it's best practice to check the virtual machine
status and the curent snapshot.
First of all check the virtual machine status with the following::

    $ VBoxManage showvminfo "<Name of VM>" | grep State
    State:           powered off (since 2012-06-27T22:03:57.000000000)

If the state is "powered off" you can go ahead with the next check, if the state is
"aborted" or something else you have to restore it to "powered off" before::

    $ VBoxManage controlvm "<Name of VM>" poweroff

With the following check the current snapshots state::

    $ VBoxManage snapshot "<Name of VM>" list --details
       Name: s1 (UUID: 90828a77-72f4-4a5e-b9d3-bb1fdd4cef5f)
          Name: s2 (UUID: 97838e37-9ca4-4194-a041-5e9a40d6c205) *

If you have a snapshot marked with a star "*" your snapshot is ready, anyway
you have to restore the current snapshot::

    $ VBoxManage snapshot "<Name of VM>" restorecurrent

Cuckoo eats all my RAM
----------------------

It's a rare case, but some sample can generate gigs of logs. These logs must
be processed by Cuckoo and all reports generated, and eat some RAM.
To avoid memory leaks, file bigger than analysis_size_limit in cuckoo.conf aren't
processed.
If you need to process them, you have to do it manually via submit.py utility.

I got a lot of errors from virtualization
-----------------------------------------

Proper sizing of VM number running on a host is fundamental, if you oversize
the VM number you get too much I/O. All virtualization software we have tested,
under high I/O, raise error when you try to run CLI utilities.
If you get some errors, try to decrease the number of running VMs.