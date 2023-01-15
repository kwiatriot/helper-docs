# SSH Key Info

## Generate key

```markdown
ssh-keygen -b 4096 
```
This will ask where to store the key and if you want a password on the file

[ssh-keygen man page](https://linux.die.net/man/1/ssh-keygen)

## Copy Key to Remote Server

### Using ssh-copy-id

```markdown
ssh-copy-id username@remote-host
```

### Using password-based ssh

```markdown
cat ~/.ssh/id_rsa.pub | ssh username@remote-host "mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys && chmod -R go= ~/.ssh && cat >> ~/.ssh/authorized_keys"
```