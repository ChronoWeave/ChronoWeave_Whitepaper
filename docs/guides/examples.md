# ChronoWeave Examples

This guide provides practical examples of how to use ChronoWeave in various scenarios.

## Healthcare Use Cases

### Patient History Management

This example shows how to use ChronoWeave to manage patient history in a healthcare application.

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your-api-key")

# Store patient information
client.observe({
    "node_type": "PatientInfo",
    "content": {
        "name": "ENC:AES256:abc123def456...",  # Encrypted PHI
        "dob": "ENC:AES256:ghi789jkl012...",   # Encrypted PHI
        "mrn": "ENC:AES256:mno345pqr678..."    # Encrypted PHI
    }
}, user_id="patient_123")

# Store medical history
client.observe({
    "node_type": "MedicalHistory",
    "content": {
        "condition": "ENC:AES256:stu901vwx234...",  # Encrypted PHI
        "diagnosis_date": "2023-01-15T09:30:00Z",
        "diagnosing_provider": "ENC:AES256:yz0123abc456..."  # Encrypted PHI
    }
}, user_id="patient_123")

# Store medication information
client.observe({
    "node_type": "Medication",
    "content": {
        "name": "ENC:AES256:def789ghi012...",  # Encrypted PHI
        "dosage": "ENC:AES256:jkl345mno678...",  # Encrypted PHI
        "prescribed_date": "2023-01-15T10:15:00Z",
        "prescribing_provider": "ENC:AES256:pqr901stu234..."  # Encrypted PHI
    }
}, user_id="patient_123")

# Retrieve patient history for a provider
# First, log the access for HIPAA compliance
client.compliance.hipaa.log_access({
    "user_id": "provider_456",
    "phi_types": ["NAME", "DOB", "MRN", "CONDITION", "MEDICATION"],
    "purpose": "treatment",
    "patient_id": "patient_123",
    "action": "view"
})

# Then retrieve the relevant information
patient_history = client.recall({
    "text": "What is the patient's medical history and current medications?",
    "reference_time": "now",
    "filters": {
        "node_types": ["PatientInfo", "MedicalHistory", "Medication"]
    }
}, user_id="patient_123")

# Process and decrypt the results
for item in patient_history.items:
    # Decrypt PHI fields
    if item.node_type == "PatientInfo":
        item.content["name"] = client.compliance.hipaa.decrypt_phi(item.content["name"])
        item.content["dob"] = client.compliance.hipaa.decrypt_phi(item.content["dob"])
        item.content["mrn"] = client.compliance.hipaa.decrypt_phi(item.content["mrn"])
    elif item.node_type == "MedicalHistory":
        item.content["condition"] = client.compliance.hipaa.decrypt_phi(item.content["condition"])
        item.content["diagnosing_provider"] = client.compliance.hipaa.decrypt_phi(item.content["diagnosing_provider"])
    elif item.node_type == "Medication":
        item.content["name"] = client.compliance.hipaa.decrypt_phi(item.content["name"])
        item.content["dosage"] = client.compliance.hipaa.decrypt_phi(item.content["dosage"])
        item.content["prescribing_provider"] = client.compliance.hipaa.decrypt_phi(item.content["prescribing_provider"])
```

### Clinical Decision Support

This example shows how to use ChronoWeave to provide context for clinical decision support.

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your-api-key")

# Log access for HIPAA compliance
client.compliance.hipaa.log_access({
    "user_id": "provider_456",
    "phi_types": ["CONDITION", "MEDICATION", "LAB_RESULT"],
    "purpose": "treatment",
    "patient_id": "patient_123",
    "action": "view"
})

# Infuse context into a prompt for clinical decision support
infused_prompt = client.infuse_prompt(
    "What should I consider when treating this patient's hypertension?",
    user_id="patient_123",
    filters={
        "node_types": ["MedicalHistory", "Medication", "LabResult"],
        "reference_time": "now"
    }
)

# Send the infused prompt to an LLM for clinical decision support
# (This is a simplified example - in a real application, you would use a medical-specific LLM)
response = llm_client.generate(infused_prompt)

# Log the recommendation
client.observe({
    "node_type": "ClinicalRecommendation",
    "content": {
        "recommendation": "ENC:AES256:vwx567yz0123...",  # Encrypted PHI
        "reasoning": "ENC:AES256:abc890def234...",  # Encrypted PHI
        "provider_id": "provider_456",
        "recommendation_time": "2023-07-15T14:30:00Z"
    }
}, user_id="patient_123")
```

## Financial Services Use Cases

### Wealth Management

This example shows how to use ChronoWeave for wealth management applications.

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your-api-key")

