# Creating an AMI containing the node app

This repo will show how to create an AMI containing the node app. It will explain how to use berksfile and packer, and explain the provisioners.

## Prerequisites

- git
- chef
- AWS

## Berksfile

The berksfile contains the cookbook `node` linked with the set github.
To install these locally run:
```
$ berks vendor
```

A berks-cookbooks file should then appear.

You are then ready to provision your packer.json file.

## packer.json

Within the packer.json, the first thing provisioned was installing the node::default run_list.
This will test and install the environment for the app to work.
```
"provisioners": [
  {
    "type": "chef-solo",
    "cookbook_paths": ["./berks-cookbooks"],
    "run_list": ["node::default"]
  },
```

After, I have created a directory `/home/ubuntu/app` and then put my `app/` into that directory.
```
  {
    "type": "shell",
    "inline": ["mkdir /home/ubuntu/app"]
  },
  {
    "type": "file",
    "source": "app/",
    "destination": "/home/ubuntu/app"
  },
```

Finally, I have told my machine to set the app up so it is ready to start.
```
  {
    "type": "shell",
    "inline": ["cd /home/ubuntu/app", "sudo npm install", "sudo npm test"]
  }
]
}
```

## Creating the AMI

To create the AMI run:
```
$ packer validate packer.json
```
If this is successful, then run:
```
$ packer build packer.json
```
Once this finishes building, your AMI should appear in AWS.
