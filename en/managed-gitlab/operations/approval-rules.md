---
title: How to set up approval rules
description: Follow this guide to set up approval rules.
---

# Setting up approval rules

With {{ mgl-name }}, you can flexibly set up mandatory approval rules before any code can be added to the target branch of the project. For more information on how approval rules work, see [Approval rules](../concepts/approval-rules.md).

Before getting started, [create a service account](create-user.md#create) with administrator privileges and [add it to your {{ GL }} project](create-user.md#add-to-project). Assign the `Maintainer` or `Owner` [role]({{ gl.docs }}/ee/user/permissions.html) to the account, as other roles do not provide enough permissions to configure approval rules. Next, log in to the {{ GL }} instance and set up the approval rules via the service account.

To use approval rules:

1. [Create a {{ GL }} token](#gitlab-token).
1. [Enable approval rules](#enable).
1. [Configure approval rules](#rules).
1. [Set up Code Ownership](#code-ownership) (available in **Standard** and **Advanced** [configurations](../concepts/approval-rules.md#packages)).

If required, enable [debugging mode](#debugging) and check out the [rules for handling exceptions](#exceptions).

## Create a {{ GL }} token {#gitlab-token}

You need the [{{ GL }} token](../concepts/approval-rules.md#gitlab-token) to [enable approval rules](#enable) and access the repository, because the token is used for {{ GL }} API authentication.

To create a token:

1. Open your {{ GL }} instance.
1. Click the profile icon and select **Edit profile**.
1. Go to **Access Tokens** in the left-hand menu.
1. Click **Add new token**.
1. In the window that opens, in the **Token name** field, enter a name to easily locate the token in your {{ GL }} project.
1. In the **Expiration date** field, specify the token's expiration date.

    The default value is one month and the maximum is one year from the token creation date. The token expires at midnight UTC on the specified day.

1. Under **Select scopes**, select **api**.
1. Click **Create personal access token**.

    A new token will be generated.

1. Copy and save it. Later you will not be able to copy it in {{ GL }}.

## Enable approval rules {#enable}

1. Activate the {{ GL }} setting that prevents merging changes to the target branch until all threads in a merge request are resolved:
    1. Open your project in {{ GL }}.
    1. In the left-hand menu, select **Settings** → **Merge requests**.
    1. Under **Merge checks**, select **All threads must be resolved**.
    1. Click **Save changes**.
1. Add a system hook:
    1. Open your {{ GL }} instance.
    1. In the left-hand menu, select **Search or go to** → **Admin Area**.
    1. In the left-hand menu, select **System Hooks**.
    1. Click **Add new webhook**.
    1. Specify the hook parameters:
        * **URL**: `http://localhost:24080/default`.
        * In the **Trigger** section, disable all options except **Merge request events**, **Push events**, and **Repository update events**.
    1. Click **Add webhook**.
1. Enable the {{ GL }} setting which allows sending messages to the local network:
    1. Open your {{ GL }} instance.
    1. In the left-hand menu, select **Search or go to** → **Admin Area**.
    1. In the left-hand menu, select **Settings** → **Network**.
    1. In the **Outbound requests** section, enable the **Allow requests to the local network from system hooks** option.
    1. In the list of IP addresses and domain names, specify the `http://localhost:24080` URL.
    1. Click **Save changes**.
1. Enable approval rules in the {{ mgl-name }} instance:
    1. In the {{ yandex-cloud }} [management console]({{ link-console-main }}), select the [folder](../../resource-manager/concepts/resources-hierarchy.md#folder) where the [{{ GL }} instance](../concepts/index.md#instance) is located.
    1. Select **{{ mgl-name }}**.
    1. Select the instance and click ![image](../../_assets/console-icons/pencil.svg) **{{ ui-key.yacloud.common.edit }}** at the top of the page.
    1. In the **{{ ui-key.yacloud.gitlab.field_approval-rules }}** field, select the approval rule [configuration](../concepts/approval-rules.md#packages).

        {% include [note-approval-rules-pricing](../../_includes/managed-gitlab/note-approval-rules-pricing.md) %}

    1. In the **{{ ui-key.yacloud.gitlab.field_approval-rules-token }}** field, specify the [token you created earlier](#gitlab-token).
    1. Click **{{ ui-key.yacloud.common.save }}**.

## Set up the approval rules {#rules}

The approval rules for the repository are stored in the `APPROVALRULES` file in the root directory. The configuration is read from the [default branch]({{ gl.docs }}/ee/user/project/repository/branches/default.html) when running the instance and is automatically loaded when you modify the file.

The file consists of two sections:

* `ApprovalRules`: Describes the rules.
* `BranchGroups`: Describes which branches the rules apply to.

The structure of the `APPROVALRULES` file is as follows:

```text
ApprovalRules:
  - <rule_name>:
      approvers:
        - <username>
        ...
      groups:
        - <group_name>
        ...
      count: <required_number_of_approvals>
BranchGroups:
  - <branch_group_name>:
      branches:
        - <branch_name>
        ...
      rules:
        - <rule_name>
        ...
```

Where:

* `approvers`: Names of {{ GL }} users who can approve the merge request. The users must be added to the project either directly or via a group. When counting approvals, the merge request author is not counted in. A user on this list who had submitted commits to the merge request and is not the author can also be an approver.
* `groups`: List of names of {{ GL }} groups whose members can approve the merge request (except for members of the group for which this group is a subgroup).
* `count`: Integer from `0` to `100`. If set to `0`, the rule is optional.
* `branches`: List of names of branches whose changes require a review.
* `rules`: List of rules that apply to the specified branches.

You may use the `*` wildcard symbol instead of usernames and in branch names.

> For example, to apply the _four-eyes principle_ to the repository master branch, add the following to the `APPROVALRULES` file:
>
> ```text
> ApprovalRules:
>   - FourEyesRule:
>       approvers:
>         - "*"
>       count: 1
> BranchGroups:
>   - Master:
>       branches:
>         - master
>       rules:
>         - FourEyesRule
> ```

## Set up Code Ownership {#code-ownership}

The feature is available in **Standard** and **Advanced** [configurations](../concepts/approval-rules.md#packages).

The code ownership settings for the repository are stored in the `CODEOWNERS` file in the root directory. The configuration is read from the [default branch]({{ gl.docs }}/ee/user/project/repository/branches/default.html) when running the instance and is automatically loaded when you modify the file.

The file structure supports the [{{ GL }} syntax](https://docs.gitlab.com/ee/user/project/codeowners/reference.html), except for the use of user subgroups and email addresses as user IDs.

{% note info %}

If multiple records in the `CODEOWNERS` file include the same file or directory at once, the rules from the most recent record apply.

{% endnote %}

To use the code ownership settings when handling merge requests to specific branches, add the following record to the `APPROVALRULES` file:

```text
BranchGroups:
  - <branch_group_name>:
      branches:
        - <branch_name>
        ...
      rules:
        - CODE_OWNERS
```

## Debugging {#debugging}

Add the `detailed_AR` keyword to a merge request description to have detailed information on each rule output in a thread:

* Names of users who approved the merge request.
* How many approvals are left to be received.
* Names of remaining users who can approve the merge request.

## Handling exceptions {#exceptions}

### Incorrect configuration of approval rules {#errors}

Issues related to the `APPROVALRULES` file are handled as follows:

* If the file is missing from the repository, no approval rules apply to the repository's merge requests.
* If the file is available but is configured incorrectly:
    * Users with the `Maintainer` role get an email notification about the error.
    * All merge requests of the repository are blocked.

### Overriding the rules {#force-merge}

If you need to commit merge request changes, but the responsible team members are not available, a user with the `Maintainer` role or higher can commit the changes by overriding the existing approval rules:

1. Add a comment with the `force_merge` keyword to the merge request.
1. Update the merge request description so that {{ mgl-name }} gets notified of the changes in the merge request.

This will close the thread and unblock the merge request. During the integration, users with the `Maintainer` role or higher will receive an email notification about the violated code approval workflow.
