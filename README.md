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
