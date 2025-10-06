# SSH key change problem

(This document is for macOS, ChatGPT is a co-author)

## System prompts

When connecting to a machine with ssh, you may encounter the following prompts

```plaintext
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:jdifh89848nvf87y37huind8yhenu9ffff.
Please contact your system administrator.
Add correct host key in /Users/yourname/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /Users/yourname/.ssh/known_hosts:1
Host key for 192.168.0.101 has changed and you have requested strict checking.
Host key verification failed.
Connection closed
```

This error means that you have previously connected to `192.168.0.101`, but its SSH host key has changed and no longer matches the one stored in your local cache. The system will warn you: "Someone may be doing a man-in-the-middle attack", so the connection is blocked

⚠️ If you're not sure whether the target machine has been modified, verify its security before deleting the old key

If you **confirm** that you reinstalled or reset the system (or SSH service) of that machine yourself. This is a normal key change. You just need to delete the old key from `known_hosts`

## Solution

Run this command to delete the corresponding old key:

```zsh
ssh-keygen -R 192.168.0.101
```

Then reconnect:

```zsh
ssh youruser@192.168.0.101
```

The system will prompt you for the fingerprint of the new key and ask you to confirm whether to trust it. Enter yes.

## Explanation

In order to prevent man-in-the-middle attacks, SSH will record the public key of the host you have connected to in the `~/.ssh/known_hosts` file. If the key of the host is found to have changed next time, this error will be reported.

Your error message says:

```plaintext
Offending ECDSA key in /Users/yourname/.ssh/known_hosts:1
```

This means that the key on line 1 doesn't match the current host's key

You can also edit this file manually:

```zsh
nano ~/.ssh/known_hosts
```

Then delete line 1, save and exit

If you want to temporarily skip this verification, you can disable host key checking (**not recommended for regular use**):

```zsh
ssh -o StrictHostKeyChecking=no youruser@192.168.0.101
```
