meterpreter > help

Metasploit has two versions of Mimikatz available as Meterpreter extensions: 
version 1.0 by loading the mimikatz extension, and the newer version 2.x by loading the kiwi extension.
                                                                       
Core Commands                                                                                    
=============                                                                             
                                                                  
    Command                   Description                      
    -------                   -----------                                 
    ?                         Help menu                           
    background                Backgrounds the current session       
    bgkill                    Kills a background meterpreter script                    
    bglist                    Lists running background scripts      
    bgrun                     Executes a meterpreter script as a background thread
    channel                   Displays information or control active channels
    close                     Closes a channel                                    
    disable_unicode_encoding  Disables encoding of unicode strings
    enable_unicode_encoding   Enables encoding of unicode strings     
    exit                      Terminate the meterpreter session          
    get_timeouts              Get the current session timeout values
    guid                      Get the session GUID        
    help                      Help menu                                                     
    info                      Displays information about a Post module
    irb                       Drop into irb scripting mode                                            
    load                      Load one or more meterpreter extensions
    machine_id                Get the MSF ID of the machine attached to the session
    migrate                   Migrate the server to another process    
    pivot                     Manage pivot listeners                                             
    quit                      Terminate the meterpreter session                           
    read                      Reads data from a channel           
    resource                  Run the commands stored in a file               
    run                       Executes a meterpreter script or Post module
    sessions                  Quickly switch to another session
    set_timeouts              Set the current session timeout values
    sleep                     Force Meterpreter to go quiet, then re-establish session.
    transport                 Change the current transport mechanism
    use                       Deprecated alias for "load"
    uuid                      Get the UUID for the current session
    write                     Writes data to a channel


Stdapi: File system Commands
============================

    Command       Description
    -------       -----------
    cat           Read the contents of a file to the screen
    cd            Change directory
    checksum      Retrieve the checksum of a file
    cp            Copy source to destination
    dir           List files (alias for ls)
    download      Download a file or directory
    edit          Edit a file
    getlwd        Print local working directory
    getwd         Print working directory
    lcd           Change local working directory
    lls           List local files
    lpwd          Print local working directory
    ls            List files
    mkdir         Make directory
    mv            Move source to destination
    pwd           Print working directory
    rm            Delete the specified file
    rmdir         Remove directory
    search        Search for files
    show_mount    List all mount points/logical drives
    upload        Upload a file or directory


Stdapi: Networking Commands
===========================

    Command       Description
    -------       -----------
    arp           Display the host ARP cache
    getproxy      Display the current proxy configuration
    ifconfig      Display interfaces
    ipconfig      Display interfaces
    netstat       Display the network connections
    portfwd       Forward a local port to a remote service
    resolve       Resolve a set of host names on the target
    route         View and modify the routing table


Stdapi: System Commands                                                                                     
=======================

    Command       Description
    -------       -----------
    clearev       Clear the event log
    drop_token    Relinquishes any active impersonation token.
    execute       Execute a command
    getenv        Get one or more environment variable values
    getpid        Get the current process identifier
    getprivs      Attempt to enable all privileges available to the current process
    getsid        Get the SID of the user that the server is running as
    getuid        Get the user that the server is running as
    kill          Terminate a process
    localtime     Displays the target system's local date and time
    pgrep         Filter processes by name
    pkill         Terminate processes by name
    ps            List running processes
    reboot        Reboots the remote computer
    reg           Modify and interact with the remote registry
    rev2self      Calls RevertToSelf() on the remote machine
    shell         Drop into a system command shell
    shutdown      Shuts down the remote computer
    steal_token   Attempts to steal an impersonation token from the target process
    suspend       Suspends or resumes a list of processes
    sysinfo       Gets information about the remote system, such as OS


Stdapi: User interface Commands
===============================

    Command        Description
    -------        -----------
    enumdesktops   List all accessible desktops and window stations
    getdesktop     Get the current meterpreter desktop
    idletime       Returns the number of seconds the remote user has been idle
    keyscan_dump   Dump the keystroke buffer
    keyscan_start  Start capturing keystrokes
    keyscan_stop   Stop capturing keystrokes
    screenshot     Grab a screenshot of the interactive desktop
    setdesktop     Change the meterpreters current desktop
    uictl          Control some of the user interface components


Stdapi: Webcam Commands
=======================

    Command        Description
    -------        -----------
    record_mic     Record audio from the default microphone for X seconds
    webcam_chat    Start a video chat
    webcam_list    List webcams
    webcam_snap    Take a snapshot from the specified webcam
    webcam_stream  Play a video stream from the specified webcam


