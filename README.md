# core11
*Scripts to Build a Trimmed-Down Windows 11 Image Using PowerShell*

---

## Overview

Welcome to the overhauled version of **core11 builder**. Transitioning from the legacy Batch implementation to a native PowerShell environment has allowed for a much more flexible, complete, and scalable solution. This architecture provides a single, unified framework capable of handling varying development demands.

Thanks to the enhanced scripting and automation capabilities of PowerShell, this tool now supports any Windows 11 release, language, or system architecture (including x64 and ARM64).

### Features
* **Automated Optimization:** Automatically strips heavy components and background telemetry to create a streamlined Windows 11 image, inspired by lightweight operating system concepts.
* **Native DISM Integration:** Leverages Deployment Image Servicing and Management (DISM) native recovery compression (`/Compact`) to reduce the final ISO size without requiring unverified external third-party utilities.
* **Minimalist Executable Footprint:** The only bundled executable is `oscdimg.exe` (sourced directly from the official Windows Assessment and Deployment Kit), which is strictly utilized to generate bootable ISO images.
* **Automated Out-of-Box Experience (OOBE):** Includes a pre-configured unattended answer file (`autounattend.xml`) designed to bypass Microsoft Account requirements during initial setup and deploy the operating system natively with the compact flag.

### Credits
This project is an evolution of the core imaging mechanics popularized by **NTDEV**. It is built upon the structural foundation and optimization logic originally introduced in the open-source **tiny11builder** project. 

* **Original Creator:** [NTDEV](https://github.com/ntdevlabs)
* **Upstream Base Code:** [tiny11builder](https://github.com/ntdevlabs/tiny11builder)

---

## Script Variants

The project is split into two distinct operational scripts depending on your targets for stability, deployment longevity, and post-installation support.

* **`core11basic.ps1` (Standard Edition):** The recommended baseline script for general deployment. It strips heavy application bloat and background telemetry while preserving the integrity of the Windows Component Store. The system remains fully serviceable, allowing the installation of future cumulative updates, language packs, and optional features.
* **`core11minimal.ps1` (Core Edition):** An aggressive reduction script tailored specifically for rapid testing, specialized virtualized sandboxes, or temporary development environments. It maximizes resource reduction by permanently stripping out servicing frameworks.

---

## Instructions

1.  Download a standard Windows 11 ISO from the official Microsoft Software Download page or via verified utilities such as Rufus.
2.  Mount the downloaded ISO image natively within Windows Explorer.
3.  Launch an elevated **PowerShell 5.1** console (Run as Administrator).
4.  Temporarily modify the script execution policy for the current process scope to allow execution:
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```
    *Note: Utilizing the `-Scope Process` modifier ensures that your system's global execution policy remains unchanged and secure once the current PowerShell session is terminated.*
5.  Execute the build script passing the required parameters for your environment:
    ```powershell
    .\core11maker.ps1 -ISO <DriveLetter> -SCRATCH <DriveLetter>
    ```
    *Note: Advanced parameter options and internal syntax details can be viewed at any time by executing `Get-Help .\core11maker.ps1`.*
6.  When prompted, provide the exact drive letter where the source Windows 11 ISO is currently mounted (enter the letter only, omitting colons or backslashes).
7.  Select the desired Stock Keeping Unit (SKU) / Windows Edition that you wish to base your lightweight image on.
8.  Allow the automation process to finalize mounting, modification, provisioning removal, and image compression.
9.  Upon successful completion, the optimized bootable image will be generated in the root script directory under the filename `core11.iso`.

---

## Features

| Standard Modifications (`core11maker`) | Core Aggressive Modifications (`core11coremaker`) |
| :--- | :--- |
| Clipchamp, News, Weather, Xbox | **Includes all Standard removals, plus the following:** |
| GetHelp, GetStarted, Feedback Hub | Complete Windows Component Store (`WinSxS`) |
| Office Hub, Solitaire, ToDo, Maps | Windows Defender *(Disabled by default; can be re-enabled)* |
| Mail & Calendar, PeopleApp, Alarms | Windows Update Service *(Disabled due to removal of WinSxS)* |
| Sound Recorder, Your Phone, Media Player | Windows Recovery Environment (`WinRE`) |
| PowerAutomate, QuickAssist, Internet Explorer | |
| Microsoft Edge, OneDrive, Tablet PC Math | |

*Important Servicing Note:* Systems deployed using the Core edition (`core11coremaker`) cannot re-install system features or language packs post-installation due to the absence of the Component Store. However, during the compilation process, the script will interactively prompt you to choose whether to bake in **.NET Framework 3.5 support** before sealing the runtime environment.

---

## Issues

* **Settings App Remnants:** While Microsoft Edge is completely deleted from the file system, dead pointers or broken shortcuts linking to it may occasionally show up inside specific menus of the native Settings application.
* **Winget Initialization Requirements:** Due to the removal of pre-provisioned application data, you may need to open the Microsoft Store and check for app updates manually before the `winget` command-line utility functions perfectly.
* **Background Provisioning Hurdles:** System packages like New Outlook and Dev Home frequently attempt to re-provision themselves via automated background store tasks. The script implements aggressive blocks against this behavior, but minor edge cases remain an ongoing target for optimization.
* **ARM64 Compilation Warnings:** When running the script against an ARM64 source image, a transient non-terminating error message may briefly flash in the console. This occurs natively because ARM64 Windows installation media does not bundle `OneDriveSetup.exe` inside the `System32` directory.

---
