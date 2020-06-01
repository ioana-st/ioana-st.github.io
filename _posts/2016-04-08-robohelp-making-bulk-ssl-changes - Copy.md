---
layout: single
title: "RoboHelp: Making Bulk SSL Changes"
date: 2016-04-08 12:54:28 +0300
classes: wide
categories: robohelp
---
RoboHelp's interface is pretty intuitive, and once you understand what each option does it's not difficult to generate an online help system. But what do you do if you have to make a lot of changes - like, for example, modifying a setting in WebHelp for 40 modules? The "simple" option is to open each module, double-click the SSL, navigate to the corresponding wizard page, make the change, save, open the next module... and so on, 40 times. The smarter option is realizing that (like most of RoboHelp's files) the SSL is a relatively-simple-to-manipulate XML.

This method can be used for any of the settings visible in the GUI (and even for some that are not visible). In the following example I will assume that I have several modules, with at least one SSL defined for each, and that I'm trying to deactivate Mark of the Web for all of them.

In short, we will compare the XML with Mark of the Web turned on and the XML with Mark of the Web turned off and we will then use search &amp; replace to change the option in all modules.

1. From the source folder of one of the RoboHelp projects, open the file _&lt;SSL_name&gt;.ssl_ in a text editor (I use <a href="https://notepad-plus-plus.org/">Notepad++</a>). You will get something like this:<br>
![WebHelp SSL]({{ "/assets/images/ssl_xml_before.png" | relative_url }})
2. Open an empty document or tab and copy the entire contents of the file in it. You need to do this because the next steps will change the initial XML and you need both versions for the comparison.
3. In RoboHelp, open the respective project and double-click the same SSL you used at step 1.<br>
![WebHelp SSL with Mark of the Web enabled]({{ "/assets/images/rh_motw_on.png" | relative_url }})
4. Change the options as needed - in my case, I cleared **Mark of the Web**. (You can change multiple options at the same time, but you risk getting confused, so it's safest to take them one by one.)
5. Save the SSL. The XML at step 1 is immediately modified and you now have two versions of the same file - a _before_ and an _after_.
6. Identify the modified row. In Notepad++, I use a plugin called <a href="https://sourceforge.net/projects/npp-compare/">Compare</a>. If I select **Plugins &gt; Compare &gt; Compare**, Notepad++ compares the last two files opened and highlights the changes.<br>
Be careful though, because (for reasons that escape me) some rows are rearranged the moment the SSL is saved, so you will see a lot of apparent changes that are actually irrelevant to what we are trying to do. Either way, the files are not big and it's fairly easy to find the row with the modified value by simply scrolling and eyeballing it.<br>
![Notepad++ Compare plugin diff]({{ "/assets/images/ssl_xml_diff.png" | relative_url }})<br>
As you can see, _&lt;element name="m_bSupportMOTW" value="1" /&gt;_ in the initial file means that Mark of the Web is on, while _&lt;element name="m_bSupportMOTW" value="o" /&gt;_ means that it's off.
7. Replace the old row with the new one in all relevant SSLs. In Notepad++, that can look like this:<br>
![Notepad++ Search and Replace]({{ "/assets/images/ssl_search_replace.png" | relative_url }})<br>
...which translates as :
* Replace _&lt;element name="m_bSupportMOTW" value="1" /&gt;_ with _&lt;element name="m_bSupportMOTW" value="o" /&gt; _...
* ...in all files called _WebHelp.ssl_...
* ...located in _D:\technical_documentation_ or one of its subfolders.

Tada! It all took 5 minutes in total, and you can continue by using a script to regenerate the entire help with the new option.