Stdapi: Audio Output Commands
=============================

    Command       Description
    -------       -----------
    play          play an audio file on target system, nothing written on disk

Priv: Elevate Commands
======================
                 
    Command       Description
    -------       -----------    
    getsystem     Attempt to elevate your privilege to that of local system.
                                                        
                                                       
Priv: Password database Commands          
================================                            
                                                   
    Command       Description                        
    -------       -----------                          
    hashdump      Dumps the contents of the SAM database
             

Priv: Timestomp Commands
========================

    Command       Description
    -------       -----------
    timestomp     Manipulate file MACE attributes

==============================================================================================================
=====================================  Load meterpreter extensions  ==========================================
==============================================================================================================

========================
===== load espia =======
========================
Espia Commands                                                         
==============
                             
    Command       Description        
    -------       -----------                                               
    screengrab    Attempt to grab screen shot from process's active desktop

========================
===== load extapi ======
========================
Extapi: Window Management Commands                                                     
==================================                               
                                                                
    Command       Description                                                         
    -------       -----------                                    
    window_enum   Enumerate all current open windows     
                                                        
                                                                  
Extapi: Service Management Commands
===================================
                                
    Command          Description
    -------          -----------                 
    service_control  Control a single service (start/pause/resume/stop/restart)
    service_enum     Enumerate all registered Windows services
    service_query    Query more detail about a specific Windows service    


Extapi: Clipboard Management Commands                                         
=====================================                                                                                                                                                         
                                                                             
    Command                   Description
    -------                   -----------
    clipboard_get_data        Read the target's current clipboard (text, files, images)
    clipboard_monitor_dump    Dump all captured clipboard content
    clipboard_monitor_pause   Pause the active clipboard monitor
    clipboard_monitor_purge   Delete all captured clipboard content without dumping it
    clipboard_monitor_resume  Resume the paused clipboard monitor
    clipboard_monitor_start   Start the clipboard monitor           
    clipboard_monitor_stop    Stop the clipboard monitor
    clipboard_set_text        Write text to the target's clipboard


Extapi: ADSI Management Commands
================================

    Command                      Description
    -------                      -----------
    adsi_computer_enum           Enumerate all computers on the specified domain.
    adsi_dc_enum                 Enumerate all domain controllers on the specified domain.
    adsi_domain_query            Enumerate all objects on the specified domain that match a filter.
    adsi_group_enum              Enumerate all groups on the specified domain.
    adsi_nested_group_user_enum  Recursively enumerate users who are effectively members of the group specified.
    adsi_user_enum               Enumerate all users on the specified domain.


Extapi: WMI Querying Commands
=============================

    Command       Description
    -------       -----------
    wmi_query     Perform a generic WMI query and return the results

========================
==== load mimikatz =====
======================== 
Mimikatz Commands
=================

    Command           Description
    -------           -----------
    kerberos          Attempt to retrieve kerberos creds
    livessp           Attempt to retrieve livessp creds
    mimikatz_command  Run a custom command
    msv               Attempt to retrieve msv creds (hashes)
    ssp               Attempt to retrieve ssp creds
    tspkg             Attempt to retrieve tspkg creds
    wdigest           Attempt to retrieve wdigest creds


========================
==== load incognito ====
======================== 
Incognito Commands
==================

    Command              Description
    -------              -----------
    add_group_user       Attempt to add a user to a global group with all tokens
    add_localgroup_user  Attempt to add a user to a local group with all tokens
    add_user             Attempt to add a user with all tokens
    impersonate_token    Impersonate specified token
    list_tokens          List tokens available under current user context
    snarf_hashes         Snarf challenge/response hashes for every token

