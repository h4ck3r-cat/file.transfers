# Windows
[PowerShell Base64 Encode & Decode](https://github.com/h4ck3r-cat/file.transfers/blob/main/README.md#windows)
## PowerShell Base64 Encode & Decode
- This method does **not** require a network connection</li>

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
If the MD5 hash is the same, we succeeded in trasfering the file.
