# Compliance API

The Compliance API provides operations for working with ChronoWeave's compliance features, including HIPAA and FINRA compliance.

## HIPAA Compliance

The HIPAA Compliance endpoints provide operations for working with Protected Health Information (PHI).

### Detect PHI

```
POST /compliance/hipaa/detect-phi
```

Detect Protected Health Information (PHI) in text.

#### Request

```json
{
  "text": "Patient John Smith (DOB: 01/15/1980) was prescribed 20mg of Lisinopril for hypertension on 06/12/2023.",
  "context_aware": true,
  "confidence_threshold": 0.7
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "phi_detected": true,
    "phi_items": [
      {
        "type": "NAME",
        "value": "John Smith",
        "start": 8,
        "end": 18,
        "confidence": 0.95
      },
      {
        "type": "DATE",
        "value": "01/15/1980",
        "start": 25,
        "end": 35,
        "confidence": 0.98
      },
      {
        "type": "MEDICATION",
        "value": "Lisinopril",
        "start": 56,
        "end": 66,
        "confidence": 0.92
      },
      {
        "type": "CONDITION",
        "value": "hypertension",
        "start": 71,
        "end": 83,
        "confidence": 0.89
      },
      {
        "type": "DATE",
        "value": "06/12/2023",
        "start": 87,
        "end": 97,
        "confidence": 0.98
      }
    ]
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Classify PHI

```
POST /compliance/hipaa/classify-phi
```

Classify detected PHI by type and sensitivity.

#### Request

```json
{
  "phi_items": [
    {
      "type": "NAME",
      "value": "John Smith",
      "start": 8,
      "end": 18,
      "confidence": 0.95
    },
    {
      "type": "DATE",
      "value": "01/15/1980",
      "start": 25,
      "end": 35,
      "confidence": 0.98
    },
    {
      "type": "MEDICATION",
      "value": "Lisinopril",
      "start": 56,
      "end": 66,
      "confidence": 0.92
    },
    {
      "type": "CONDITION",
      "value": "hypertension",
      "start": 71,
      "end": 83,
      "confidence": 0.89
    },
    {
      "type": "DATE",
      "value": "06/12/2023",
      "start": 87,
      "end": 97,
      "confidence": 0.98
    }
  ]
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "classified_phi": [
      {
        "type": "NAME",
        "value": "John Smith",
        "start": 8,
        "end": 18,
        "confidence": 0.95,
        "category": "IDENTIFIER",
        "sensitivity": "HIGH",
        "hipaa_category": "INDIVIDUAL_IDENTIFIER"
      },
      {
        "type": "DATE",
        "value": "01/15/1980",
        "start": 25,
        "end": 35,
        "confidence": 0.98,
        "category": "DEMOGRAPHIC",
        "sensitivity": "HIGH",
        "hipaa_category": "DATE"
      },
      {
        "type": "MEDICATION",
        "value": "Lisinopril",
        "start": 56,
        "end": 66,
        "confidence": 0.92,
        "category": "CLINICAL",
        "sensitivity": "MEDIUM",
        "hipaa_category": "HEALTH_INFORMATION"
      },
      {
        "type": "CONDITION",
        "value": "hypertension",
        "start": 71,
        "end": 83,
        "confidence": 0.89,
        "category": "CLINICAL",
        "sensitivity": "MEDIUM",
        "hipaa_category": "HEALTH_INFORMATION"
      },
      {
        "type": "DATE",
        "value": "06/12/2023",
        "start": 87,
        "end": 97,
        "confidence": 0.98,
        "category": "TEMPORAL",
        "sensitivity": "LOW",
        "hipaa_category": "DATE"
      }
    ]
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### De-identify PHI

```
POST /compliance/hipaa/de-identify
```

De-identify text containing PHI.

#### Request

```json
{
  "text": "Patient John Smith (DOB: 01/15/1980) was prescribed 20mg of Lisinopril for hypertension on 06/12/2023.",
  "method": "safe_harbor",
  "preserve_format": true
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "de_identified_text": "Patient [NAME] (DOB: [DATE]) was prescribed 20mg of [MEDICATION] for [CONDITION] on [DATE].",
    "phi_replacements": [
      {
        "original": "John Smith",
        "replacement": "[NAME]",
        "type": "NAME"
      },
      {
        "original": "01/15/1980",
        "replacement": "[DATE]",
        "type": "DATE"
      },
      {
        "original": "Lisinopril",
        "replacement": "[MEDICATION]",
        "type": "MEDICATION"
      },
      {
        "original": "hypertension",
        "replacement": "[CONDITION]",
        "type": "CONDITION"
      },
      {
        "original": "06/12/2023",
        "replacement": "[DATE]",
        "type": "DATE"
      }
    ]
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Encrypt PHI

```
POST /compliance/hipaa/encrypt-phi
```

Encrypt PHI in structured data.

#### Request

```json
{
  "data": {
    "patient": {
      "name": "John Smith",
      "dob": "01/15/1980",
      "ssn": "123-45-6789"
    },
    "encounter": {
      "date": "06/12/2023",
      "provider": "Dr. Jane Doe",
      "diagnosis": "Hypertension",
      "medication": "Lisinopril 20mg"
    }
  },
  "phi_fields": [
    "patient.name",
    "patient.dob",
    "patient.ssn",
    "encounter.provider",
    "encounter.diagnosis",
    "encounter.medication"
  ]
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "encrypted_data": {
      "patient": {
        "name": "ENC:AES256:abc123def456...",
        "dob": "ENC:AES256:ghi789jkl012...",
        "ssn": "ENC:AES256:mno345pqr678..."
      },
      "encounter": {
        "date": "06/12/2023",
        "provider": "ENC:AES256:stu901vwx234...",
        "diagnosis": "ENC:AES256:yz0123abc456...",
        "medication": "ENC:AES256:def789ghi012..."
      }
    },
    "encryption_metadata": {
      "algorithm": "AES-256-GCM",
      "key_id": "key_jkl345",
      "timestamp": "2023-07-15T10:30:45Z"
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Decrypt PHI

```
POST /compliance/hipaa/decrypt-phi
```

Decrypt encrypted PHI.

#### Request

```json
{
  "encrypted_data": {
    "patient": {
      "name": "ENC:AES256:abc123def456...",
      "dob": "ENC:AES256:ghi789jkl012...",
      "ssn": "ENC:AES256:mno345pqr678..."
    },
    "encounter": {
      "date": "06/12/2023",
      "provider": "ENC:AES256:stu901vwx234...",
      "diagnosis": "ENC:AES256:yz0123abc456...",
      "medication": "ENC:AES256:def789ghi012..."
    }
  },
  "key_id": "key_jkl345"
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "decrypted_data": {
      "patient": {
        "name": "John Smith",
        "dob": "01/15/1980",
        "ssn": "123-45-6789"
      },
      "encounter": {
        "date": "06/12/2023",
        "provider": "Dr. Jane Doe",
        "diagnosis": "Hypertension",
        "medication": "Lisinopril 20mg"
      }
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Log PHI Access

```
POST /compliance/hipaa/log-access
```

Log access to PHI for compliance purposes.

#### Request

```json
{
  "user_id": "user_123",
  "phi_types": ["NAME", "DOB", "DIAGNOSIS", "MEDICATION"],
  "purpose": "treatment",
  "patient_id": "patient_456",
  "action": "view",
  "system_id": "system_789"
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "log_id": "log_abc123",
    "user_id": "user_123",
    "phi_types": ["NAME", "DOB", "DIAGNOSIS", "MEDICATION"],
    "purpose": "treatment",
    "patient_id": "patient_456",
    "action": "view",
    "system_id": "system_789",
    "timestamp": "2023-07-15T10:35:22Z",
    "ip_address": "192.168.1.100"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Generate HIPAA Compliance Report

```
POST /compliance/hipaa/report
```

Generate a HIPAA compliance report.

#### Request

```json
{
  "report_type": "access_log",
  "start_date": "2023-07-01T00:00:00Z",
  "end_date": "2023-07-15T23:59:59Z",
  "filters": {
    "user_ids": ["user_123"],
    "patient_ids": ["patient_456"],
    "actions": ["view", "edit", "delete"]
  },
  "format": "pdf"
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "report_id": "report_def456",
    "report_type": "access_log",
    "start_date": "2023-07-01T00:00:00Z",
    "end_date": "2023-07-15T23:59:59Z",
    "generation_time": "2023-07-15T10:40:15Z",
    "download_url": "https://api.chronoweave.ai/v1/compliance/hipaa/reports/report_def456",
    "expiration": "2023-07-22T10:40:15Z"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

## FINRA Compliance

The FINRA Compliance endpoints provide operations for working with financial data.

### Apply Retention Policy

```
POST /compliance/finra/retention
```

Apply a retention policy to financial data.

#### Request

```json
{
  "user_id": "user_123",
  "data_category": "financial_transactions",
  "retention_period": "7y",
  "action": "archive",
  "jurisdiction": "US-FINRA"
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "policy_id": "policy_ghi789",
    "user_id": "user_123",
    "data_category": "financial_transactions",
    "retention_period": "7y",
    "action": "archive",
    "jurisdiction": "US-FINRA",
    "creation_time": "2023-07-15T10:45:30Z",
    "status": "active"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Manage Data Lifecycle

```
POST /compliance/finra/lifecycle
```

Manage the lifecycle of financial data.

#### Request

```json
{
  "user_id": "user_123",
  "data_category": "financial_transactions",
  "lifecycle_stage": "archive",
  "effective_date": "2023-07-15T00:00:00Z",
  "node_ids": ["node_abc123", "node_def456"]
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "lifecycle_id": "lifecycle_jkl012",
    "user_id": "user_123",
    "data_category": "financial_transactions",
    "lifecycle_stage": "archive",
    "effective_date": "2023-07-15T00:00:00Z",
    "node_ids": ["node_abc123", "node_def456"],
    "creation_time": "2023-07-15T10:50:15Z",
    "status": "processing"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Generate Regulatory Report

```
POST /compliance/finra/report
```

Generate a regulatory report for FINRA compliance.

#### Request

```json
{
  "report_type": "transaction_history",
  "start_date": "2023-07-01T00:00:00Z",
  "end_date": "2023-07-15T23:59:59Z",
  "user_id": "user_123",
  "format": "pdf"
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "report_id": "report_mno345",
    "report_type": "transaction_history",
    "start_date": "2023-07-01T00:00:00Z",
    "end_date": "2023-07-15T23:59:59Z",
    "user_id": "user_123",
    "generation_time": "2023-07-15T10:55:22Z",
    "download_url": "https://api.chronoweave.ai/v1/compliance/finra/reports/report_mno345",
    "expiration": "2023-07-22T10:55:22Z"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Verify Compliance Rule

```
POST /compliance/finra/verify-rule
```

Verify that a specific compliance rule is being followed.

#### Request

```json
{
  "rule_id": "rule_pqr678",
  "user_id": "user_123",
  "data_category": "financial_transactions",
  "reference_time": "2023-07-15T12:00:00Z"
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "rule_id": "rule_pqr678",
    "user_id": "user_123",
    "data_category": "financial_transactions",
    "reference_time": "2023-07-15T12:00:00Z",
    "compliant": true,
    "verification_details": {
      "retention_policy": "compliant",
      "data_lifecycle": "compliant",
      "access_controls": "compliant",
      "audit_logging": "compliant"
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

## Cross-Industry Compliance

The Cross-Industry Compliance endpoints provide operations that apply across multiple regulatory frameworks.

### Compliance Status

```
GET /compliance/status/{user_id}
```

Get the compliance status for a user across all applicable regulations.

#### Parameters

- `jurisdiction` (optional): Filter by jurisdiction
- `data_category` (optional): Filter by data category

#### Response

```json
{
  "status": "success",
  "data": {
    "user_id": "user_123",
    "overall_status": "compliant",
    "jurisdictions": {
      "US-HIPAA": {
        "status": "compliant",
        "details": {
          "phi_protection": "compliant",
          "access_controls": "compliant",
          "audit_logging": "compliant"
        },
        "last_verified": "2023-07-15T11:00:00Z"
      },
      "US-FINRA": {
        "status": "compliant",
        "details": {
          "retention_policies": "compliant",
          "data_lifecycle": "compliant",
          "regulatory_reporting": "compliant"
        },
        "last_verified": "2023-07-15T11:00:00Z"
      },
      "EU-GDPR": {
        "status": "compliant",
        "details": {
          "consent_management": "compliant",
          "data_minimization": "compliant",
          "right_to_be_forgotten": "compliant"
        },
        "last_verified": "2023-07-15T11:00:00Z"
      }
    }
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Compliance Audit

```
POST /compliance/audit
```

Perform a compliance audit for a user.

#### Request

```json
{
  "user_id": "user_123",
  "jurisdictions": ["US-HIPAA", "US-FINRA", "EU-GDPR"],
  "data_categories": ["health_information", "financial_transactions", "personal_data"],
  "reference_time": "2023-07-15T12:00:00Z"
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "audit_id": "audit_stu901",
    "user_id": "user_123",
    "audit_time": "2023-07-15T11:05:30Z",
    "reference_time": "2023-07-15T12:00:00Z",
    "overall_status": "compliant",
    "findings": [
      {
        "jurisdiction": "US-HIPAA",
        "data_category": "health_information",
        "status": "compliant",
        "details": {
          "phi_protection": "compliant",
          "access_controls": "compliant",
          "audit_logging": "compliant"
        }
      },
      {
        "jurisdiction": "US-FINRA",
        "data_category": "financial_transactions",
        "status": "compliant",
        "details": {
          "retention_policies": "compliant",
          "data_lifecycle": "compliant",
          "regulatory_reporting": "compliant"
        }
      },
      {
        "jurisdiction": "EU-GDPR",
        "data_category": "personal_data",
        "status": "compliant",
        "details": {
          "consent_management": "compliant",
          "data_minimization": "compliant",
          "right_to_be_forgotten": "compliant"
        }
      }
    ],
    "recommendations": []
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

### Compliance Report

```
POST /compliance/report
```

Generate a comprehensive compliance report.

#### Request

```json
{
  "user_id": "user_123",
  "report_type": "comprehensive",
  "start_date": "2023-07-01T00:00:00Z",
  "end_date": "2023-07-15T23:59:59Z",
  "jurisdictions": ["US-HIPAA", "US-FINRA", "EU-GDPR"],
  "format": "pdf"
}
```

#### Response

```json
{
  "status": "success",
  "data": {
    "report_id": "report_vwx234",
    "report_type": "comprehensive",
    "user_id": "user_123",
    "start_date": "2023-07-01T00:00:00Z",
    "end_date": "2023-07-15T23:59:59Z",
    "generation_time": "2023-07-15T11:10:45Z",
    "download_url": "https://api.chronoweave.ai/v1/compliance/reports/report_vwx234",
    "expiration": "2023-07-22T11:10:45Z"
  },
  "meta": {
    "request_id": "req_123456",
    "processing_time_ms": 42
  }
}
```

## Next Steps

Explore the other API sections:

- [Core API](core.md): Store and retrieve temporal information
- [TIF API](tif.md): Work with the Temporal Integrity Framework