# Store client financial information
client.observe({
    "node_type": "FinancialProfile",
    "content": {
        "income": 120000,
        "net_worth": 750000,
        "risk_tolerance": "moderate",
        "investment_horizon": "long_term",
        "financial_goals": ["retirement", "college_fund", "home_purchase"]
    }
}, user_id="investor_789")

# Store investment transactions
client.observe({
    "node_type": "InvestmentTransaction",
    "content": {
        "transaction_type": "buy",
        "security": "VTSAX",
        "amount": 10000,
        "price": 112.75,
        "date": "2023-06-01T10:15:00Z"
    }
}, user_id="investor_789")

# Apply FINRA retention policy
client.compliance.finra.apply_retention_policy({
    "user_id": "investor_789",
    "data_category": "financial_transactions",
    "retention_period": "7y",
    "action": "archive",
    "jurisdiction": "US-FINRA"
})

# Retrieve investment history
investment_history = client.recall({
    "text": "What are the client's investment transactions?",
    "reference_time": "now",
    "filters": {
        "node_types": ["InvestmentTransaction"],
        "start_time": "2023-01-01T00:00:00Z"
    }
}, user_id="investor_789")

# Generate investment recommendations
infused_prompt = client.infuse_prompt(
    "Based on the client's financial profile and investment history, what investment recommendations would you make?",
    user_id="investor_789",
    filters={
        "node_types": ["FinancialProfile", "InvestmentTransaction"],
        "reference_time": "now"
    }
)

# Send the infused prompt to an LLM for investment recommendations
recommendations = llm_client.generate(infused_prompt)

# Store the recommendations
client.observe({
    "node_type": "InvestmentRecommendation",
    "content": {
        "recommendations": recommendations,
        "advisor_id": "advisor_012",
        "recommendation_time": "2023-07-15T15:30:00Z"
    }
}, user_id="investor_789")
```

### Fraud Detection

This example shows how to use ChronoWeave for fraud detection.

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your-api-key")

# Store transaction data
client.observe({
    "node_type": "Transaction",
    "content": {
        "transaction_id": "tx_345678",
        "amount": 1500.00,
        "merchant": "Electronics Store",
        "location": "New York, NY",
        "timestamp": "2023-07-15T16:45:00Z",
        "card_present": False
    }
}, user_id="customer_345")

# Retrieve transaction history
transaction_history = client.recall({
    "text": "What are the customer's recent transactions?",
    "reference_time": "2023-07-15T16:45:00Z",
    "filters": {
        "node_types": ["Transaction"],
        "start_time": "2023-07-01T00:00:00Z"
    }
}, user_id="customer_345")

# Check for suspicious patterns
suspicious = False
for transaction in transaction_history.items:
    # Example fraud detection logic
    if transaction.content["amount"] > 1000 and not transaction.content["card_present"]:
        # Check if there's a similar transaction in a different location within 24 hours
        for other_tx in transaction_history.items:
            if (other_tx.content["transaction_id"] != transaction.content["transaction_id"] and
                other_tx.content["location"] != transaction.content["location"] and
                abs(parse_time(other_tx.content["timestamp"]) - parse_time(transaction.content["timestamp"])) < 86400):
                suspicious = True
                break

# If suspicious, store a fraud alert
if suspicious:
    client.observe({
        "node_type": "FraudAlert",
        "content": {
            "alert_type": "suspicious_pattern",
            "transaction_id": "tx_345678",
            "risk_score": 0.85,
            "reason": "High-value card-not-present transaction in different location within 24 hours",
            "timestamp": "2023-07-15T16:46:00Z"
        }
    }, user_id="customer_345")
    
    # Apply FINRA compliance rules for fraud alerts
    client.compliance.finra.apply_retention_policy({
        "user_id": "customer_345",
        "data_category": "fraud_alerts",
        "retention_period": "5y",
        "action": "preserve",
        "jurisdiction": "US-FINRA"
    })
```

## Legal and Professional Services Use Cases

### Document Analysis

This example shows how to use ChronoWeave for legal document analysis.

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your-api-key")

# Store document information
client.observe({
    "node_type": "LegalDocument",
    "content": {
        "document_id": "doc_567890",
        "title": "Employment Agreement",
        "parties": ["ABC Corp", "John Smith"],
        "date": "2023-01-15T00:00:00Z",
        "document_type": "contract",
        "status": "active"
    }
}, user_id="case_456")

# Store document clauses
client.observe({
    "node_type": "DocumentClause",
    "content": {
        "document_id": "doc_567890",
        "clause_id": "clause_123",
        "title": "Non-Compete",
        "text": "Employee shall not engage in any competitive business...",
        "section": "8.2",
        "tags": ["restriction", "employment", "competition"]
    }
}, user_id="case_456")

# Store document analysis
client.observe({
    "node_type": "DocumentAnalysis",
    "content": {
        "document_id": "doc_567890",
        "analyst_id": "lawyer_789",
        "analysis": "The non-compete clause appears to be overly broad...",
        "timestamp": "2023-07-10T14:30:00Z"
    }
}, user_id="case_456")

