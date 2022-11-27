# Schemarin and System Integrity Protection

If you're here, it's probably because Schemarin gave you an error message that looked something like:

```
[path] is protected by macOS. To allow Schemarin access to it, you must disable System Integrity Protection (very bad idea) and rerun Schemarin with sudo.
```

This happens when you ask Schemarin to scan
for files[^1] in one or more of the following directories[^2]:

- `/System`
- `/usr`
- `/bin`
- `/sbin`
- `/var`
- The package contents of apps that came pre-installed with macOS

These directories are protected by a macOS security feature called
[System Integrity Protection](https://support.apple.com/en-us/HT204899), which is a very good thing that you should
generally not turn off under any circumstance and definitely not just so Schemarin can scan for iTerm2 color schemes
in directories where they shouldn't even be stored.

**It is strongly recommended that you simply refrain from asking Schemarin to scan protected directories and leave
System Integrity Protection on.**

If you absolutely must have Schemarin access files in protected directories, here's what you have to do:[^3]

1. Follow [Apple's instructions](https://developer.apple.com/documentation/security/disabling_and_enabling_system_integrity_protection)
to disable System Integrity Protection.

2. Run Schemarin with `sudo`.[^4]

This will allow Schemarin to read from (and write to!) *any* file on your Mac. Schemarin is open-source so you can
have confidence that it's not going to do anything undesirable, but a less-virtuous program would have free reign
to wreck your system.

When you're done, you should immediately re-enable System Integrity Protection using the instructions at
[the same link](https://developer.apple.com/documentation/security/disabling_and_enabling_system_integrity_protection).

[^1]: When you you provide Schemarin with a path from which to import iTerm2 color schemes, and that path is a
directory, Schemarin scans that directory recursively. This means it scans the immediate children of that directory,
as well as their children, as well as *their* children, all the way until Schemarin reaches the bottom of the tree that
starts from the directory you provided. Therfore, if you provide some absurdly high-level directory like `/`, Schemarin
will inevitably try to scan a directory guarded by System Integrity Protection.

[^2]: Technically speaking, it happens when Python raises a `PermissionError` during the scan, which Schemarin
catches and resolves to the friendly error message that likely brought you here. Schemarin does not inherently know
which directories are and are not protected, and, obviously, makes no attempt to avoid scanning protected directories.
The list of protected directories in this document is sourced from [Apple](https://support.apple.com/en-us/HT204899).


[^3]: You need to be an administrator on your Mac to do any of this, but I don't expect that to be an issue
for Schemarin's target audience.

[^4]: Contrary to what your intuition might tell you, using `sudo` alone— without first disabling System Integrity
Protection — is not sufficient to gain access to protected directories.

    Neither is directly logging in as the root user after enabling it via Directory Utility. You weren't thinking
    of trying that, were you?