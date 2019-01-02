# terraform_kitchen_extensive_ec2
Content of this repo is guideline on features of extensive Kitchen-Terraform by developing a Terraform module which configures resources on the Amazon Web Services (AWS) platform.

**Note that all this is tested on Ubuntu OS**

## Repo content
- `main.tf` - Terraform configuration file
- `variables.tf` - Terraform variables file
- `outputs.tf` - Terraform outputs file 
- `.kitchen.yml` - Kitchen configuration file
- `Gemfile` -  Specify the the ruby version, and all gems needed for Kitchen test
- `test directory - Contain files needed for the kitchen test itself.
- `ssh.sh` - bash script which generates SSH key 
- `<file>.env` - **ATTENTION! This file is not uploaded because contain sensitive information!** Contain AWS information needed to terraform in order to create the ec2 image. Please create your own file (the name, before extention, is not really matters) in the following format:
  ```
  export AWS_ACCESS_KEY_ID=<AWS_Access_Key_ID>
  export AWS_SECRET_ACCESS_KEY=<AWS_secret_key>
  export AWS_DEFAULT_REGION=<your_region>
  ```
- `file.tfvars` - **ATTENTION! This file is not uploaded because contain sensitive information!** Kitchen is going to use the variables in this file while creating the testing enironment. Please create your own file in the following format:
  ```
  aws_access_key = "your_access_key"
  aws_secret_key = "your_secret_key"
  instances_ami = "your_ami"
  key_pair_public_key = "path/to/your/key"
  subnet_availability_zone = "your_region"

  ```
## Requierments
- You need to have Terraform tool installed
- You need to have functional AWS Account
- You need to have kitchen tool installed
Please follow the instructions below in order to run this project.

## Installing **Terraform**
- Download required package from [here](https://www.terraform.io/downloads.html)
- Change to directory where package is download. For example: `cd $HOME/Downloads/` 
- Type on your terminal: `unzip <Downloaded_TF_Package>`
- Type on your terminal: `sudo mv terraform /usr/local/bin/`
- Check whether Terraform is available with:  `terraform --version` command

## Make **AWS Account** functional
- If you do not have AWS account click [here](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/) in order to create one.
- Set [MFA](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#multi-factor-authentication)
- Set [Access Key ID and Secret Access Key ](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys)
- Set [Key Pair](https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#key-pairs)
- Note that you have default security group in AWS. In order to be able to access the created terraform image via ssh, you need to add a specific **rule** in your security group. Follow the steps below in order to add this rule:
  - Once you logged in to AWS Console, click on **Security Groups**
  - from button **Actions** select **Edit inbound rules**
  - When the dialog window appears click on ***Add Rule* button
  - choose **SSH** from **Type** drop-down menu 
  - choose **Anywhere** from **Source** drop-down menu 
  - click on **Save** button

Now you can proceed further

## Install  **kitchen** tool
- Type: `sudo apt-get install rbenv ruby-dev ruby-bundler`
- add to your ~/.bash_profile: 
```
eval "$(rbenv init -)"
true
export PATH="$HOME/.rbenv/bin:$PATH"
```
- do `. ~/.bash_profile` in order to apply the changes made in .bash_profile 

## Prepare your PC environment for this **Terraform-Kitchen project**
- Download the repo **berchev/terraform_kitchen_extensive_ec2**: `git clone https://github.com/berchev/terraform_kitchen_extensive_ec2.git`
- Change to terraform_kitchen_extensive_ec2: `cd terraform_kitchen_extensive_ec2`
- Create a file which ends on **.env** (check **Repo content** section)
- for default region specify `us-east-1`
- Type in your terminal `. <file>.env` or `source <file>.env`, in order to export AWS data as env variables   
- Run ssh.sh script in order to generate SSH key used later on **.kitchen.yml file**: `./ssh.sh`
-  Make sure you are into the directory which consist `Gemfile` and type: `bundle install --path vendor/bundle` in order to install all needed gems for the test
- Start kitchen **centos** instance: `bundle exec kitchen create centos`
- Provision and configuring kitchen **centos** instance: `bundle exec kitchen converge centos`
- Run automated test on **centos** instance: `bundle exec kitchen verify centos`
- Destroy created **centos** instance after the test: `bundle exec kitchen destroy centos`
- Change the defaut region in order to be able to perform the test on **ubuntu** instance: `export AWS_DEFAULT_REGION=us-west-2` 
- Start kitchen **ubuntu** instance: `bundle exec kitchen create ubuntu`
- Provision and configuring kitchen **ubuntu** instance: `bundle exec kitchen converge ubuntu`
- Run automated test on **ubuntu** instance: `bundle exec kitchen verify ubuntu`
- Destroy created **ubuntu** instance after the test: `bundle exec kitchen destroy ubuntu`