# Retrieve document information and analysis
document_info = client.recall({
    "text": "What are the key details and analysis of the employment agreement?",
    "reference_time": "now",
    "filters": {
        "node_types": ["LegalDocument", "DocumentClause", "DocumentAnalysis"]
    }
}, user_id="case_456")

# Infuse context into a prompt for legal analysis
infused_prompt = client.infuse_prompt(
    "What are the potential enforceability issues with the non-compete clause in this employment agreement?",
    user_id="case_456",
    filters={
        "node_types": ["LegalDocument", "DocumentClause", "DocumentAnalysis"],
        "reference_time": "now"
    }
)

# Send the infused prompt to an LLM for legal analysis
legal_analysis = llm_client.generate(infused_prompt)

# Store the new analysis
client.observe({
    "node_type": "DocumentAnalysis",
    "content": {
        "document_id": "doc_567890",
        "analyst_id": "lawyer_789",
        "analysis": legal_analysis,
        "timestamp": "2023-07-15T17:30:00Z"
    }
}, user_id="case_456")
```

### Case Management

This example shows how to use ChronoWeave for legal case management.

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your-api-key")

# Store case information
client.observe({
    "node_type": "Case",
    "content": {
        "case_id": "case_123456",
        "title": "Smith v. Jones",
        "case_type": "civil",
        "practice_area": "employment",
        "filing_date": "2023-02-15T00:00:00Z",
        "status": "active"
    }
}, user_id="firm_789")

# Store case events
client.observe({
    "node_type": "CaseEvent",
    "content": {
        "case_id": "case_123456",
        "event_type": "filing",
        "title": "Complaint Filed",
        "date": "2023-02-15T10:30:00Z",
        "description": "Initial complaint filed with the court"
    }
}, user_id="firm_789")

client.observe({
    "node_type": "CaseEvent",
    "content": {
        "case_id": "case_123456",
        "event_type": "hearing",
        "title": "Motion Hearing",
        "date": "2023-05-10T09:00:00Z",
        "description": "Hearing on defendant's motion to dismiss"
    }
}, user_id="firm_789")

# Store case notes
client.observe({
    "node_type": "CaseNote",
    "content": {
        "case_id": "case_123456",
        "author_id": "lawyer_012",
        "note": "Client provided additional evidence supporting...",
        "timestamp": "2023-06-20T14:15:00Z",
        "tags": ["evidence", "client_communication"]
    }
}, user_id="firm_789")

# Retrieve case timeline
case_timeline = client.recall({
    "text": "What is the timeline of events for the Smith v. Jones case?",
    "reference_time": "now",
    "filters": {
        "node_types": ["CaseEvent"],
        "sort": "date"
    }
}, user_id="firm_789")

# Infuse context into a prompt for case strategy
infused_prompt = client.infuse_prompt(
    "Based on the case history and notes, what strategy should we consider for the upcoming deposition?",
    user_id="firm_789",
    filters={
        "node_types": ["Case", "CaseEvent", "CaseNote"],
        "reference_time": "now"
    }
)

# Send the infused prompt to an LLM for strategy recommendations
strategy_recommendations = llm_client.generate(infused_prompt)

# Store the strategy recommendations
client.observe({
    "node_type": "CaseStrategy",
    "content": {
        "case_id": "case_123456",
        "author_id": "lawyer_012",
        "strategy": strategy_recommendations,
        "timestamp": "2023-07-15T18:15:00Z",
        "tags": ["deposition", "strategy"]
    }
}, user_id="firm_789")
```

## Enterprise Knowledge Management Use Cases

### Institutional Memory

This example shows how to use ChronoWeave for institutional memory.

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your-api-key")

# Store organizational knowledge
client.observe({
    "node_type": "OrganizationalPolicy",
    "content": {
        "policy_id": "policy_123",
        "title": "Remote Work Policy",
        "description": "Guidelines for employees working remotely",
        "effective_date": "2023-01-01T00:00:00Z",
        "status": "active",
        "department": "Human Resources"
    }
}, user_id="company_456")

# Store policy versions
client.observe({
    "node_type": "PolicyVersion",
    "content": {
        "policy_id": "policy_123",
        "version": "1.0",
        "content": "1. Employees may work remotely up to 2 days per week...",
        "effective_date": "2023-01-01T00:00:00Z",
        "approved_by": "executive_team"
    }
}, user_id="company_456")

client.observe({
    "node_type": "PolicyVersion",
    "content": {
        "policy_id": "policy_123",
        "version": "1.1",
        "content": "1. Employees may work remotely up to 3 days per week...",
        "effective_date": "2023-04-01T00:00:00Z",
        "approved_by": "executive_team"
    }
}, user_id="company_456")

