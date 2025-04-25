# Sign a FILE using Self-Signed Certificate on Windows 11

A working guide for you, if you find it is hard to figure out all the necessary details on your own.

### 1. If file \*.pfx is missing, run:

makecert_pfx.ps1 (PowerShell script):
```
$MySPC = "qLiu_Github_cert"
$MyPwd = "xyz@gH"

# use the same pwd (xyz@gH), when requested
# CN: name of certificate in GUI of certmgr.msc
makecert -r -pe -n "CN=Qiang Liu CA,E=abc@cornell.edu" -a sha256 -cy end -sky signature -sv "$MySPC.pvk" "$MySPC.cer"

pvk2pfx -pvk "$MySPC.pvk" -spc "$MySPC.cer" -pfx "$MySPC.pfx" -po $MyPwd
```

### 2. Make a self-signed certificate trusted:
> PS > certmgr.msc

Go to:
> Trusted Root Certification Authorities > Certificates

Right-click and select:
> All tasks > Import ...

Follow the wizard to import the self-signed certificate (qLiu_Github_cert.cer).

### 3. use **qLiu_Github_cert.pfx** to sign files; edit and run:
> PS > sign_exe.ps1

sign_exe.ps1:
```
$MyPwd = "xyz@gH"   # same pass as for qLiu_Github_cert.pfx
signtool sign /tr http://timestamp.digicert.com /td sha256 /fd sha256 /f ".\qLiu_Github_cert.pfx" /p $MyPwd "D:\qlWork\myDev\xother\Python\LunarCalendar\qldist\SolarLunarConvert.exe"
```

### Note:
1. For exe and dll in my GitHub packages, you can download **qLiu_Github_cert.pfx** and import into your machine.

2. Check, import, or delete certificate with:
PS > certmgr

3. I was not able to make PowerShell Cmdlet **New-SelfSignedCertificate** for signing an exe, after trying various approaches or examples provided by Microsoft.


