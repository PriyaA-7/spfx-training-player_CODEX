# TrainingCompletionRegister Schema (v1.0)

## Purpose
The `TrainingCompletionRegister` SharePoint List is the authoritative record of training completion events. Each list item represents **one user completing one training module for one specific training version**. This list is designed for audit-grade traceability and governance, and it relies on SharePoint list controls (permissions and version history) for integrity.

## Column Definitions
Each required column below includes data type, mandatory flag, example value, and audit notes.

| Column | Data type | Mandatory | Example value | Audit notes |
| --- | --- | --- | --- | --- |
| CompletionId | Single line of text | Yes | `TC-2025-00001234` | Immutable identifier for the completion record. System-generated and **non-editable** after creation. |
| User | Person or Group (single user, Entra ID-backed) | Yes | `Avery Johnson` | Must be a **Person or Group** column (single user only). Entra ID (Azure AD) backed. **Do not use free-text** for user identity. Non-editable after creation. |
| UserDirectoryId | Single line of text | Yes | `3f1a2b3c-4d5e-6f70-8191-0a1b2c3d4e5f` | Azure AD Object ID for the user. System-controlled to ensure identity binding. Non-editable after creation. |
| TrainingId | Single line of text | Yes | `SEC-AWARENESS` | Stable training identifier. Non-editable after creation. |
| TrainingTitle | Single line of text | Yes | `Security Awareness Training` | Human-readable title at the time of completion. Non-editable after creation. |
| TrainingVersion | Single line of text | Yes | `2025-Q1` | Specific version label for the training module. Non-editable after creation. |
| CompletionStatus | Choice (Completed, Expired, Revoked) | Yes | `Completed` | Status of the completion record. Controlled vocabulary. Changes restricted to system processes. |
| CompletionDateTime | Date and Time (UTC) | Yes | `2025-02-10T14:35:22Z` | UTC timestamp when completion occurred. System-controlled and non-editable after creation. |
| ExpiryDateTime | Date and Time (UTC) | No | `2026-02-10T00:00:00Z` | UTC timestamp for expiry (if applicable). System-controlled. |
| CompletionSource | Choice (MS Forms, Manual, Import) | Yes | `MS Forms` | Source of the completion event. System-controlled to preserve provenance. |
| EvidenceLink | Hyperlink or Picture | No | `https://contoso.sharepoint.com/sites/training/Forms/AllItems.aspx?view=7` | Link to form response or SharePoint evidence. Read-only after creation. |
| ControlExecutionId | Single line of text | Yes | `SEC-AWARENESS-Q1-2025` | Control execution identifier (e.g., campaign or control run). Non-editable after creation. |
| SystemCreatedBy | Single line of text | Yes | `svc-training-register` | Service account or system context that created the record. System-controlled and non-editable. |
| RecordCreatedDateTime | Date and Time (UTC) | Yes | `2025-02-10T14:35:30Z` | UTC timestamp when the list item was created. System-controlled and non-editable. |

## Governance and Integrity
- **No cryptographic hashes** are stored (no `RecordHash` field). Integrity is ensured through SharePoint list **version history** and **permissions**.
- System-controlled, non-editable fields after creation: **CompletionId, User, UserDirectoryId, TrainingId, TrainingTitle, TrainingVersion, CompletionDateTime, ExpiryDateTime, CompletionSource, EvidenceLink, ControlExecutionId, SystemCreatedBy, RecordCreatedDateTime**.
- **CompletionStatus** changes are restricted to system processes (e.g., expiration job, administrative revocation with approvals).
- **Do not use free-text fields for user identity**. The `User` field must be a Person or Group column (single user), backed by Entra ID (Azure AD).

## Notes for SOC / ISO Audit Readiness
- **UTC timestamps** are used for all date/time fields to ensure consistent, cross-region auditability.
- The list follows an **append-only / restricted edit** model. Immutable fields are locked after creation, and status changes are controlled by approved system processes.
- **Item-level permissions** are enforced to restrict who can view or edit records, supporting least privilege.
- **Evidence retention** expectations: Evidence links must remain accessible for the required retention period; evidence should be stored in SharePoint or a governed system with retention policies aligned to compliance requirements.
