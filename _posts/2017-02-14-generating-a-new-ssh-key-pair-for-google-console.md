---
date: '2017-02-14 12:38 +0100'
published: true
title: Generating a new SSH key-pair for Google Console
category:
  - Development
---
```
ssh-keygen -t rsa -f ~/.ssh/my-ssh-key -C [USERNAME]
```
```
chmod 400 ~/.ssh/my-ssh-key
```

Go to the metadata page for your project.

Click SSH Keys to show a list of project-wide public SSH keys.

Click the Edit button so that you can modify the public SSH keys in your project.

Obtain the contents of the ~/.ssh/my-ssh-key.pub public key file with the cat command.

```
cat ~/.ssh/my-ssh-key.pub
```

Copy the output from the cat command and paste it as a new item in the list of SSH keys.

At the bottom of the SSH Keys page, click Save to save your new project-wide SSH key.

The public key is now set to work across all of the instances in your project. Use the ssh command to connect to your instances.
