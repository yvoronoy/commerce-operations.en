---
title: Install Guide
description: Use this guide to install [!DNL Site-Wide Analysis Tool] for your website
---
# Install Guide

The [!DNL Site-Wide Analysis Tool] provides 24/7 real-time performance monitoring, reports, and recommendations to ensure the security and operability of Adobe Commerce on cloud infrastructure installations. It also provides detailed information about available and installed patches, third-party extensions, and your Adobe Commerce installation.

>[!INFO]
>
>Learn [how to enable](../site-wide-analysis-tool/access.md) the [!DNL Site-Wide Analysis Tool] and generate reports.

If you have an on-premises installation of Adobe Commerce, you must install an agent on your infrastructure to use the tool. You do not need to install the agent on Adobe Commerce on cloud infrastructure projects.

## Agent

The [!DNL Site-Wide Analysis Tool] Agent allows you to use the [!DNL Site-Wide Analysis Tool] for on-premises installations of Adobe Commerce.

The [!DNL Site-Wide Analysis Tool] Agent collects application and business data, analyzes it, and provides additional insights about the health of your installation so that you can improve customer experience. It monitors your application and helps you identify performance, security, availability, and application issues.

Installing the agent requires the following steps:

1. Verify system requirements.

1. Configure API keys in the [!UICONTROL Commerce Services Connector] extension.

1. Install the agent.

1. Run the agent.

>[!INFO] 
>
>The agent supports multi-node Adobe Commerce installations. You must install and configure the agent on each node.

## System requirements

Your on-premises infrastructure must meet the following requirements before installing the agent:

- Operating systems

  - [!DNL Linux x86-64] distributions, such as [!DNL RedHat Enterprise Linux (RHEL)], [!DNL CentOS], [!DNL Ubuntu], [!DNL Debian], and similar

  >[!IMPORTANT]
  >
  >Adobe Commerce is not supported on [!DNL Microsoft Windows] or [!DNL macOS].

- Adobe Commerce 2.4.1 or later

- [!DNL Commerce Services Connector extension]

- PHP CLI

- Bash/shell utilities

  - `grep`
  
  - `awk`

  - `nice`

  - `grep`

## [!DNL Commerce Services Connector]

