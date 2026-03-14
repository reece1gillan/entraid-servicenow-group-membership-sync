## Title
Entra ID ServiceNow Group Membership Sync

## Overview
Sync groups and memberships between entra ID and ServiceNow in real-time.

## Architecture
Entra ID > Event Grid Topic > REST API Call > Azure Function: SyncEntraIDToSN

ServiceNow > Microsoft Graph API Call > Azure Function: SyncSNtoEntraID

## Data Flow

### Group: Entra ID > ServiceNow
1. Group created or deleted in Entra ID
2. Event Grid receives event with 'groupId', 'groupName'
3. SyncGroupEntraIDToSN function receives event from Event Grid, calls ServiceNow REST API
4. Group is created or deactivated in ServiceNow

### Group: ServiceNow > Entra ID
1. Group created or deleted in ServiceNow
2. Flow triggers REST call to Azure Function webhook
3. SyncGroupSNtoEntraID function receives event from ServiceNow, calls Microsoft Graph API
4. Group is created or deactivated in Entra ID

### Membership: Entra ID > ServiceNow
1. User added or removed to / from group in Entra ID
2. Event Grid receives event with 'groupId', 'groupName', 'userId' and 'action'
3. SyncGroupMembershipEntraIDToSN function receives event from Event Grid, calls ServiceNow REST API
4. Group membership is created or deleted in ServiceNow

### Membership: ServiceNow > Entra ID
1. User added or removed to / from group in ServiceNow
2. Flow triggers REST call to Azure Function webhook
3. SyncGroupMembershipSNtoEntraID function receives event from ServiceNow, calls Microsoft Graph API
4. Group memership is created or deleted in Entra ID

## Edge Cases
- Group doesn't exist in target system
- User doesn't exist in target system
- API call fails
- Duplicate events

## Acceptance Criteria
- Add user in either system > sync appears in other system
- Remove user in either system > sync appears in other system
- Errors logged via Application Insights
- No data loss or silent failures

## Tech
- Azure Event Grid (event broker)
- Azure Functions (serverless compute, Python 3.16)
- Microsoft Graph API (Entra ID interactions)
- Azure Application Insights (moniroting/logging)
- ServiceNow REST API (ServiceNow interactions)
- ServiceNow Flow Designer (ServiceNow logic)