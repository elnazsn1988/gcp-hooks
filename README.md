# GCP Synced Repository 
You may have heard Google Cloud Platform trying to make a big break with its new cloud services by offering small grants and vouchers. However, ordinary gitizens might have no clue how to make GCP play nice with current workflows. 

## What is this?
Use the hooks in this repository [bare](http://www.saintsjd.com/2011/01/what-is-a-bare-git-repository/) in a bare repository you would like synced with google cloud storage services.

## Who would need this?
If you use datalad, git-annex or version control large binary files and don't want to post your files on github. Maybe you want to just avoid using google repositories beacuse of no shell access and would rather sync to a simple bucket?

## How does it work?
This repo attempts to rsync google cloud storage after every push. Since it requires software on the receiving end, this only works if you push to a local central repo on a machine with all dependencies installed. 

In general, you fork + clone this template repository as a bare repo on your local machine. Then clone the bare repo to create a work tree and add + commit files to it. Push from the work tree to the local bare repository to sync to GCP.

## Requirements
1. Install and configure gcloud on the machine you will sync from
     `curl https://sdk.cloud.google.com | bash`
     `exec -l $SHELL`
     `gcloud init`

2. You may also have to authorize your machine to log in
	`gcloud auth login`

3. Make sure you've already created a project & storage bucket. This can be done in the browser or using gsutil.
	`gsutil mb -p [PROJECT_NAME] -c [STORAGE_CLASS] -l [BUCKET_LOCATION] gs://[BUCKETNAME]`

4. Set the permissions of the bucket so that everyone you want, can indeed, write to it.
	`gsutil acl help # set permissions with gsutil`

5. Add your local binaries (i.e., wherver gcloud/gsutil lives) to your path in your `~/.bashrc` file
	`echo "export PATH=/usr/local/bin:${PATH} >> ~/.bashrc`

## How to use this?
1. Make a bare repository on a machine with gcloud installed
	`cd /my/local/bare-repos`
	`mkdir gcp-hooks-test`
	`git init --bare`

2. Clone this repository
	`git clone https://github.com/seldamat/gcp-hooks.git /my/local/repos/gcp-hooks-test`

3. Copy the pre and post receive hooks to the bare repository
	`cp -iv /my/local/repos/gcp-hooks-test/pre-receive /my/local/bare-repos/gcp-hooks-test/hooks/`
	`cp -iv /my/local/repos/gcp-hooks-test/post-receive /my/local/bare-repos/gcp-hooks-test/hooks/`

4. Clone the bare repo to create a work tree then add + commit all your desired files to the new work tree

5. Push to the bare repo (assuming it is set with remote name origin)
	`git push -o GCS_BUCKET='gs://BUCKETNAME' origin master`

6. You can always create a new repository on github and add that as a remote
	`git remote add github https://github.com/user/new-github-project.git`


## An aside or two..
* Use this with [git-annex](https://git-annex.branchable.com/) to keep all your large files off of github and stashed in GCP.
* Use this with [git-crypt](https://www.agwa.name/projects/git-crypt/) to keep your files encrypted / secret on GCP.
* The central repo may be on a non-local server you have install permissions on (haven't tested this)
* Make sure to configure your ~/.bashrc file
* GCS_ARRAY and GCS_BUCKET are variables automatically set/updated in your ~/.bashrc file.
* Hooks call #!/bin/bash shell by default, if not desired you can change this in .git/hooks/[pre-receive|post-receive]
* Don't ever put your private keys in your repo.

Let me know what you think! Is this useful? Is there something missing?
