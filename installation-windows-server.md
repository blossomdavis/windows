# Installera och sätt upp Windows Server 2025 som Domänkontrollant (DC), med ADDS 

Övningen kommer att göras som en labb i klass. Alla ska göra konfigurationen individuellt, likt Ubuntu installationen tidigare i kursen.
Exempel på konfiguration av:
Active Directory: Domain ServicesLinks to an external site.
 

Mål: Målet är att ni ska få upp en fungerande virtuell Windows Server 2025 i er labbmiljö, konfigurerad med fast IP, befordrad till Domänkontrollant (DC) samt installerad med Active Directory Domain Services (AD DS) och DNS. Denna server kommer att fungera som central identitets- och domänhanterare för framtida labbar.


Del 1: Installera Windows Server & initial start
Virtuella maskinens specifikationer:

Ladda ner en Windows Server 2025 ISO-image för vår framtida domänkontrollant (Evaluation ISO från Microsoft Evaluation CenterLinks to an external site.).

Hårdvaruspecifikation:

    Nätverk: 1 st Nätverkskort, NAT Network (samma nätverk som i övriga labbar: 192.168.50.0/24).

    Säkerställ UEFI-användning innan start: Välj UEFI (istället för Legacy BIOS) i inställningarna för den virtuella maskinen (VM-inställningarna) samt aktivera TPM 2.0 om din hypervisor kräver det för Windows Server 2025.


Steg 1: Skapa VM, installera & initial Start

Skapa VM & installera Windows Server

    Skapa en ny VM i VirtualBox och döp den till t.ex. srv-dc01.

    Resurser: Tilldela minst 4 GB RAM (gärna 8 GB om resurser finns) och 2 vCPUs.

    Lagring: Skapa en virtuell hårddisk på minst 50 GB.

    UEFI: Gå till Settings > System > Motherboard och bocka i Enable UEFI.

    Nätverk: Gå till Settings > Network > Adapter 1, välj NAT Network och anslut till ditt labbnätverk (t.ex. 192.168.50.0/24).

    Starta den virtuella maskinen från ISO-filen för Windows Server 2025.

    Välj Language to install: English (United States) och Keyboard: Swedish.
    Välj installationstyp: Windows Server 2025 Datacenter Evaluation (Desktop Experience) eller Standard Evaluation (Desktop Experience).

    På Diskpartitionering & Installation, älj Custom: Install Windows only (advanced).

    Markera det oallokerade utrymmet (Unallocated Space) och klicka på Next (Windows installerar automatiskt krävd GPT- och EFI-systempartition).

    Slutför installationen och ange ett lösenord för det lokala Administrator-kontot när du uppmanas. (I en riktig miljö ska lösenordet såklart vara ett starkt lösenord enligt policy.)



Initial start 

Innan en server befordras till domänkontrollant måste den ha ett korrekt datornamn och en fast IP-adress.

    Starta om/starta din VM och logga in som lokal Administrator.

    Öppna Server Manager (eller kör ncpa.cpl via Win + R).

    Gå till Ethernet-egenskaper > Internet Protocol Version 4 (TCP/IPv4) och ange följande:

        IP address: 192.168.50.100

        Subnet mask: 255.255.255.0

        Default gateway: 192.168.50.1

        Preferred DNS server: 127.0.0.1 (eller 192.168.50.100 – peka på sig själv då servern ska bli DNS-server)

        Alternate DNS server: 1.1.1.1

    Byt namn på servern:

        I Server Manager > Local Server, klicka på nuvarande Computer Name.

        Ändra namnet till t.ex. SRV-DC01.

        Starta om servern för att namnändringen ska träda i kraft.


Del 2: Gör till domänkontrollant och installera ADDS
Installation av AD DS

Installera Active Directory: Domain Services

    Öppna Server Manager efter omstarten.

    Klicka på Manage > Add Roles and Features.

    Klicka Next fram till Server Roles.

    Markera Active Directory Domain Services. Godkänn tillhörande verktyg (Management Tools) i pop-up rutan och klicka på Add Features.

    Säkerställ att DNS Server också är markerat (om det inte väljs automatiskt i nästa steg).

    Stega vidare med Next och klicka på Install. Vänta tills installationen av rollfilerna är klar.


Gör till Domänkontrollant (DC)

    När rollinstallationen är färdig, klicka på den gula varningstriangeln högst upp i Server Manager och välj Promote this server to a domain controller.

    I guiden för domänkonfiguration:

        Välj Add a new forest.

        Root domain name: Ange ditt domännamn, t.ex. chaslab.local .

    Domain Controller Options:

        Behåll Functional Levels på högsta nivå (Windows Server 2025 / Server 2025).

        Säkerställ att Domain Name System (DNS) server och Global Catalog (GC) är ikryssade.

        Ange ett starkt lösenord för Directory Services Restore Mode (DSRM).

    Stega vidare genom DNS Options, Additional Options (NetBIOS-namn genereras automatiskt, t.ex. CHASLAB) och Paths (standard sökvägar för NTDS-databas, loggar och SYSVOL).

    Klicka på Install efter att förutsättningskontrollen (Prerequisites Check) har godkänts.

    Servern kommer automatiskt att starta om när konfigureringen av Active Directory är klar.


Verifiera konfigurationer

    Verifiera Active Directory-verktyg:

        Öppna Server Manager > Tools.

        Kontrollera att Active Directory Users and Computers (ADUC) och DNS finns tillgängliga och går att öppna.

    Verifiera via Kommandotolken/PowerShell:

    Öppna PowerShell och kör följande kommandon för att verifiera hälsan på AD och DNS:

    # Kontrollera IP-inställningar
    Get-NetIPConfiguration

    # Verifiera Active Directory-tjänsten och DNS-poster
    dcdiag /v

    Kontrollera i DNS Manager under Forward Lookup Zones att din domän (t.ex. chaslab.local) finns skapad och innehåller SRV-poster (_msdcs).



Skapa Snapshot i VirtualBox 
Skapa en snapshot när du har en fungerande Domänkontrollant med Domain Services, med verifierade konfigurationer.

För att ta ett snapshot:
1. Stäng av din VM
2. Markera din VM i "Hem vyn" i VirtualBox, där dina VM:s är listade.
3. Välj fliken "Snapshots" och högerklicka på "Current state" och vält "Take".