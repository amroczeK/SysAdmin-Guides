
# Useful-Windows-Server-Guides

## Active Directory
### Change default OU for Computers in AD
1. Open Active Directory Users and Computers
2. Click View -> Select "Advanced Features"
3. Right-click the "Organizational Unit" folder you want to set as default and click "Properties"
4. Select "Attribute Editor" and select "distinguishedName"
5. Copy the value/line
6. Launch PowerShell with Administrator elevation
7. Type "redircmp" and paste the copied "distinguishedName" value and surround it with quotes
8. Press Enter, you will get "Redirection was successful." if it worked

Example: redircmp "OU=_WDS,OU=NOTCREATIVE OU,DC=NOTCREATIVE,DC=internal"

Reference: http://pc-addicts.com/server-2012-change-default-ou/

### Rename a Domain Controller
1. Launch PowerShell with Administrator elevation
2. Add a new Domain Controller name by typing the following: use: netdom computername "currentDCname" /add: "newDCname" ... Example: use: netdom computername "DCServer.com" /add: "SVR-DOMAIN.com"
3. Next we have to promote this new domain name as the primary domain name by typing the following: netdom computername "currentDCname" /makeprimary: "newDCname"
4. Reboot server
5. Remove old DC name by typing the following in PowerShell: netdom computername "newDCname" /remove: "oldDCname"
6. Verify replication by typing: repadmin /showrepl

Reference: https://community.spiceworks.com/how_to/103538-properly-renaming-a-domain-controller-server-2012r2

## Windows Distributed File System
### Installation
1. Add Role Services -> Distributed File System (DFS Namespaces & Replication)
2. Choose to create a namespace now or later
3. Confirm & Install

### Namespace Configuration
1. Expand File Services -> Right-click "DFS Management" -> "New Namespace"
2. Enter the name of the server that will host the namespace or browse to it
3. Enter the name for the namespace, e.g. create a UNC path to combine network shares, "\\\Distro\DFS"
4. Choose between Domain-based/Stand-alone namespace, note: Domain-based is most common
5. Click Create to create the name under "Review Settings..."

### Adding folders to namespace
1. Under "DFS Management" in "File Services", expand "Namespaces" and click the UNC path created earlier e.g. "\\\Distro\DFS"
2. Right-click "\\\Distro\DFS" -> New Folder
3. Enter the folder name and add target folder e.g. "\\\DCSouth\users", NOTE: make sure the folders you are selecting are already shared on the server. This folder will now appear inside "\\\Distro\DFS"
4. Continue adding folders from multiple servers if required, to have a consolidated folder share server. NOTE: Each folder will show you their namespace/path in their properties

### Replication Configuration
This is used to replicate folders and their contents by syncing across the network. For example if you have a "Scanned" folder which stores files scanned by an office printer but want the contents of this folder to by synced to a "Scanned" folder on another server/site location, you can use DFS Replication to achieve this by setting up a target folder for the source to replicate/sync to.

1. Under "DFS Management" in "File Services", expand "Namespaces"
2. Create a folder called "Scanned" and add a target folder e.g. "\\\DCNorth\Scanned" which is an empty folder
3. Right-click the target folder you wish to have files replicate to and click "Add Folder Target"
4. Enter path of folder you want to sync from e.g. "\\\DCSouth\Scanned" which is the source folder containing the content
5. Click "Yes" when prompted to create a replication group
6. "Replicate Folder Wizard" will open up, click "Next" for Step "Replication Group and Replicated Folder Name" unless you want to modify the default settings
7. "Replication Eligibility" shows which folders are going to be replicated, click "Next"
8. In step "Primary Member", select the server which contains the folder with conent you wish to replicate from e.g. source
9. In "Topology Selection", select "Full mesh" by default and click "Next". Click "No topology" if you want to customize your own topology
10. In "Replication Group Schedule and Bandwidth", select "Replicate continuously..." if you want 24/7 syncing/replication and select the desired "Bandwidth", "Full" is the default setting and click "Next". NOTE: It is best to limit the bandwidth so it does not impact the network too much
11. In step "Confirmation" click "Close" and click "OK" on "Replication Delay" prompt

After a few minutes the target folder will sync with the source folder and aquire all its files. Therefore "\\\DCNorth\Scanned" and "\\\Distro\DFS\Scanned" will have all documents and files in "\\\DCSouth\Scanned".