========================
====== load kiwi =======
========================
Kiwi Commands
=============

    Command                Description
    -------                -----------
    creds_all              Retrieve all credentials (parsed)
    creds_kerberos         Retrieve Kerberos creds (parsed)
    creds_msv              Retrieve LM/NTLM creds (parsed)
    creds_ssp              Retrieve SSP creds
    creds_tspkg            Retrieve TsPkg creds (parsed)
    creds_wdigest          Retrieve WDigest creds (parsed)
    dcsync                 Retrieve user account information via DCSync (unparsed)
    dcsync_ntlm            Retrieve user account NTLM hash, SID and RID via DCSync
    golden_ticket_create   Create a golden kerberos ticket
    kerberos_ticket_list   List all kerberos tickets (unparsed)
    kerberos_ticket_purge  Purge any in-use kerberos tickets
    kerberos_ticket_use    Use a kerberos ticket
    kiwi_cmd               Execute an arbitary mimikatz command (unparsed)
    lsa_dump_sam           Dump LSA SAM (unparsed)
    lsa_dump_secrets       Dump LSA secrets (unparsed)
    password_change        Change the password/hash of a user
    wifi_list              List wifi profiles/creds for the current user
    wifi_list_shared       List shared wifi profiles/creds (requires SYSTEM)
    
    kiwi_cmd
    =============
        Command
        ------- 
        standard
        crypto
        sekurlsa
        dpapi
        sysenv
        sid
        iis
        rpc
        
        Module :  standard - Basic commands (does not require module name)
            exit  -  Quit mimikatz
             cls  -  Clear screen (doesn't work with redirections, like PsExec)
          answer  -  Answer to the Ultimate Question of Life, the Universe, and Everything
          coffee  -  Please, make me a coffee!
           sleep  -  Sleep an amount of milliseconds
             log  -  Log mimikatz input/output to file
          base64  -  Switch file input/output base64
         version  -  Display some version informations
              cd  -  Change or display current directory
       localtime  -  Displays system local date and time (OJ command)
        hostname  -  Displays system local hostname

        Module :  dpapi - Data Protection application programming interface
            blob  -  Describe a DPAPI blob, unprotect it with API or Masterkey
         protect  -  Protect a data via a DPAPI call
       masterkey  -  Describe a Masterkey file, unprotect each Masterkey (key depending)
        credhist  -  Describe a Credhist file
            capi  -  CAPI key test
             cng  -  CNG key test
            cred  -  CRED test
           vault  -  VAULT test
            wifi  -  WiFi test
            wwan  -  Wwan test
          chrome  -  Chrome test
             ssh  -  SSH Agent registry cache
             rdg  -  RDG saved passwords
              ps  -  PowerShell credentials (PSCredentials or SecureString)
              
        Module :  sekurlsa - Some commands to enumerate credentials...
             msv  -  Lists LM & NTLM credentials
         wdigest  -  Lists WDigest credentials
        kerberos  -  Lists Kerberos credentials
           tspkg  -  Lists TsPkg credentials
         livessp  -  Lists LiveSSP credentials
             ssp  -  Lists SSP credentials
  logonPasswords  -  Lists all available providers credentials
         process  -  Switch (or reinit) to LSASS process  context
        minidump  -  Switch (or reinit) to LSASS minidump context
             pth  -  Pass-the-hash
          krbtgt  -  krbtgt!
     dpapisystem  -  DPAPI_SYSTEM secret
           trust  -  Antisocial
      backupkeys  -  Preferred Backup Master keys
         tickets  -  List Kerberos tickets
           ekeys  -  List Kerberos Encryption Keys
           dpapi  -  List Cached MasterKeys
         credman  -  List Credentials Manager

========================
==== load lanattacks ===
========================

Lanattacks: DHCP Commands
=========================

    Command            Description
    -------            -----------
    dhcp_load_options  Load DHCP optionis from a datastore
    dhcp_log           Log DHCP server activity
    dhcp_reset         Reset the DHCP server
    dhcp_set_option    Set a DHCP server option
    dhcp_start         Start the DHCP server
    dhcp_stop          Stop the DHCP server


Lanattacks: TFTP Commands
=========================

    Command        Description
    -------        -----------
    tftp_add_file  Add a file to the TFTP server
    tftp_reset     Reset the TFTP server
    tftp_start     Start the TFTP server
    tftp_stop      Stop the TFTP server


========================
==== load Powershell ===
========================
Powershell Commands
===================

    Command             Description
    -------             -----------
    powershell_execute  Execute a Powershell command string
    powershell_import   Import a PS1 script or .NET Assembly DLL
    powershell_shell    Create an interactive Powershell prompt


========================
====== load python =====
========================
Python Commands
===============

    Command         Description
    -------         -----------
    python_execute  Execute a python command string
    python_import   Import/run a python file or module
    python_reset    Resets/restarts the Python interpreter


========================
===== load sniffer =====
========================
Sniffer Commands
================

    Command             Description
    -------             -----------
    sniffer_dump        Retrieve captured packet data to PCAP file
    sniffer_interfaces  Enumerate all sniffable network interfaces
    sniffer_release     Free captured packets on a specific interface instead of downloading them
    sniffer_start       Start packet capture on a specific interface
    sniffer_stats       View statistics of an active capture
    sniffer_stop        Stop packet capture on a specific interface


========================
===== load winpmem =====
========================
Winpmem Commands
================

    Command       Description
    -------       -----------
    dump_ram      Dump victim RAM