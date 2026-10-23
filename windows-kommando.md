# Windows kommando
https://mentorsparks.com/displayLessonDeck.html?lessonDeck=81358ece-da6a-43b4-b9cb-db645c19f98b&date=2026-08-27&page=1 

## Ta reda på var du är 
Windows organiserar filer per enhet (C:, D:) snarare än en enda rot /. När du arbetar i kommandotolken befinner du dig alltid i en specifik katalog (current working directory).

CMD: cd (utan argument) visar nuvarande katalog. cd /d C:\ byter enhet och katalog
PowerShell: Get-Location (alias pwd) visar nuvarande plats. Set-Location (alias cd) byter katalog.

**CMD**
cd
cd %USERPROFILE%

**PowerShell**
Get-Location
Set-Location $HOME

## Lista innehåll i kataloger
CMD: dir listar filer och kataloger. dir /a visar även dolda, dir /w bred vy.
PowerShell: Get-ChildItem (alias ls, dir, gci) listar. Get-ChildItem -Hidden visar dolda. 

**CMD**
dir
dir /w C:\Windows

**Powershell**
Get-ChildItem
Get-ChildItem -Hidden

## Byta enhet och katalog
I CMD byter man enhet med t.ex. D: och katalog med cd. PowerShell hanterar enheter som namnområden (C:, D:) och du kan cd direkt.

    cd .. går upp ett steg (samma som Linux).
    cd \ går till enhetens rot.

**CMD**
cd \
cd Windows\System32
cd ..

**Powershell**
Set-Location C:\
Set-Location C:\Windows\System32
Set-Location .. 

## Trädstruktur
tree visar katalogstrukturen grafiskt i både CMD och PowerShell. tree /f visar även filer. 

**CMD**
mkdir projekt\alfa\beta
tree projekt

**Powershell**
New-Item -ItemType Directory -Path projekt\alfa\beta -Force
tree projekt

## Skapa filer
CMD: echo text > fil.txt skapar en fil med innehåll. type fil.txt visar innehåll.
PowerShell: New-Item -ItemType File skapar fil. Get-Content (alias cat) visar innehåll. Set-Content skriver.

**CMD**
echo Hej > a.txt
type a.txt

**Powershell**
New-Item -ItemType File -Name b.txt
Get-Content b.txt

## Skapa kataloger
mkdir (eller md) skapar kataloger i både CMD och PowerShell. PowerShells mkdir klarar mkdir -p-liknande beteende automatiskt för djupa sökvägar.

**CMD**
mkdir bilder
mkdir backup\2026\08

**Powershell**
New-Item -ItemType Directory -Path bilder
New-Item -ItemType Directory -Path backup\2026\08 -Force

## Kopiera filer och kataloger
CMD: copy kopierar filer. xcopy kopierar kataloger (med /E för underkataloger). robocopy är det kraftfulla moderna verktyget.
PowerShell: Copy-Item (alias cp) med -Recurse för kataloger.

**CMD**
copy a.txt a_kopia.txt
xcopy backup backup2 /E /I

**Powershell**
Copy-Item a.txt a_kopia.txt
Copy-Item backup backup2 -Recurse

## Flytta och döpa om
CMD: move flyttar, ren (rename) byter namn.
PowerShell: Move-Item flyttar/byter namn.

**CMD**
ren b.txt b_omdöpt.txt
move a_kopia.txt bilder\

**Powershell**
Move-Item b.txt b_omdöpt.txt
Move-Item a_kopia.txt bilder\

## Ta bort filer och kataloger
CMD: del tar bort filer. rmdir (eller rd) tar bort kataloger, /S rekursivt, /Q utan bekräftelse.
PowerShell: Remove-Item (alias rm, del) tar bort både filer och kataloger med -Recurse -Force.

**CMD**
del a.txt
rmdir /S /Q backup2

**Powershell**
Remove-Item a.txt
Remove-Item backup2 -Recurse -Force

## Visa innehåll
CMD: type fil.txt skriver ut hela filen. more bläddrar.
PowerShell: Get-Content (alias cat). Select-Object -First 5 motsvarar head, -Last 3 motsvarar tail. Get-Content -Wait följer en fil (likt tail -f).

**CMD**
type b_omdöpt.txt

**Powershell**
Get-Content C:\Windows\WindowsUpdate.log -First 5
Get-Content C:\Windows\WindowsUpdate.log -Last 3

