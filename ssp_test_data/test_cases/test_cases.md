# Self Service+ Identity Test Cases

This document contains comprehensive test cases for Self Service+ (SSP) identity functionality, formatted for AI agent execution and manual testing guidance.

## Test Case Format

Each test case follows this structure:
- **Test ID**: Unique identifier from TestRail
- **Title**: Descriptive test case name
- **Category**: Functional area being tested
- **Type**: Test type (typically Acceptance)
- **Preconditions**: Given conditions using Gherkin syntax
- **Test Steps**: Step-by-step execution with expected results
- **Configuration**: Required configuration files and setup instructions
- **References**: Supporting documentation and screenshots

---

## Test Cases

### Test ID: C116671008
**Title**: Test state of tiles changes after sign in  
**Category**: Overview  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given a user has not yet signed into Self Service+ through the menubar utility
And a minimal menubar configuration is installed
And the Self Service+ menubar utility is running
```

**Test Steps**:

**Step 1**: Verify Initial State
```gherkin
When the user has launched the main Self Service+ application
Then the default state of the Account management section should be displayed with the following tiles:
- User account
- Identity provider (signed out)
- Password sync (out of sync)
- Local password expiration
```

**Step 2**: Sign In and Verify State Changes
```gherkin
When the user signs into the Self Service+ menubar application with a valid username and password
Then the state of the tiles should be updated accordingly:
- User account (no change)
- Identity provider (changes to "Signed in" status, displays IdP username)
    - If Okta, "View dashboard" button and Okta tenant hostname should also be displayed
