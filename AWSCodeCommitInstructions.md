# AWS CodeCommit Instructions

The following are a set of instructions to setup from scratch a user, git repository and HTTPS access.

As I prepared these statements I am working on Ubuntu 17.10.

## Assumptions

- Working AWS account.
- Admin rights to create users.
- `awscli` installed <http://docs.aws.amazon.com/cli/latest/userguide/installing.html>
- `awscli` configured <http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html>

### Install awscli

```bash
$ pip install awscli --upgrade --user
```

### Configure awscli

```bash
$ aws configure
AWS Access Key ID [None]: XXXXXXXXXXXXXXXXXXXX
AWS Secret Access Key [None]: XXXXXXXXXX...XXXXXXXXXX
Default region name [None]: us-west-2
Default output format [None]: json
```

## Create User

Create a group for `git` users.

```bash
$ aws iam create-group --group-name dubelyoo-CodeCommitPowerUsers
```

Add permissions to the new group.

```bash
$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/AWSCodeCommitPowerUser --group-name dubelyoo-CodeCommitPowerUsers
$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/IAMSelfManageServiceSpecificCredentials --group-name dubelyoo-CodeCommitPowerUsers
$ aws iam attach-group-policy --policy-arn arn:aws:iam::aws:policy/IAMReadOnlyAccess --group-name dubelyoo-CodeCommitPowerUsers
```

Create a user.

```bash
$ aws iam create-user --user-name warren.gavin
```

Add user to a group.

```bash
$ aws iam add-user-to-group --group-name dubelyoo-CodeCommitPowerUsers --user-name warren.gavin
```

As far as I can tell, you cannot create the "HTTPS Git credentials for AWS CodeCommit" via `awscli`. This has to be done via the AWS Console.

- <http://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html>

dconf write /org/gnome/desktop/interface/cursor-size 48