The agent requires the [[!DNL Commerce Services Connector]](https://experienceleague.adobe.com/docs/commerce-merchant-services/user-guides/saas.html) extension to be installed on your system and [configured](https://experienceleague.adobe.com/docs/commerce-merchant-services/user-guides/saas.html) with API keys. To verify that the extension is installed, run the following command:

```bash
bin/magento module:status Magento_ServicesConnector
```

If you have installed the extension and configured it using an existing API key for a different service, you **MUST regenerate the API key** and update it in the Adobe Commerce Admin for the agent.

1. Put your website into [maintenance mode](../../installation/tutorials/maintenance-mode.md).

1. Log into [accounts.magento.com](https://account.magento.com/customer/account/login?_ga=2.164207871.117144580.1649172612-1623400270.1640858671).

1. Click **[!UICONTROL API Portal]**.

1. Click **[!UICONTROL Delete]** next to the existing API Key.

1. [Configure](https://experienceleague.adobe.com/docs/commerce-merchant-services/user-guides/saas.html) a new API key.

>[!IMPORTANT]
>
> If you generate new keys in the API Portal, immediately update the API keys in the [!DNL Admin configuration]. If you generate new keys and do not update them in the [!DNL Admin], your SaaS extensions will no longer work and you will lose valuable data.

If the extension is not installed, use the following instructions to install it:

1. Add the extension to your `composer.json` file and install it.

   ```bash
   composer require magento/services-connector:1.*
   ```

1. Enable the extension.

   ```bash
   bin/magento module:enable Magento_ServicesConnector
   ```

1. Update the database schema.

   ```bash
   bin/magento setup:upgrade
   ```

1. [Configure API Keys](https://experienceleague.adobe.com/docs/commerce-merchant-services/user-guides/saas.html) to connect the extension to your system.

## Install the agent

We have created a [shell script](https://github.com/magento-swat/install-agent-helpers/blob/main/install.sh) to simplify installation. We recommend using the shell script, but you can follow the [manual installation](#manual) method if necessary.

>[!INFO]
>
>After the agent is installed, it will self-update when a new release is available.

### Scripted

1. Download and execute the shell script.

   ```bash
   bash -c "$(wget -qO - https://raw.githubusercontent.com/magento-swat/install-agent-helpers/main/install.sh)"
   ```
   
   >[!TIP]
   >
   >We recommend installing the agent outside of your root Adobe Commerce project directory.

1. Verify installation.

   ```bash
   ./scheduler -v
   ```

   ```bash
   Version: 1.0.1
   Success exit.
   ```

1. After downloading and installing the agent, you must [configure it to run](#run-the-agent) using one of the following methods:

   - [Service](#service) (preferred if you have root access)
   
   - [Cron](#cron)

### Manual {#manual}

If you do not want to use our [shell script](https://github.com/magento-swat/install-agent-helpers/blob/main/install.sh) to install the agent, then you must manually install it by following these steps:

1. Create a directory where you want to download the agent.

   >[!TIP]
   >
   >We recommend installing the agent outside of your root Adobe Commerce project directory.

1. Download the binary file and unpack it.

   >[!INFO]
   >
   >To use the [!DNL Site-Wide Analysis Tool], you must first read and accept the Terms of Use that are presented when you access the dashboard from the Adobe Commerce Admin.

   For the **AMD64** architecture:

   1. Download the launcher archive.

      ```bash
      curl -O https://updater.swat.magento.com/launcher/launcher.linux-amd64.tar.gz
      ```

   1. Unpack the launcher archive.

      ```bash
      tar -xf launcher.linux-amd64.tar.gz
      ```

   For the **ARM64** architecture:

   1. Download the launcher archive.

      ```bash
      curl -O https://updater.swat.magento.com/launcher/launcher.linux-arm64.tar.gz
      ```

   1. Inpack the launcher archive.

      ```bash
      tar -xf launcher.linux-arm64.tar.gz
      ```

1. *(Optional)* Verify the signature for the checksum file.

   ```bash
   echo -n "LS0tLS1CRUdJTiBQVUJMSUMgS0VZLS0tLS0KTUlJQ0lqQU5CZ2txaGtpRzl3MEJBUUVGQUFPQ0FnOEFNSUlDQ2dLQ0FnRUE0M2FBTk1WRXR3eEZBdTd4TE91dQpacG5FTk9pV3Y2aXpLS29HendGRitMTzZXNEpOR3lRS1Jha0MxTXRsU283VnFPWnhUbHZSSFhQZWt6TG5vSHVHCmdmNEZKa3RPUEE2S3d6cjF4WFZ3RVg4MEFYU1JNYTFadzdyOThhenh0ZHdURVh3bU9GUXdDcjYramFOM3ErbUoKbkRlUWYzMThsclk0NVJxWHV1R294QzBhbWVoakRnTGxJUSs1d1kxR1NtRGRiaDFJOWZqMENVNkNzaFpsOXFtdgorelhjWGh4dlhmTUU4MUZsVUN1elRydHJFb1Bsc3dtVHN3ODNVY1lGNTFUak8zWWVlRno3RFRhRUhMUVVhUlBKClJtVzdxWE9kTGdRdGxIV0t3V2ppMFlrM0d0Ylc3NVBMQ2pGdEQzNytkVDFpTEtzYjFyR0VUYm42V3I0Nno4Z24KY1Q4cVFhS3pYRThoWjJPSDhSWjN1aFVpRHhZQUszdmdsYXJSdUFacmVYMVE2ZHdwYW9ZcERKa29XOXNjNXlkWApBTkJsYnBjVXhiYkpaWThLS0lRSURnTFdOckw3SVNxK2FnYlRXektFZEl0Ni9EZm1YUnJlUmlMbDlQMldvOFRyCnFxaHNHRlZoRHZlMFN6MjYyOU55amgwelloSmRUWXRpdldxbGl6VTdWbXBob1NrVnNqTGtwQXBiUUNtVm9vNkgKakJmdU1sY1JPeWI4TXJCMXZTNDJRU1MrNktkMytwR3JyVnh0akNWaWwyekhSSTRMRGwrVzUwR1B6LzFkeEw2TgprZktZWjVhNUdCZm00aUNlaWVNa3lBT2lKTkxNa1cvcTdwM200ejdUQjJnbWtldm1aU3Z5MnVMNGJLYlRoYXRlCm9sdlpFd253WWRxaktkcVkrOVM1UlNVQ0F3RUFBUT09Ci0tLS0tRU5EIFBVQkxJQyBLRVktLS0tLQ==" | base64 -d > release.pub
   ```

   ```bash
   openssl dgst -sha256 -verify release.pub -signature launcher.sha256 launcher.checksum
   ```

1. *(Optional)* Verify the checksum.

   ```bash
   shasum -a 512 -c launcher.checksum
   ```

1. Create the `config.yaml` file with the following contents.

   ```yaml
   project:
     appname: "Acme Inc" # Company or site name that you provided when installing the agent
   application:
     phppath: php # Path to your PHP CLI interpreter (usually /usr/bin/php)
     magentopath: /var/www/html/example.com # Root directory where your Adobe Commerce application is installed (usually /var/www/html)
     checkregistrypath: /path/to/swat-agent/tmp # Temporary directory for the agent (usually /usr/local/swat-agent/tmp)
     issandbox: false # Enabling sandbox mode to use the agent on staging environment (true or false)
     database:
       user: your-adobe-commerce-db-username # Database user for your Adobe Commerce installation
       password: your-password # Database password for the specified user for your Adobe Commerce installation
       host: 127.0.0.1 # Database host for your Adobe Commerce installation
       dbname: your-adobe-commerce-db-name # Database name for your Adobe Commerce installation
       port: 3306 # Database port for your Adobe Commerce installation (usually 3306)
       tableprefix: # Table Prefix for your Adobe Commerce installation (default value: empty)
    enableautoupgrade: true # Enables automatic upgrade (restart required after an upgrade; agent does not check for upgrades if the option is disabled; true or false)
    runchecksonstart: true # Collect data on the first run (Usually 1)
    loglevel: error # Determines what events are logged based on severity (usually error)
   ```

1. Verify the installation.

   ```bash  
   scheduler -v
   ```

   ```bash
   Version: 1.0.1
   Success exit.
   ```

1. After downloading and installing the agent, you must [configure it to run](#run-the-agent) using one of the following methods:

   - [Service](#service) (preferred if you have root access)
   
   - [Cron](#cron)

## Run the agent {#run-the-agent}

We recommend configuring the agent to run as a service. If you have limited access to your infrastructure and do not have root permissions, then you must use [cron](#cron) instead.

### Service {#service}

1. Create a systemd unit file `(/etc/systemd/system/scheduler.service)` with the following configuration (replace `<filesystemowner>` with the Unix user that owns the directory where the agent and the Adobe Commerce software is installed). If you downloaded the agent as the root user, change the directory and nested files owner.

   ```config
   [Unit]
   Wants=network.target
   After=network.target

   [Service]
   Type=simple
   User=<filesystemowner>
   ExecStart=/path/to/agent/scheduler
   Restart=always
   RestartSec=3

   [Install]
   WantedBy=multi-user.target
   ```

1. Launch the service.

   ```bash
   systemctl daemon-reload
   ```

   ```bash
   systemctl start scheduler
   ```

   ```bash
   systemctl enable scheduler
   ```

1. Validate that the service is up and running.

   ```bash
   journalctl -u scheduler | grep "Application is going to update" | tail -1 && echo "Agent is successfully installed"
   ```

### Cron {#cron}

If you do not have root permissions or do not have permissions to configure a service as root, you can use cron instead.

Update your cron schedule:

```bash
( crontab -l ; echo "* * * * * flock -n /tmp/swat-agent.lockfile -c '/path/to/agent/scheduler' >> /path/to/agent/errors.log 2>&1" ) | sort - | uniq - | crontab -
```

## Uninstall

Run the following commands to uninstall the service from your system and remove all generated files:

1. Stop the scheduler.

   ```bash
   systemctl stop scheduler
   ```

1. Disable the scheduler.

   ```bash
   systemctl disable scheduler
   ```

1. Remove the scheduler service's `systemd` unit file.

   ```bash
   rm /etc/systemd/system/scheduler.service
   ```

1. Reload the `systemd` manager configuration.

   ```bash
   systemctl daemon-reload
   ```

1. Reset any `systemd` units from a failed state.

   ```bash
   systemctl reset-failed
   ```

1. Remove the scheduler service directory.

   ```bash
   rm -rf <CHECK_REGISTRY_PATH> #see SWAT_AGENT_APPLICATION_CHECK_REGISTRY_PATH in /etc/systemd/system/scheduler.service
   ```

1. Remove the scheduler binary file.

   ```bash
   rm /usr/local/bin/scheduler
   ```

If you configured the agent to run with cron instead, use the following instructions:

1. Remove the agent from the crontab list.

   ```bash
   crontab -e
   ```

1. Stop the running job.

   ```bash
   ps aux | grep scheduler
   ```

1. Remove the directory where you installed the agent.

   ```bash
   rm -rf swat-agent
   ```

## Override the configuration file

You can override the values that you specified in the configuration file during installation by using environment variables. This preserves backward compatibility with earlier versions of the agent. See the following table for recommended values:

| PROPERTY | DESCRIPTION |
| --- | --- |
| `SWAT_AGENT_APP_NAME` | Company or site name that you provided when installing the agent |
| `SWAT_AGENT_APPLICATION_PHP_PATH` | Path to your PHP CLI interpreter (usually `/usr/bin/php`) |
| `SWAT_AGENT_APPLICATION_MAGENTO_PATH` | Root directory where your Adobe Commerce application is installed (usually `/var/www/html`) |
| `SWAT_AGENT_APPLICATION_DB_USER` | Database user for your Adobe Commerce installation |
| `SWAT_AGENT_APPLICATION_DB_PASSWORD` | Database password for the specified user for your Adobe Commerce installation |
| `SWAT_AGENT_APPLICATION_DB_HOST` | Database host for your Adobe Commerce installation |
| `SWAT_AGENT_APPLICATION_DB_NAME` | Database name for your Adobe Commerce installation |
| `SWAT_AGENT_APPLICATION_DB_PORT` | Database port for your Adobe Commerce installation (usually `3306`) |
| `SWAT_AGENT_APPLICATION_DB_TABLE_PREFIX` | Table Prefix for your Adobe Commerce installation (default value: `empty`) |
| `SWAT_AGENT_APPLICATION_DB_REPLICATED` | Whether your Adobe Commerce installation has a secondary database instance (usually `false`) |
| `SWAT_AGENT_APPLICATION_CHECK_REGISTRY_PATH` | Temporary directory for the agent (usually `/usr/local/swat-agent/tmp`) |
| `SWAT_AGENT_RUN_CHECKS_ON_START` | Collect data on the first run (usually `1`) |
| `SWAT_AGENT_LOG_LEVEL` | Determines what events are logged based on severity (usually `error`) |
| `SWAT_AGENT_ENABLE_AUTO_UPGRADE` | Enables automatic upgrade (restart required after an upgrade; agent does not check for upgrades if the option is disabled; `true` or `false`) | 
| `SWAT_AGENT_IS_SANDBOX=false` | Enabling sandbox mode to use the agent on staging environment |

>[!INFO]
>
>See [How to Access [!DNL Site-Wide Analysis Tool] and generate reports](../site-wide-analysis-tool/access.md).
