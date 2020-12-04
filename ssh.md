## Acessing the remote server

You will connect to the remote server using the **ssh tool**. In order to use this tool, you will first ned to generate a pair of ssh keys, consisting of a *public* key and a *private* key!

The exact procedure depends on whether you are using macOS/Linux or Windows.

******

### MacOS / Linux

If you are using macOS or Linux, you have a native terminal environment, with all required commands, like `ssh`.

First, you need to generate a pair of ssh-keys:

1. open a terminal (the black window)

2. type the following command: `ssh-keygen`

3. press return at all the questions

This will generate a pair of **ssh keys**, composed of a *private key* and a *public key*. 

If you have pressed return at all questions, this command will have generated new files in the `.ssh` directory in you home folder; inside this directory, you will find 2 files:

* a file `id_rsa` : this contains your *private key* , and **should not be disclosed** !
* a file `id_rsa.pub` : this contains your *public key*, which should start with `ssh-rsa ...` followed by a very long string, for example
> ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDO7JOEqg9Zuar9MmlR45Pexs
> o6NZLVr1xV7PUqVKWeEyEDRolYHovSYU6f2TkDUAdDI6O/a8FYN1aDFNAR>
> +ANF0pkhKnTponuhEWuOFl3/lqh4PPYPFrZg3/fktLV2EEjrmqvulStYRn3wT+jBj91aWnt
> +kv/BXCAFawZ/drMXscw1R3Jr/L7OIMX1YMnBc+pGzbkwI2nKwLOaLuYCcj
> XUFGrPLmLpn269HXh+a+zw1ycBGvVdVhp573I/X6NjFSTMzOki
> +qGSuQgEaZkyPsy3DdXubx8HL9UI7PeOkKXgUlC5kioFDFL+LDBU3LzU6r655SPDuzqUj
> 1CeZMB carlherrmann@dkfz-vpn43.inet.dkfz-heidelberg.de

4. Open the `id_rsa.pub` using a text editor, and copy the content of the file into [this spreadsheet](https://docs.google.com/spreadsheets/d/10_Xo75mFgg80Vs6R9Q4Dhth3INDV21heSdoopFPrr5o/edit?usp=sharing) in one of the  free slots; fill in the additional information (surname,name,...)


Once this is done, we will copy your public key to the server, and inform you once it is done. Once you get notified by us, you can connect to the server using the following command:

1. Type `ssh -i .ssh/id_rsa user1@134.176.27.78 -p 30127` (RETURN) *(WARNING: replace `user1` with the user name in the column **Account** in the Google Sheet of the row you selected!!)*

2. Type `yes` if a question appears. 

3. You should now be connected to the server: check if you see something like `user1@powerfulrutherford` in the command line. If so, you are connected to the Virtual machine `powerfulRutherford`!

4. *Good to go!*

******

### Windows


If you are using Windows, you will need to install an additional software to use ssh; we recommend install the [Git for Windows](https://gitforwindows.org/) tools on your computer. Once it is installed, follow the instructions:

1. Open the program **Git Bash**; a black window should appear
2. type the following command: 

 > `ssh-keygen.exe`

3. Press RETURN at each question
4. One this is done, you should have 2 files inside the `.ssh` folder in your home folder
5. These 2 files might appear to have the same name (`id_rsa`), however, one should have the ending `.pub` (public key), the other has no ending (private key) *(If you do not see the ending `.pub` in the filename. do to `Ansicht` or `View`, then tick `Dateinamenerweiterung` or `File name extension`; now you see the ending in one of the files!)*
6. Open the `id_rsa.pub` file with a text editor *(Warning! not with Word, but for example with Notepad)*
7. The file should contain a long string starting with  `ssh-rsa....` (see above).
8. Copy this string into [this spreadsheet](https://docs.google.com/spreadsheets/d/10_Xo75mFgg80Vs6R9Q4Dhth3INDV21heSdoopFPrr5o/edit?usp=sharing) in one of the  free slots; fill in the additional information (surname,name,...)

We will then copy your public key onto your account on the server, and inform you once this is done. Once you get an email from us, you will be able to connect to the server using the following procedure:

1. Open again a Git Bash window (Go to the list of programs)
2. Type `cd` (then RETURN)
3. Type the following command:
`ssh -i .ssh/id_rsa user1@134.176.27.78 -p 30127` (RETURN) *(WARNING: replace `user1` with the user name in the column **Account** in the Google Sheet of the row you selected!!)*
4. Type `yes` if a question appears.
5. You should now be connected to the server: check if you see something like `user1@powerfulrutherford` in the command line. If so, you are connected to the Virtual machine `powerfulRutherford`!
6. *Good to go!*