## Redigera filer
notepad fil.txt öppnar Anteckningar (GUI). Det går också att skriva direkt från PowerShell med Set-Content eller Add-Content.

**CMD**
notepad notes.txt

**Powershell**
notepad notes.txt
Add-Content notes.txt "Detta är en övning"

## Söka efter filer
CMD: dir /s /b namn söker rekursivt. where hittar program i PATH.
PowerShell: Get-ChildItem -Recurse -Filter "*.txt" eller Get-ChildItem -Path C:\ -Filter "*.log" -Recurse -ErrorAction SilentlyContinue.

**CMD**
where notepad.exe

**Powershell**
Get-ChildItem -Path $HOME -Filter "*.txt" -Recurse

## Söka innehåll 
CMD: findstr söker mönster i textfiler. findstr /i "error" fil.txt är skiftlägesokänsligt.
PowerShell: Select-String (alias sls) motsvarar grep. -Pattern, -CaseSensitive.

**CMD**
findstr /i "error" C:\Windows\WindowsUpdate.log

**Powershell**
Select-String -Path C:\Windows\WindowsUpdate.log -Pattern "windows" -CaseSensitive:$false

## Förstå rättigheter
Windows använder ACL (Access Control Lists) istället för Linux chmod. icacls fil visar vilka användare/grupper som har vilka rättigheter (F=full, M=modify, RX=läsa+exekvera, R=läsa, W=skriva).

**CMD**
echo data > rätt.txt
icacls rätt.txt

**Powershell**
New-Item -ItemType File -Name rätt.txt
icacls rätt.txt

## Ge rättigheter
icacls fil /grant Användare:R ger läsrätt. :F ger full kontroll, :RX läsa+exekvera, :W skriva. (OI)(CI) ärver till filer/mappar.

**CMD**
icacls rätt.txt /grant Everyone:R
icacls rätt.txt

**Powershell**
icacls rätt.txt /grant Everyone:R

## Ta bort rättigheter
icacls fil /deny Användare:R nekar rättighet (prioriteras före grant). icacls fil /remove Användare tar helt bort posten.

**CMD**
icacls rätt.txt /remove Everyone
icacls rätt.txt

**Powershell**
PowerShell hanterar rättigheter objektorienterat. Get-Acl hämtar åtkomstkontrollen, du modifierar den och skriver tillbaka med Set-Acl.

$acl = Get-Acl rätt.txt
$acl.Access

## Ägarskap
takeown /f fil tar över ägarskap av en fil (användbart när du låst ute dig själv). Kräver ofta administratör. Därefter kan du sätta rättigheter med icacls /grant.

**CMD som admin**
echo test > låst.txt
takeown /f låst.txt
icacls låst.txt /grant %USERNAME%:F

## Kör som administratör
Vissa kommandon kräver elevation (administratörsrättigheter). I CMD använder man "Kör som administratör", eller runas /user:ADMIN kommando. I PowerShell kan du starta en ny session elevate med högerklick → "Kör som administratör".

**CMD**
net session
runas /user:ADMINISTRATOR "powershell.exe"

## Diskutrymme
CMD: wmic logicaldisk get size,freespace,caption visar diskar. fsutil volume diskfree C: visar fritt utrymme.
PowerShell: Get-Volume eller Get-PSDrive C visar utrymme.

**CMD**
fsutil volume diskfree C:

**Powershell**
Get-Volume
Get-PSDrive C

## Processer
CMD: tasklist listar processer. tasklist | findstr namn filtrerar.
PowerShell: Get-Process (alias ps likt Linux!) listar. Get-Process -Name explorer.

**CMD**
tasklist
tasklist | findstr explorer

**Powershell**
Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 5

## Avsluta processer
CMD: taskkill /IM namn.exe avslutar på namn. taskkill /PID 1234 på PID. /F tvingar.
PowerShell: Stop-Process -Name namn eller -Id PID. -Force tvingar.

**CMD**
start notepad.exe
tasklist | findstr notepad
taskkill /IM notepad.exe /F

**Powershell**
Start-Process notepad
Stop-Process -Name notepad -Force

## Nätverk
ipconfig visar IP-adresser, gateway och DNS (motsvarar Linux ip a). ipconfig /all ger full info. ping testar anslutning (fungerar likadant som Linux).

**CMD**
ipconfig
ping localhost -n 4

**Powershell**
ipconfig
Test-Connection -ComputerName localhost -Count 4