- Password sync (changes to "Synced" status, text updated to "Password is currently in sync")
- Local password expiration (no change)
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Base.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Base.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**References**:
- [Signed out state screenshot](https://jamfsw.sharepoint.com/:i:/s/caribou.team/EWs--yex8OFAmLtlRpq_JacBXPUjTqhJJ6nd6OBHE3Gk5w?e=zJEz7L)
- [Signed in state screenshot](https://jamfsw.sharepoint.com/:i:/s/caribou.team/EZ4kysbC3V9Hh1rKX6etHAEBhTR59gexzJZrvuW2qFHtjw?e=JFPTxa)

---

### Test ID: C116671010
**Title**: Test default state of User Account tile  
**Category**: User Account Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given Self Service+ application is installed on the macOS device
And the menu bar utility is running
```

**Test Steps**:

**Step 1**: Verify User Account Tile Display
```gherkin
When the user opens the Self Service+ menu bar utility
Then the User Account tile shows "Standard user" label
And the user's name is displayed
And the user's username is displayed
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Base.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Base.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

---

### Test ID: C116671011
**Title**: Test Sign in button  
**Category**: Identity Provider Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed out of Self Service+
And the Identity Provider tile shows "Signed out" status
```

**Test Steps**:

**Step 1**: Initiate Sign In
```gherkin
When the user clicks the "Sign in" button
Then the identity provider authentication window is displayed
```

**Step 2**: Complete Authentication
```gherkin
When the user enters valid credentials
Then the authentication is successful
And the Identity Provider tile changes to "Signed in" status
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Base.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Base.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

---

### Test ID: C116671030
**Title**: Test View dashboard button - Okta only  
**Category**: Identity Provider Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+ with an Okta identity provider
And the Identity Provider tile shows the user's Okta email
```

**Test Steps**:

**Step 1**: Access Okta Dashboard
```gherkin
When the user clicks the "View dashboard" button
Then the user's Okta dashboard opens in the default browser
And the user can view their Okta applications and settings
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Base.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: This test is Okta-specific and should not be run with Microsoft Entra ID configurations.

---

### Test ID: C116671012
**Title**: Test Sync password button  
**Category**: Password Sync Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And the Password sync tile shows "Out of sync" status
And the message "Password is out of sync and needs to be updated" is displayed
```

**Test Steps**:

**Step 1**: Initiate Password Sync
```gherkin
When the user clicks the "Sync password" button
Then a password dialog appears requesting the user's password
```

**Step 2**: Complete Password Sync
```gherkin
When the user enters the correct password
Then the password sync process completes
And the Password sync tile status changes to "Synced"
And the message changes to "Password is currently in sync"
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Base.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Base.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: To test "Out of sync" state, password sync can be disabled using configurations with DisablePasswordSync variants.

---

### Test ID: C116671013
**Title**: Test Request now button  
**Category**: Administrator Privileges Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And Administrator privileges are available
```

**Test Steps**:

**Step 1**: Request Elevation
```gherkin
When the user clicks the "Request now" button in the Administrator privileges tile
Then the user immediately receives elevated privileges
And the Administrator privileges status changes directly to "Elevated"
And an elevation timer appears showing the remaining time (default 5 minutes or IT admin configured)
And optionally, a dialog may appear requesting justification for the elevation request
```

**Step 2**: Provide Justification (if prompted)
```gherkin
When the user provides a reason for the request (if prompted)
Then the reason is recorded for IT admin audit purposes
And the user continues with elevated privileges
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Kerberos+PrivilegeElevation.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Kerberos+PrivilegeElevation.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: Requires TemporaryUserPromotion configuration enabled.

---

### Test ID: C116671018
**Title**: Test End Elevation button  
**Category**: Administrator Privileges Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And the user has elevated Administrator privileges
And the Administrator privileges tile shows "Elevated" status
```

**Test Steps**:

**Step 1**: End Elevation
```gherkin
When the user clicks the "End Elevation" button
Then the elevated privileges are revoked immediately
And the Administrator privileges tile status changes back to "Available"
And the "Request now" button is displayed again
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Kerberos+PrivilegeElevation.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Kerberos+PrivilegeElevation.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: User must first have elevated privileges before testing this feature.

---

### Test ID: C116671027
**Title**: Test tile state - user promotion timer persists reboot (session still active)  
**Category**: Administrator Privileges Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And the user has elevated Administrator privileges
And an elevation timer is displayed showing remaining time
```

**Test Steps**:

**Step 1**: Reboot and Verify Persistence
```gherkin
When the system is rebooted
And the user logs back in
Then Self Service+ retains the user's elevated status
And the elevation timer continues from where it left off
And the Administrator privileges tile still shows "Elevated" status
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Kerberos+PrivilegeElevation.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Kerberos+PrivilegeElevation.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: Ensure elevation timer has sufficient time remaining before reboot.

---

### Test ID: C116671028
**Title**: Test tile state - user promotion timer persists reboot (session ended, user demoted on startup)  
**Category**: Administrator Privileges Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And the user has elevated Administrator privileges
And the elevation session is set to expire soon
```

**Test Steps**:

**Step 1**: Reboot After Timer Expiration
```gherkin
When the system is rebooted
And the reboot process takes longer than the remaining elevation time
And the user logs back in
Then the elevated privileges are automatically revoked
And the Administrator privileges tile shows "Available" status
And the "Request now" button is displayed
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Kerberos+PrivilegeElevation.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Kerberos+PrivilegeElevation.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: Configure short elevation timer or delay reboot to exceed timer.

---

### Test ID: C116671014
**Title**: Test Set up authenticator button  
**Category**: Desktop MFA Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And Desktop MFA is available
```

**Test Steps**:

**Step 1**: Initiate MFA Setup
```gherkin
When the user clicks the "Set up authenticator" button
Then the offline authentication setup process is initiated
And the user is prompted to set up their authentication method
```

**Step 2**: Complete MFA Setup
```gherkin
When the authentication method setup is completed
Then the status changes to reflect the configured authenticator
```

**Configuration**:
- System level: Use `configurations/OfflineMFA.plist`
- Load with: `sudo cp [config-file] /Library/Preferences/com.jamf.connect.plist`
- Then: `defaults read /Library/Preferences/com.jamf.connect.login.plist`

**Note**: OfflineMFA requires system-level configuration.

---

### Test ID: C116671026
**Title**: Test Remove multifactor authentication button  
**Category**: Desktop MFA Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And Desktop MFA is configured
And the MFA status shows as active
```

**Test Steps**:

**Step 1**: Initiate MFA Removal
```gherkin
When the user clicks the "Remove multifactor authentication" button
Then a confirmation dialog appears
```

**Step 2**: Confirm MFA Removal
```gherkin
When the removal is confirmed
Then the MFA configuration is removed
And the MFA status changes to "Available"
And the "Set up authenticator" button is displayed again
```

**Configuration**:
- System level: Use `configurations/OfflineMFA.plist`
- Load with: `sudo cp [config-file] /Library/Preferences/com.jamf.connect.plist`
- Then: `defaults read /Library/Preferences/com.jamf.connect.login.plist`

**Note**: MFA must first be configured before testing removal.

---

### Test ID: C116671016
**Title**: Test Change password button (kerberos)  
**Category**: Local Password Expiration Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And Kerberos SSO is configured
```

**Test Steps**:

**Step 1**: Initiate Kerberos Password Change
```gherkin
When the user clicks the "Change password" button in the Local account password section
Then the kerberos password change dialog appears
```

**Step 2**: Complete Password Change
```gherkin
When the user enters their current and new password
Then upon successful password change, the local account password is updated
And a confirmation message is displayed
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Kerberos.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Kerberos.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: Requires Kerberos realm ENZIANTECH.COM configuration.

---

### Test ID: C116671019
**Title**: Test Change password button (web)  
**Category**: Local Password Expiration Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And the identity provider supports web-based password changes
```

**Test Steps**:

**Step 1**: Initiate Web Password Change
```gherkin
When the user clicks the "Change password" button in the Local account password section
Then the identity provider's password change page opens in the default browser
And the user can complete their password change through the web interface
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Base.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Base.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: Web-based password change depends on IdP capabilities.

---

### Test ID: C116671017
**Title**: Test Refresh button  
**Category**: Kerberos SSO Tile  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And Kerberos SSO is active
And the Kerberos ticket has an expiration time
```

**Test Steps**:

**Step 1**: Refresh Kerberos Ticket
```gherkin
When the user clicks the "Refresh" button in the Kerberos SSO tile
Then the Kerberos ticket is refreshed
And the expiration time is updated with a new value
And the "Active tickets" status remains displayed
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Kerberos.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Kerberos.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: Requires active Kerberos realm ENZIANTECH.COM configuration.

---

### Test ID: C116671007
**Title**: In-place upgrade from Jamf Connect 2.x to Self Service+ 2.x  
**Category**: Upgrade Scenarios  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given Jamf Connect 2.x is installed on the macOS device
And the user has an active Jamf Connect configuration
```

**Test Steps**:

**Step 1**: Perform Upgrade
```gherkin
When the system administrator deploys Self Service+ 2.x as an upgrade
Then the upgrade process completes successfully
And Self Service+ preserves all user settings and configurations from Jamf Connect
And the menu bar utility appears with the new Self Service+ branding
And all previously configured identity features continue to function
```

**Configuration**:
- Pre-upgrade: Any existing Jamf Connect 2.x configuration
- Post-upgrade verification: Use equivalent Self Service+ configurations

**Note**: This test requires an existing Jamf Connect 2.x installation.

---

### Test ID: C116671021
**Title**: Test Password Expiration notification  
**Category**: Notifications  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And the user's password is approaching its expiration date
```

**Test Steps**:

**Step 1**: Verify Password Expiration Notification
```gherkin
When the password expiration threshold is reached
Then a notification appears warning about the upcoming password expiration
And the notification includes the number of days remaining
And clicking the notification opens the Self Service+ password change interface
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Base.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Base.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: May require configuring password expiration policies in IdP.

---

### Test ID: C116671022
**Title**: Test Privilege Request notification  
**Category**: Notifications  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And Administrator privileges are configured
And the user does not currently have elevated privileges
```

**Test Steps**:

**Step 1**: Verify Privilege Request Notification
```gherkin
When the user requests administrative privileges
Then a notification appears confirming the privilege request
And the notification shows the status of the request
And clicking the notification opens the Administrator privileges tile
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Kerberos+PrivilegeElevation.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Kerberos+PrivilegeElevation.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

---

### Test ID: C116671023
**Title**: Test Privilege Granted notification  
**Category**: Notifications  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And Administrator privileges are configured
And a privilege request has been made
```

**Test Steps**:

**Step 1**: Verify Privilege Granted Notification
```gherkin
When administrative privileges are granted to the user
Then a notification appears confirming that privileges have been granted
And the notification shows the duration of the elevated access
And clicking the notification opens the Administrator privileges tile showing "Elevated" status
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Kerberos+PrivilegeElevation.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Kerberos+PrivilegeElevation.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

---

### Test ID: C116671024
**Title**: Test Privilege Revoked notification  
**Category**: Notifications  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And the user has elevated Administrator privileges
And the elevation timer is about to expire
```

**Test Steps**:

**Step 1**: Verify Privilege Revoked Notification
```gherkin
When the elevation timer expires or privileges are manually revoked
Then a notification appears informing the user that privileges have been revoked
And the notification explains that standard user access has been restored
And clicking the notification opens the Administrator privileges tile showing "Available" status
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Kerberos+PrivilegeElevation.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Kerberos+PrivilegeElevation.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

---

### Test ID: C116671025
**Title**: Test Offline MFA required notification - initial notification  
**Category**: Notifications  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And Desktop MFA is required but not yet configured
```

**Test Steps**:

**Step 1**: Verify Initial MFA Notification
```gherkin
When the offline MFA requirement threshold is reached
Then an initial notification appears prompting the user to set up offline MFA
And clicking the notification opens the MFA setup interface
And the Desktop MFA tile indicates that setup is required
```

**Configuration**:
- System level: Use `configurations/OfflineMFA.plist`
- Load with: `sudo cp [config-file] /Library/Preferences/com.jamf.connect.plist`
- Then: `defaults read /Library/Preferences/com.jamf.connect.login.plist`

---

### Test ID: C116671029
**Title**: Test Offline MFA required notification - hourly reminder notifications  
**Category**: Notifications  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And Desktop MFA is required but not yet configured
And the user has dismissed the initial MFA notification
```

**Test Steps**:

**Step 1**: Verify Hourly MFA Reminders
```gherkin
When one hour has passed since the last notification
Then a reminder notification appears prompting the user to set up offline MFA
And these reminders continue hourly until MFA is configured
And each notification can be clicked to open the MFA setup interface
```

**Configuration**:
- System level: Use `configurations/OfflineMFA.plist`
- Load with: `sudo cp [config-file] /Library/Preferences/com.jamf.connect.plist`
- Then: `defaults read /Library/Preferences/com.jamf.connect.login.plist`

---

### Test ID: C116671045
**Title**: Test local password out of sync on background check notification  
**Category**: Notifications  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And the password is initially in sync
```

**Test Steps**:

**Step 1**: Verify Out of Sync Notification
```gherkin
When the background password check detects that the local password is out of sync
Then a notification appears informing the user about the password sync issue
And the Password sync tile changes to "Out of sync" status
And clicking the notification opens the password sync interface
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Base.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Base.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: May require manually changing local password to create out-of-sync state.

---

### Test ID: C116671046
**Title**: Test cloud password incorrect on background check notification  
**Category**: Notifications  
**Type**: Acceptance

**Preconditions**:
```gherkin
Given the user is signed in to Self Service+
And the password is initially in sync
```

**Test Steps**:

**Step 1**: Verify Cloud Password Issue Notification
```gherkin
When the background check detects that the cloud password is incorrect or changed
Then a notification appears informing the user about the cloud password issue
And the Password sync tile changes to "Out of sync" status
And the notification provides guidance on updating the cloud password
```

**Configuration**:
- Okta: Use `configurations/Okta Classic/Base.plist`
- Microsoft Entra ID: Use `configurations/Microsoft Entra ID/Base.plist`
- Load with: `defaults import com.jamf.connect [config-file-path]`

**Note**: May require changing password in IdP to create out-of-sync state.

---

## Configuration Reference

### Available Configuration Files

#### Okta Classic Configurations:
- `configurations/Okta Classic/Base.plist` - Basic Okta Classic setup
- `configurations/Okta Classic/Kerberos.plist` - Kerberos authentication enabled
- `configurations/Okta Classic/Kerberos+PrivilegeElevation.plist` - Kerberos with admin privileges
- `configurations/Okta Classic/Kerberos+PrivilegeElevation+DisablePasswordSync.plist` - Full Kerberos setup without password sync

#### Microsoft Entra ID Configurations:
- `configurations/Microsoft Entra ID/Base.plist` - Basic Microsoft Entra ID setup
- `configurations/Microsoft Entra ID/Base+DisablePasswordSync.plist` - Basic setup without password sync
- `configurations/Microsoft Entra ID/Kerberos.plist` - Kerberos authentication enabled
- `configurations/Microsoft Entra ID/Kerberos+PrivilegeElevation.plist` - Kerberos with admin privileges
- `configurations/Microsoft Entra ID/Kerberos+PrivilegeElevation+DisablePasswordSync.plist` - Full Kerberos setup without password sync

#### System-Level Configurations:
- `configurations/OfflineMFA.plist` - Multi-factor authentication for offline scenarios

### Configuration Loading Commands

**User-level configurations**:
```bash
defaults import com.jamf.connect /path/to/configuration.plist
```

**System-level configurations**:
```bash
sudo defaults import com.jamf.connect.login /path/to/OfflineMFA.plist
```

**Verification**:
```bash
defaults read com.jamf.connect
```

**Service restart** (if needed):
```bash
sudo launchctl unload /Library/LaunchDaemons/com.jamf.connect.daemon.ssp.plist
sudo launchctl load /Library/LaunchDaemons/com.jamf.connect.daemon.ssp.plist
```

## Test Environment Requirements

### Kerberos Environment:
- **Realm**: ENZIANTECH.COM
- **Domain Controller**: Must be accessible for Kerberos ticket validation
- **User Account**: Valid domain credentials for ENZIANTECH.COM realm

### Network Requirements:
- Internet connectivity for identity provider authentication
- Access to Okta tenant or Microsoft Entra ID tenant
- Kerberos KDC connectivity (for Kerberos tests)

## Notes for AI Agent Execution

1. **Configuration Management**: Always verify the correct configuration is loaded before executing test steps
2. **State Validation**: Check initial application state matches preconditions before proceeding
3. **Error Handling**: Document any deviations from expected results with screenshots and logs
4. **Test Dependencies**: Some tests require specific prerequisites (e.g., elevated privileges, MFA setup)
5. **Cleanup**: Reset application state between tests to ensure clean test execution
