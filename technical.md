## Technical pre-requisite

All computational steps will be done on a server running on the de.NBI cloud system.
In order to connect to the server, you will use two pieces of software:

* Cyberduck: this is a software available for all operating systems allowing to browse files on a remote server; this will allow you to open files that you will be generating on the distant server (for example pdf files showing QC plots)
* ssh-client: the ssh-protocol allows you to securely connect to a distant server using a terminal; 

In addition, you will need to generate a pair of ssh-keys which will allow you to connect to the server without a password! 

### MacOS / Linux

If you are using macOS or Linux, you have a native terminal environment, with all required commands, like `ssh`.

First, install [Cyberduck](https://cyberduck.io/).

Next, you need to generate a pair of ssh-keys:

1. open a terminal (the black window)
2. type the following command: `ssh-keygen`
3. press return for all the questions

This will generate a pair of **ssh keys**, composed of a *private key* and a *public key*. If you have pressed return at all questions, this command will have generated new files in the `.ssh` directory in you home folder; inside this directory, you will find 2 files:

* a file `id_rsa` : this contains your *private key* , and **should not be disclosed** !
* a file `id_rsa.pub` : this contains your *public key*, which should start with `ssh-rsa ...` followed by a very long string, for example
> ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDO7JOEqg9Zuar9MmlR45Pexs
> o6NZLVr1xV7PUqVKWeEyEDRolYHovSYU6f2TkDUAdDI6O/a8FYN1aDFNAR>
> +ANF0pkhKnTponuhEWuOFl3/lqh4PPYPFrZg3/fktLV2EEjrmqvulStYRn3wT+jBj91aWnt
> +kv/BXCAFawZ/drMXscw1R3Jr/L7OIMX1YMnBc+pGzbkwI2nKwLOaLuYCcj
> XUFGrPLmLpn269HXh+a+zw1ycBGvVdVhp573I/X6NjFSTMzOki
> +qGSuQgEaZkyPsy3DdXubx8HL9UI7PeOkKXgUlC5kioFDFL+LDBU3LzU6r655SPDuzqUj
> 1CeZMB carlherrmann@dkfz-vpn43.inet.dkfz-heidelberg.de

Open the `id_rsa.pub` using a text editor, and copy the content of the file into [this spreadsheet](https://docs.google.com/spreadsheets/d/10_Xo75mFgg80Vs6R9Q4Dhth3INDV21heSdoopFPrr5o/edit?usp=sharing) in one of the  free slots; fill in the additional information (surname,name,...)


### Windows

First, install [Cyberduck](https://cyberduck.io/).

If you are using Windows, you will need to install an additional software to use ssh; we recommend install the [Git for Windows](https://gitforwindows.org/) tools on your computer. Once it is installed, follow the instructions:

1. 