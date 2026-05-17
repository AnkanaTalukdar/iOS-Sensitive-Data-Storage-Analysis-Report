##### **Phase 1: Network Optimization \& Tool Installation**



**Step 1: Stabilize the NAT Network Connection**



* Fix the packet loss and DNS timeouts over your VM's NAT adapter by binding Google DNS directly to your active network profile:

###### *Bash*

sudo nmcli connection modify "netplan-enp0s3" ipv4.dns "8.8.8.8 8.8.4.4"

sudo nmcli connection modify "netplan-enp0s3" ipv4.ignore-auto-dns yes

sudo nmcli connection up "netplan-enp0s3"



*"Verify with ping -c 3 8.8.8.8 to ensure 0% packet loss."*



**Step 2: Install Local Storage Utilities**



* Install the core Linux packages required to parse and read iOS local data structures:

###### *Bash*

sudo apt update

sudo apt install -y libimobiledevice-utils libplist-utils sqlite3



##### **Phase 2: Building the iOS Sandbox Workspace**



**Step 3: Initialize the Directory Structure**



* Create a dedicated workspace mimicking the file layout of an extracted iOS .ipa package:

###### *Bash*

mkdir -p \~/ios\_pentesting/TargetApp.app

mkdir -p \~/ios\_pentesting/AppData/Documents

mkdir -p \~/ios\_pentesting/AppData/Library/Preferences

cd \~/ios\_pentesting



##### **Phase 3: Practical Pentesting Execution**



**Step 4: Audit the Local Database (.sqlite)**



* Create and populate the unencrypted SQLite database in a single command string:

###### *Bash*

sqlite3 AppData/Documents/user\_session.db "CREATE TABLE user\_credentials (id INTEGER PRIMARY KEY, username TEXT, session\_token TEXT, is\_admin INTEGER); INSERT INTO user\_credentials (username, session\_token, is\_admin) VALUES ('student\_admin', 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9', 1); INSERT INTO user\_credentials (username, session\_token, is\_admin) VALUES ('john\_doe', 'auth\_ticket\_89432a76fbc9e821', 0);"



* Run the audit command to prove data exposure:

###### *Bash*

sqlite3 AppData/Documents/user\_session.db "SELECT \* FROM user\_credentials;"



**Step 5: Audit Application Preferences (.plist)**



* Open the text editor to create the configuration file:

###### *Bash*

nano AppData/Library/Preferences/com.targetapp.config.plist

###### *Paste this exact XML block inside Nano, then save and exit (Ctrl+O, Enter, Ctrl+X):*

code.py

* Compile the file into an authentic iOS binary property list:

###### *Bash*

plistutil -i AppData/Library/Preferences/com.targetapp.config.plist -o AppData/Library/Preferences/com.targetapp.config.bin.plist



* Run the pentesting audit tool to reverse and read the binary data:

###### *Bash*

plistutil -i AppData/Library/Preferences/com.targetapp.config.bin.plist



**Step 6: Audit the Application Binary (Keychain Flags)**



* Simulate a compiled application binary containing a hardcoded security flag:

###### *Bash*

echo "Initializing Core Data... Fetching Keychain Profile... Setting Security Policy: kSecAttrAccessibleAlways... Process Complete." > TargetApp.app/TargetAppExecutable



* Run the static text analysis query to uncover the weak policy:

###### *Bash*

strings TargetApp.app/TargetAppExecutable | grep -E "kSecAttrAccessible"



##### *Your lab environment is fully configured, executed, and ready for grading!*