## Kitchen results
- **Centos** test results:
```
Verifying local

Profile: Extensive Kitchen-Terraform (extensive_suite)
Version: 0.1.0
Target:  local://

  ✔  inspec_attributes: static terraform output
     ✔  static terraform output should eq "static terraform output"
     ✔  static terraform output should eq "static terraform output"
  ✔  state_file: 0.11.8
     ✔  0.11.8 should match /\d+\.\d+\.\d+/


Profile Summary: 2 successful controls, 0 control failures, 0 controls skipped
Test Summary: 3 successful, 0 failures, 0 skipped
Verifying host ec2-3-85-53-113.compute-1.amazonaws.com of remote

Profile: Extensive Kitchen-Terraform (extensive_suite)
Version: 0.1.0
Target:  ssh://centos@ec2-3-85-53-113.compute-1.amazonaws.com:22

  ✔  operating_system: centos
     ✔  centos should eq "centos"
  ✔  reachable_other_host: Host 100.26.137.6

     ✔  Host 100.26.137.6
      should be reachable


Profile Summary: 2 successful controls, 0 control failures, 0 controls skipped
Test Summary: 2 successful, 0 failures, 0 skipped
Verifying host ec2-54-226-237-250.compute-1.amazonaws.com of remote

Profile: Extensive Kitchen-Terraform (extensive_suite)
Version: 0.1.0
Target:  ssh://centos@ec2-54-226-237-250.compute-1.amazonaws.com:22

  ✔  operating_system: centos
     ✔  centos should eq "centos"
  ✔  reachable_other_host: Host 100.26.137.6

     ✔  Host 100.26.137.6
      should be reachable


Profile Summary: 2 successful controls, 0 control failures, 0 controls skipped
Test Summary: 2 successful, 0 failures, 0 skipped
       Finished verifying <extensive-suite-centos> (0m27.96s).
-----> Kitchen is finished. (0m29.15s)
```

- **Ubuntu** test results:
```
Verifying local

Profile: Extensive Kitchen-Terraform (extensive_suite)
Version: 0.1.0
Target:  local://

  ✔  inspec_attributes: static terraform output
     ✔  static terraform output should eq "static terraform output"
     ✔  static terraform output should eq "static terraform output"
  ✔  state_file: 0.11.8
     ✔  0.11.8 should match /\d+\.\d+\.\d+/


Profile Summary: 2 successful controls, 0 control failures, 0 controls skipped
Test Summary: 3 successful, 0 failures, 0 skipped
Verifying host ec2-35-165-245-217.us-west-2.compute.amazonaws.com of remote

Profile: Extensive Kitchen-Terraform (extensive_suite)
Version: 0.1.0
Target:  ssh://ubuntu@ec2-35-165-245-217.us-west-2.compute.amazonaws.com:22

  ✔  operating_system: ubuntu
     ✔  ubuntu should eq "ubuntu"
  ✔  reachable_other_host: Host 18.236.136.158

     ✔  Host 18.236.136.158
      should be reachable


Profile Summary: 2 successful controls, 0 control failures, 0 controls skipped
Test Summary: 2 successful, 0 failures, 0 skipped
Verifying host ec2-34-209-242-119.us-west-2.compute.amazonaws.com of remote

Profile: Extensive Kitchen-Terraform (extensive_suite)
Version: 0.1.0
Target:  ssh://ubuntu@ec2-34-209-242-119.us-west-2.compute.amazonaws.com:22

  ✔  operating_system: ubuntu
     ✔  ubuntu should eq "ubuntu"
  ✔  reachable_other_host: Host 18.236.136.158

     ✔  Host 18.236.136.158
      should be reachable


Profile Summary: 2 successful controls, 0 control failures, 0 controls skipped
Test Summary: 2 successful, 0 failures, 0 skipped
       Finished verifying <extensive-suite-ubuntu> (0m20.84s).
-----> Kitchen is finished. (0m21.96s)
```

## TO DO
