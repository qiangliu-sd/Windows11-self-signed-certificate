# Sign a FILE using Self-Signed Certificate on Windows 11

A working guide for you, if you find it is hard to figure out all the necessary details on your own.

### 1. If file \*.pfx is missing 
Edit and run:
> PS > makecert_pfx.ps1

makecert_pfx.ps1 (PowerShell script):
```
# MODIFY: if you want
$gh_cert = "qLiu_Github_cert"
# define file NAMES 
$ql_pvk = "$gh_cert.pvk"  # = qLiu_Github_cert.pvk
$ql_cer = "$gh_cert.cer"
$ql_pfx = "$gh_cert.pfx"
# password
$pfx_pwd = "abc@xyz"
# CN: name of certificate in GUI of certmgr.msc or file properties
$ql_cert_info = "CN=Qiang Liu Y,E=ql12@cornell.edu"

# provide the SAME pwd, when prompted
makecert -r -pe -a sha256 -cy end -sky signature -n $ql_cert_info -sv $ql_pvk $ql_cer

pvk2pfx -pvk $ql_pvk -spc $ql_cer -pfx $ql_pfx -po $pfx_pwd
```
Three files with types of .cer, .pvk, and .pfx will be created in the current dir.

### 2. Make a self-signed certificate trusted
run:
> PS > certmgr.msc

Go to:
> Trusted Root Certification Authorities > Certificates

Right-click and select:
> All tasks > Import ...

Follow the wizard to import the self-signed certificate (**qLiu_Github_cert.cer**).

### 3. Use qLiu_Github_cert.pfx to sign files
Edit and run:
> PS > sign_exe.ps1

sign_exe.ps1:
```
# MODIFY: file to be digital-signed
$f_2sign = "D:\qlWork\myDev\xother\Python\LunarCalendar\dist\cpp\solarLunar.dll"

#! CONSTANTS
$tstamp_url = "http://timestamp.digicert.com"
$ql_pfx = "qLiu_Github_cert.pfx"
$pfx_pwd = "abc@xyz"  # same pass as for qLiu_Github_cert.pfx

signtool sign /td sha256 /fd sha256 /tr $tstamp_url /f $ql_pfx /p $pfx_pwd $f_2sign
```
The exe file, SolarLunarConvert.exe, will be signed.

### Note:
1. For exe and dll in my GitHub packages, you can download **qLiu_Github_cert.pfx** and import into your machine.

2. Check, import, or delete certificate with:
> PS > certmgr

3. I was not able to make PowerShell Cmdlet **New-SelfSignedCertificate** to sign an exe, after trying various approaches or examples provided by Microsoft.


