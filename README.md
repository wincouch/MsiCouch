# Wix Installer for CouchDB
###############################################################################

!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!! WARNING
!!! this MSI installer is *very* alpha at this point. In particular it may not
!!! handle uninstall correctly. Make sure you have backed up your couch data
!!! in a safe location. Your help in testing is (appreciated)[https://github.com/wincouch/wixcouch/issues#]
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!

## Purpose
###############################################################################

This guide covers how to build an MSI install package, via WiX, for both
Erlang R14B01 and higher, and also CouchDB.

## Context
###############################################################################

The current CouchDB installer based on InnoSetup has limitations for both
upgrading and also managing large deployments in a standard Windows-like way.

MSI is the Microsoft-provided packaging tool for Windows. Key advantages are:

- installation of MSI packages in GUI, silent, or scripted modes
- Group Policy (GPO) deployments leveraging Active Directory membership
- advanced features such as firewall management, services and reg/file updates

WiX is the de-facto open-source toolset, used to build XML-based scripts that
subsequently are compiled down to an MSI.

## Requirements
###############################################################################

The key requirements for the Erlang installer are:

- deploy bundled erlang binaries
- run "Install.exe -s" to install

What's been ignored:
- handling an existing, running Erlang application during (un)install
- the uninstaller needs to "epmd -kill" to avoid hanging due to stray epmd
- handling an upgrade to erts and libraries via upgrade
- setup of windows firewall to support epmd & other erlang functionality
- any service manipulation e.g. via erlsrv.exe
- removing reg entries on uninstall

The key requirements for the CouchDB installer are:

- deploy bundled CouchDB binaries
- run erlsrv.exe to set up CouchDB service
- edit local.ini to point data files to appropriate non-admin file locations
- update windows firewall with a valid exclusion for werl,erl,epmd
- play nicely with the erlang installation
- be able to be chained/merged into other people's installations for bundling

What's been ignored:
- removing reg entries on uninstall (other than calling erlsrv.exe)
- avoiding deleting your data, logfiles, & local.ini on uninstall
- so far your data should be safe, & the log, just not local.ini

## What's needed?
###############################################################################

You'll need to install a fresh build of CouchDB in its own directory for
bundling. Don't run it, but go in & prune out database files & other crud.

        var/lib/couchdb/*
        var/log/couchdb/couch.log
        bin/erl.ini
        erts-5.8.2/bin/erl.ini

Lastly, convert `etc/*.ini` to PC/ANSI instead of UNIX file format.

Install the (wix compiler)[http://wix.codeplex.com/#] and
(warsetup)[http://sourceforge.net/projects/warsetup/files/]

TODO:

- add more detail on what happens next...
- how to setup warsetup to find your couchdb vanilla install
- testing & running the build
        
## How to build it?
###############################################################################

- made a basic installer using (warsetup)[http://warsetup.jgaa.com/]
- created a new CouchDB install banner
- created shortcuts for the wiki, couchdb project, futon admin interface

TODO:

- modify this base package with wix
- include firewall, running as a service
- include start menu icons, shortcuts + URLs
- figure out how to call custom actions using warsetup
- how can we set windows firewall where required?
- worry about UAC etc

## Useful Links
###############################################################################

No websites were irreparably harmed during the making of these notes but some
were mightily abused:

### WiX & Tools

- http://wix.codeplex.com/
- http://wix.sourceforge.net/
- http://wix.sourceforge.net/coretoolset.html
- http://wixedit.sourceforge.net/
- http://wix.sourceforge.net/votive.html

### Freeware Tools

- http://warsetup.jgaa.com/

### Commercial Tools

Many of the following offer a free variant:

- http://www.instedit.com/
- http://www.instyler.com/setup/default.aspx **
- http://www.advancedinstaller.com/

### MSI and WiX Tips & Tutorials

- http://www.codeproject.com/KB/install/WixTutorial.aspx
- http://www.tramontana.co.hu/wix/
- http://robmensching.com/blog/posts/2007/11/20/WiX-editors
- http://blogs.msdn.com/b/jrock/
