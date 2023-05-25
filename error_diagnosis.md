## This document is for diagnosing and solving user issues when uploading data to IRIDA

Before diving into specific errors, please briefly familiarize yourself with our IRIDA Uploader Training Document

https://github.com/phac-nml/irida-uploader-tutorial/blob/master/README.md

And out READ-THE-DOCS page. Specifically the pages on [Configuration](https://irida-uploader.readthedocs.io/en/stable/configuration.html), [Errors](https://irida-uploader.readthedocs.io/en/stable/errors.html), and [Parsers](https://irida-uploader.readthedocs.io/en/stable/parsers/miseq_v31.html)

https://irida-uploader.readthedocs.io/en/stable/index.html

# Table of Contents

[Most Common Requests](#common-requests)
* [Need credentials for IRIDA Uploader](#credentials)
* [Cannot Connect to IRIDA](#cannot-connect-to-irida)
* [Deleting a Sequencing Run](#deleting-a-sequencing-run)

[Pre-diagnosis](#pre-diagnosis)

[Pre Upload Errors](#pre-upload-errors)
* [Pre Upload Connection Error](#pre-upload-connection-error)
* [Parsing error](#parsing-error)
* [Fails to start upload, not connection or parsing error](#fails-to-start-upload-not-connection-or-parsing-error)

[Mid Upload Errors](#mid-upload-errors)
* [Mid Upload Connection Error](#mid-upload-connection-error)
* [error 10054](#error-10054)

[Post Upload Errors](#post-upload-errors)
* [File has 0 bytes on IRIDA](#file-has-0-bytes-on-irida)
* [Remove Uploaded Data](#remove-uploaded-data)

# Common Requests

The following are the most common requests related to the IRIDA Uploader

### Credentials

When a user is requesting credentials to be able to upload, an IRIDA administrator can generate a new `client_id` and `client_secret` value for them VIA the IRIDA Admin panel.

More details on this process can be found here: https://phac-nml.github.io/irida-documentation/user/administrator/#managing-system-clients

When providing `client_id` and `client_secret`, they should not be sent together on the ticket as this is a security issue. The id can be sent via the ticket, but the secret should be emailed separately directly to the user.

When providing the `base_url` make sure to provide the appropriate uploader server node. If you do not know which node to provide, consult IT.

### Cannot Connect to IRIDA

See [Pre Upload Connection Error](#pre-upload-connection-error)

### Deleting a Sequencing Run

See [Remove Uploaded Data](#remove-uploaded-data)

# Diagnosing Errors

## Pre-diagnosis

Always ask the users to provide the following details to make diagnosis easier

* Their IRIDA Instance.
  * There are multiple instances for IRIDA for different projects and regions, so determining which IRIDA they connect to is important.
* irida_uploader_status.info
  * Found in sequencing run directory
* irida-uploader.log
  * Found in sequencing run directory
* A screenshot of the error they are seeing

Most issues can be diagnosed with the log file and/or the on screen error.

The training documents [Common Errors](https://github.com/phac-nml/irida-uploader-tutorial/blob/master/README.md#common-errors) section an overview of how to interpret the `irida_uploader_status.info` and `irida-uploader.log` files

Also check the log file for the version of the IRIDA Uploader they are using. If they are using an outdated version, direct them to upgrade before doing any other diagnostics.

The latest version can be found [on the IRIDA Uploader Releases Page](https://github.com/phac-nml/irida-uploader/releases) and installation instructions are included on the [Tutorial Document](https://github.com/phac-nml/irida-uploader-tutorial/blob/master/README.md#uploading-data-to-a-project).

## Pre Upload Errors
Errors that happen before the user uploads data.

### Pre Upload Connection Error
1. The most likely cause is an invalid configuration

Check that their configuration matches the client_id and client_secret on IRIDA via the IRIDA Admin Panel

Check that base_url starts with `https://` and ends with `/api/`

2. The second most likely is network connectivity

See if the machine they are using to upload can access their irida instance, if not they may not be on the correct network / VPN
    
If it is a network/vpn issue, the issue should be routed to the appropriate IT department

### Parsing error
When the uploader cannot locate the files to upload, it is likely that the wrong parser is being used.

Sometimes user change the directory structure, so the files will need to be put back to the correct file structure format. 

 [Check the parser examples on the left hand side on readthedocs to see how the file structure should be formatted depending on the sequencer type.](https://irida-uploader.readthedocs.io/en/stable/parsers/miseq_v31.html)
    
### Fails to start upload, not connection or parsing error
If the machine the user is using to upload data does not have permissions to the folder where the data is, we see a Write Permissions Error.

If they do not have write permissions, the uploader will fail when it attempts to create the status and log file.

This can be resolved by checking the `readonly` box in the configuration so that logs do not appear, but it is usually better to have a system admin give them permission to write to that directory.

## Mid Upload Errors
Errors that happen during the transfer of data.

### Mid Upload Connection Error
There are multiple reason the Uploader may fail mid upload.

1. Is the network under heavy load, undergoing maintenance, or a spotty VPN?

When uploads fail, they can be resumed from the point they failed. The GUI Uploader will prompt the user to continue a partial run when it detects it via the `irida_uploader_status.info` file.

If the user is experiencing these issues repeatedly, we can have them enable advanced configuration options to allow for more retries and longer timeouts before retry attempts.

A good starting point is `http_max_retries = 10` and `http_backoff_factor = 2`

[More details on these settings can be found here.](https://irida-uploader.readthedocs.io/en/stable/configuration.html#options)


2. Is the upload server able to handle large files?

Occasionally users will surprise us with a file so big we didn't think it would happen. When this occurs the user will see the upload progress as normal but always fail on the same large file at the same point.

To diagnose this, a network administrator needs to check the Log files generated by the Upload Server Node, and see if it is crashing. If it is, they should increase the maximum file size.

Collect the timestamps from the `irida-uploader.log` file, and send them to the network administrator so they can use that to pin point the failure.

### error 10054
This is a network error that occurs when a network doesn't trust the packets the uploader is sending.

This issue has been fixed in the latest version, so the user will need to update if they are getting it. [The latest version of the Uploader can be found here.](https://github.com/phac-nml/irida-uploader/releases)

## Post Upload Errors
Errors that happen after (or are noticed after) upload has completed.

### File has 0 bytes on IRIDA
Sometimes NextSeq files are generated by BCL2FASTQ but have no data, then when upload is done, empty files are sent to IRIDA.

This is not an Uploader issue, but a problem with the user not checking the files after BCL2FASTQ ran. 

When this is encountered, the user should be directed to set `minimum_file_size` configuration value to a small value like `50` so errors will appear in validation step, and they won't mistakenly upload empty files.
    
### Remove Uploaded Data
Sometimes the wrong files are uploaded to a project, or the right files are uploaded to the wrong project. In these cases the files need to be deleted and reuploaded.

Make sure to be extra careful when removing data so that you do not delete the wrong files, or delete files on the wrong IRIDA instance.

1. The entire upload run needs to be removed.

[An IRIDA administrator can remove sequencing runs via the process shown here.](https://phac-nml.github.io/irida-documentation/user/administrator/#viewing-sequencing-runs)
    
2. Only part of a sequencing run needs to be removed

If only a few files need removal, an IRIDA Project Manager can delete them.

If all files from a Sequencing Run that when to a specific project need to be removed, but the entire run doesn't, use [TODO: Tool in development] to quickly delete the files.
