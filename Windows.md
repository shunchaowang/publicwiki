## WSL Environment Setup
1. Enable WSL either by 'Add/Remove Windows Feature' or running command in PowerShell as Administrator 
   `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux` then restart.
2. Install a Linux Distribution of Choice from Windows Store, I prefer Ubuntu, 
    and update `sudo apt update -y && sudo aptupgrade -y`
3. Setup Hyper.js as WSL launcher and configure it to launch ubuntu
    * `choco update` `choco install hyper`
    * install powerline fonts after downloading fonts from `https://github.com/powerline/fonts/archive/master.zip` by
      running the file in PowerShell `install.ps1`
    * launch hyper, select `edit->preference`, update font to be `Ubuntu Mono derivative Powerline`
    * launch hyper, select `edit->preference`, update `shell: 'ubuntu.exe'`, `shellArgs: []`
4. configure oh my zsh as the shell