# Store decision records
client.observe({
    "node_type": "DecisionRecord",
    "content": {
        "decision_id": "decision_789",
        "title": "Expansion of Remote Work Policy",
        "description": "Decision to increase remote work allowance from 2 to 3 days",
        "date": "2023-03-15T14:30:00Z",
        "decision_makers": ["executive_team"],
        "rationale": "Employee feedback and productivity metrics support increased flexibility",
        "related_policies": ["policy_123"]
    }
}, user_id="company_456")

# Retrieve policy history
policy_history = client.recall({
    "text": "What is the history of our remote work policy?",
    "reference_time": "now",
    "filters": {
        "node_types": ["OrganizationalPolicy", "PolicyVersion", "DecisionRecord"]
    }
}, user_id="company_456")

# Get policy at a specific point in time
policy_at_point = client.recall({
    "text": "What was our remote work policy in February 2023?",
    "reference_time": "2023-02-15T00:00:00Z",
    "filters": {
        "node_types": ["PolicyVersion"]
    }
}, user_id="company_456")

# Infuse context into a prompt for policy interpretation
infused_prompt = client.infuse_prompt(
    "How has our remote work policy evolved over time, and what were the key factors driving those changes?",
    user_id="company_456",
    filters={
        "node_types": ["OrganizationalPolicy", "PolicyVersion", "DecisionRecord"],
        "reference_time": "now"
    }
)

# Send the infused prompt to an LLM for analysis
policy_analysis = llm_client.generate(infused_prompt)
```

### Knowledge Discovery

This example shows how to use ChronoWeave for knowledge discovery.

```python
from chronoweave.client import ChronoWeaveClient

# Initialize client
client = ChronoWeaveClient(api_key="your-api-key")

# Store research findings
client.observe({
    "node_type": "ResearchFinding",
    "content": {
        "finding_id": "finding_123",
        "title": "Impact of Feature X on User Engagement",
        "summary": "Our research shows that Feature X increases user engagement by 25%",
        "researcher_id": "researcher_456",
        "date": "2023-02-10T00:00:00Z",
        "confidence": 0.85,
        "tags": ["user_engagement", "feature_x", "product_research"]
    }
}, user_id="research_team")

# Store experiment results
client.observe({
    "node_type": "Experiment",
    "content": {
        "experiment_id": "exp_789",
        "title": "Feature X A/B Test",
        "hypothesis": "Feature X will increase user engagement",
        "methodology": "A/B test with 10,000 users",
        "start_date": "2023-01-15T00:00:00Z",
        "end_date": "2023-02-05T00:00:00Z",
        "results": "25% increase in engagement metrics",
        "researcher_id": "researcher_456",
        "related_findings": ["finding_123"]
    }
}, user_id="research_team")

# Store product decisions
client.observe({
    "node_type": "ProductDecision",
    "content": {
        "decision_id": "decision_012",
        "title": "Roll out Feature X to all users",
        "description": "Based on positive experiment results, Feature X will be rolled out to all users",
        "date": "2023-02-20T15:30:00Z",
        "decision_makers": ["product_team"],
        "rationale": "Experiment showed 25% increase in engagement",
        "related_experiments": ["exp_789"],
        "related_findings": ["finding_123"]
    }
}, user_id="research_team")

# Discover knowledge about Feature X
feature_knowledge = client.recall({
    "text": "What do we know about Feature X and its impact on user engagement?",
    "reference_time": "now",
    "filters": {
        "node_types": ["ResearchFinding", "Experiment", "ProductDecision"]
    }
}, user_id="research_team")

# Trace the evolution of knowledge about Feature X
knowledge_evolution = client.tif.get_temporal_lineage({
    "node_ids": ["finding_123"],
    "include_related": True,
    "start_time": "2023-01-01T00:00:00Z",
    "end_time": "2023-03-01T00:00:00Z"
})

# Infuse context into a prompt for knowledge synthesis
infused_prompt = client.infuse_prompt(
    "Synthesize our knowledge about Feature X, including research findings, experiment results, and product decisions.",
    user_id="research_team",
    filters={
        "node_types": ["ResearchFinding", "Experiment", "ProductDecision"],
        "reference_time": "now"
    }
)

# Send the infused prompt to an LLM for knowledge synthesis
knowledge_synthesis = llm_client.generate(infused_prompt)
```

## Next Steps

These examples demonstrate some of the ways you can use ChronoWeave in different industries. For more detailed examples and industry-specific guides, see:

- [Healthcare Integration Guide](healthcare_integration.md)
- [Financial Services Integration Guide](financial_services_integration.md)
- [Legal Services Integration Guide](legal_services_integration.md)
- [Enterprise Knowledge Management Guide](enterprise_knowledge_management.md)