## Lyssna på portar
netstat -an visar alla anslutningar och lyssnande portar. netstat -ano lägger till PID. findstr :80 filtrerar port 80. (PowerShell: Get-NetTCPConnection.)

**CMD**
netstat -ano | findstr LISTENING
netstat -ano | findstr :445

**Powershell**
Get-NetTCPConnection -State Listen

## DNS och nätverksfelsökning
ipconfig /flushdns tömmer DNS-cache (används vid webbläsarproblem). nslookup slår upp domännamn. tracert spårar vägen till en värd.

**CMD**
ipconfig /flushdns
nslookup example.com
tracert -h 3 8.8.8.8

## Systeminformation
systeminfo ger OS-version, uppdateringar, nätverk med mera i textform. msinfo32 öppnar ett GUI. PowerShell: Get-ComputerInfo.

**CMD**
systeminfo

**Powershell**
Get-ComputerInfo | Select-Object OsName, OsVersion, CsUserName

## Händelseloggar
Windows loggar händelser i Event Viewer. wevtutil qe System /c:10 visar 10 System-händelser (CMD). PowerShell: Get-EventLog -LogName System -Newest 10 eller Get-WinEvent.

**Powershell**
Get-EventLog -LogName System -Newest 10
Get-EventLog -LogName Application -EntryType Error -Newest 10

## Tjänster 
CMD: sc query listar tjänster. net start / net stop startar/stoppar.
PowerShell: Get-Service listar. Start-Service, Stop-Service, Restart-Service.

**CMD**
sc query spooler

**Powershell**
Get-Service -Name *spooler*

## Användare och grupper
net user listar användare. net user namn visar detaljer. net localgroup hanterar grupper. PowerShell: Get-LocalUser, Get-LocalGroup. 

**CMD**
net user

**Powershell**
Get-LocalUser
Get-LocalUser -Name $env:USERNAME

## Miljövariabler
CMD: set listar alla miljövariabler. %VARIABEL% hämtar värde. set PATH visar PATH.
PowerShell: $env: är drivrutinen, t.ex. $env:USERPROFILE, $env:PATH.

**CMD**
set
echo %USERPROFILE%

**Powershell**
Get-ChildItem Env:
$env:USERPROFILE
$env:PATH

## Alias och kommandohistorik
CMD har inga beständiga alias men du kan göra doskey ll=dir. PowerShell har Set-Alias ll Get-ChildItem. Historik: F7 i CMD, Get-History i PowerShell, Ctrl+R i båda för sök.

**Powershell**
Set-Alias ll Get-ChildItem
Get-History | Select-Object -Last 10

# Scenario
Scenario 1: Städa upp en nedladdningsmapp

Teori & Koncept
Kombinera kommandon för att hitta och ta bort gamla filer. PowerShells Where-Object + Remove-Item är kraftfullt.

Övning / Uppgift

    I PowerShell, lista .tmp-filer i din Downloads som är äldre än 7 dagar.
    Ta bort dem (simulera först med -WhatIf).

Förväntat svar / Kommandon (PowerShell)

$gräns = (Get-Date).AddDays(-7)
Get-ChildItem $env:USERPROFILE\Downloads -Filter "*.tmp" | Where-Object { $_.LastWriteTime -lt $gräns }

Scenario 2: Säkra en konfigurationsfil

Teori & Koncept
Konfigurationsfiler ska ofta bara nås av ägaren. I Windows gör du detta med icacls genom att ta bort arv och ge en specifik användare rättighet.

Övning / Uppgift

    Skapa config.conf med lite innehåll.
    Ta bort ärvda rättigheter och ge bara dig själv full kontroll.

Förväntat svar / Kommandon (CMD)

echo port=8080 > config.conf
icacls config.conf /inheritance:r
icacls config.conf /grant %USERNAME%:F
icacls config.conf

Scenario 3: Hitta vad som fyller disken

Teori & Koncept
Använd rekursiv storleksberäkning för att hitta tunga kataloger.

Övning / Uppgift

    I PowerShell, hitta de 10 största katalogerna under C:\Program Files.

Förväntat svar / Kommandon (PowerShell)

Get-ChildItem "C:\Program Files" -Directory | ForEach-Object {
  $size = (Get-ChildItem $_.FullName -Recurse -File -ErrorAction SilentlyContinue | Measure-Object -Property Length -Sum).Sum
  [PSCustomObject]@{ Namn = $_.Name; Storlek_MB = [math]::Round($size/1MB, 2) }
} | Sort-Object Storlek_MB -Descending | Select-Object -First 10
