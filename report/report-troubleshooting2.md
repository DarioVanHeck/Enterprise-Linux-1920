# Enterprise Linux Lab Report - Troubleshooting

- Student name: Olivier Troch
- Class/group: TIN-TI-3B (Gent)

## Instructions

- Write a detailed report in the "Report" section below (in Dutch or English)
- Use correct Markdown! Use fenced code blocks for commands and their output, terminal transcripts, ...
- The different phases in the bottom-up troubleshooting process are described in their own subsections (heading of level 3, i.e. starting with `###`) with the name of the phase as title.
- Every step is described in detail:
    - describe what is being tested
    - give the command, including options and arguments, needed to execute the test, or the absolute path to the configuration file to be verified
    - give the expected output of the command or content of the configuration file (only the relevant parts are sufficient!)
    - if the actual output is different from the one expected, explain the cause and describe how you fixed this by giving the exact commands or necessary changes to configuration files
- In the section "End result", describe the final state of the service:
    - copy/paste a transcript of running the acceptance tests
    - describe the result of accessing the service from the host system
    - describe any error messages that still remain

## Report

### Phase 1: Network Acces Layer

1. Fysieke netwerkinterfaces?
    via virtualbox kijk in naar de instellingen en zie de twee fysieke netwerkinterfaces.
            
2. Is de netwerkkabel ingeplugd? 
    via virtualbox kijk in naar de instellingen en zie de twee fysieke netwerkinterfaces connected zijn.

### Phase 2: Internet Layer

Ipaddresses zijn toegewezen. Bij Eth1 moet ik wel "on-boot" aanpassen naar **yes** en het DNS meegeven.

### Phase 3: Transport Layer

DNS stond niet specifiek toegewezen als service, deze is toegevoegd via het command:
            firewall-cmd --add-services dns --permanent

### Phase 4: Application Layer
1. Validate config file syntax
    - sudo named-checkconf /etc/named.conf
    Dit geeft de volgende error:
    > **dnssec-lookaside 'auto' is no longer supported**
2. Gebruik client tools
    - e.g. dig, nslookup, etc.

    **configuratie in /etc/named.conf**

    Open de configuratie via het commando:  
    > **vi /etc/named.conf**

    Hier verander ik listen-on port 53 { 127.0.0.1;}; naar   
    listen-on port 53 { any;};

    Op het einde van het bestand pas ik zone   192.168.56.in-addr.arpa aan naar 56.168.192.in-addr.arpa en verander ook zo de file location naar "/var/named/56.168.192.in-addr.arpa.

    Op die locatie zelf pas ik ook de naam aan met het commando: 
    > **mv 192.168.56.in-addr-arpa 56.168.192.in-addr-arpa**

    **Zone File: Reverse Lookup Zone**

    **Zone 192.0.2.0**

            named-checkzone 2.0.192.in-addr.arpa /var/named/2.0.192.in-addr.arpa
    RESULT: OK

    **Zone 192.168.56.0**
        
            named-checkzone 56.168.192.in-addr.arpa /var/named/56.168.192.in-addr.arpa   
    RESULT: NOK

    OPLOSSING: Ik plaats punten achter alle cymalco.com domains.

    **Zone File: Forward Lookup Zone**

        sudo named-checkzone cynalco.com /var/named/cynalco.com
    RESULT: OK

Na deze stappen kan de service gestart worden aan de hand van volgend commando. 

        systemctl start named

LET OP: NAAMGEVING IS VAN UITERSTE BELANG! VOOR CHECKZONE TESTING!

**named.conf**

    - verander "listen-on-port 53" naar {127.0.0.1; 192.168.56.42;}
    - verander "allow query" naar {192.168.56.0/24;}

## End result

4 van de 8 testen waren ok.
- Foward Lookups public servers
- Reverse lookups public servers
- Alias lookups public servers
- Mail servers lookup

## Resources

List all sources of useful information that you encountered while completing this assignment: books, manuals, HOWTO's, blog posts, etc.
