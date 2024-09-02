---
draft: true
date: 2024-11-02
authors:
  - me
categories:
  - Linux
---

# Backup for M365 Business
Managing the microsoft suite of tools for a small business is challenging. I want peace of mind that everything is backed up, and I don't want to pay for, or rely on a 3rd party service for this to occur. To acheive this, I'll be using [corso backup](https://corsobackup.io/).

# Setup
Reading through the full [Corso Setup](https://corsobackup.io/docs/setup/concepts/) is helpful. It familiarizes you with the necessary Corso and M365 concepts.

## Configuring M365 Access
In order to connect to M365, we'll need three things: `AZURE_CLIENT_ID, AZURE_TENANT_ID, AZURE_CLIENT_SECRET`. Here are the steps to setup access to M365 via the azure tenant.

### Get the Azure Tenant ID
1. Login to the [Azure Portal](https://portal.azure.com) with the M365 admin account
2. Navigate to the Microsoft Entra ID service, and retrieve the Tenant ID.

### Create a New Application
1. Select the hamburber menu > All Services > Microsoft Entra ID
2. Add a new App Registration
3. Give the application a name, keep the rest default, and click Register
4. Navigate to the newly created application, and retrieve the Application (client) ID

### Set Appropriate Permissions
1. Follow the documentation [here](https://corsobackup.io/docs/setup/m365-access/). At the time of me configuring this, here are the settings I used:

    | API / PERMISSIONS NAME | TYPE | DESCRIPTION |
    |------------------------|------|-------------|
    | Calendars.ReadWrite | Application | Read and write calendars in all mailboxes |
    | ChannelMessage.Read.All | Application | Read all messages in Teams' channels |
    | ChannelSettings.Read.All | Application | Read all Teams' channel settings |
    | Chat.Read.All | Application | Read all Teams' chats and chat messages |
    | Contacts.ReadWrite | Application | Read and write contacts in all mailboxes |
    | Directory.Read.All | Application | Read all organization directory data |
    | Files.ReadWrite.All | Application | Read and write files in all site collections |
    | MailboxSettings.Read | Application | Read all user mailbox settings |
    | Mail.ReadWrite | Application | Read and write mail in all mailboxes |
    | Member.Read.Hidden | Application | Read hidden group memberships |
    | Sites.FullControl.All | Application | Have full control of all site collections |
    | TeamMember.Read.All | Application | Read all Teams' user memberships |
    | TeamSettings.Read.All | Application | Read all Teams' settings |
    | User.Read.All | Application | Read all users' full profiles |

2. Grant admin consent
3. Generate Client Secret