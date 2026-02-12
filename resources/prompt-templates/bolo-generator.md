# BOLO Generator

## Scenario

An analyst needs to draft a Be On the Lookout (BOLO) bulletin from available suspect descriptions, vehicle information, incident details, and operational context. Common use cases include drafting initial BOLOs for active incidents, updating existing BOLOs with new information, and preparing bulletins for distribution to patrol units and neighboring agencies.

This template is critical for accuracy. A BOLO with fabricated or assumed physical descriptions can misdirect resources and create liability. The prompt is specifically designed to prevent the model from "filling in" missing description fields with plausible-sounding but fabricated details.

## Prerequisites

- **Suspect description(s)** from witness statements, surveillance imagery, or officer reports. Include all available physical descriptors -- the prompt will handle missing fields by marking them as unavailable rather than guessing.
- **Vehicle information** if applicable (make, model, color, plate, damage, modifications).
- **Incident summary** with relevant details including date, time, location, and offenses.
- **Distribution list and urgency level** determined by a supervisor.
- **CJI considerations** addressed. See the [CJIS Compliance](../../docs/09-cjis-compliance.md) documentation for guidance.

## The Prompt

```
You are a law enforcement intelligence analyst drafting a BOLO
(Be On the Lookout) bulletin for distribution to patrol units
and neighboring agencies.

SOURCE INFORMATION:
---
[PASTE SUSPECT DESCRIPTION, VEHICLE INFO, AND INCIDENT DETAILS]
---

Produce a BOLO bulletin with the following structure:

1. HEADER
   - BOLO number: [ASSIGN OR LEAVE BLANK]
   - Date/time issued
   - Issuing unit
   - Priority level: [ROUTINE / PRIORITY / URGENT]
   - Expiration: [DATE]

2. SUBJECT DESCRIPTION
   - Physical description (sex, race, age range, height, weight,
     hair, eyes, distinguishing features)
   - Clothing description at time of incident
   - Known aliases or identifiers
   - NOTE: Clearly indicate which descriptors are confirmed vs.
     approximate

3. VEHICLE DESCRIPTION (if applicable)
   - Year, make, model, color
   - License plate (full or partial)
   - Distinguishing features (damage, stickers, modifications)
   - Direction of travel and last known location

4. INCIDENT SUMMARY
   - Brief description of the incident (2-3 sentences)
   - Date, time, and location of occurrence
   - Charges or offenses involved

5. OFFICER SAFETY INFORMATION
   - Known weapons involvement
   - History of violence or resistance
   - Mental health indicators
   - Other safety considerations

6. ACTION REQUESTED
   - What officers should do upon contact
   - Contact information for the investigating unit
   - Case number for reference

CONSTRAINTS:
- Use only confirmed information from the provided source material
- Clearly mark any approximate or unconfirmed details with
  "APPROXIMATE" or "UNCONFIRMED"
- Do not add physical descriptions or details not in the source
- If a standard BOLO field is not covered by the provided
  information, mark it as "NOT AVAILABLE" rather than guessing
- Prioritize officer safety information
- Keep language clear, concise, and actionable
- Total length should not exceed one page
```

## Expected Output

A one-page BOLO bulletin formatted for immediate distribution. The bulletin should:

- Contain **only** information from the provided source material
- Mark approximate or unconfirmed details with clear labels
- Use "NOT AVAILABLE" for any standard fields not covered by the source data
- Prioritize officer safety information prominently
- Be clear, concise, and actionable for field personnel

This template is particularly important to review carefully. Physical descriptions are the highest-risk area for hallucination -- the model may attempt to "complete" a partial description with plausible details. Every descriptor must be verified against the source material.

## Review Checklist

- [ ] **Every physical descriptor matches the source material exactly** -- this is the highest priority check
- [ ] No descriptors were fabricated or assumed by the model
- [ ] Approximate details are clearly marked as "APPROXIMATE"
- [ ] Missing fields show "NOT AVAILABLE" rather than fabricated content
- [ ] Vehicle information is accurate (plate number, make, model, color verified character by character)
- [ ] Officer safety information is complete, accurate, and prominently placed
- [ ] Contact information and case numbers are correct
- [ ] Bulletin does not exceed one page
- [ ] Priority level is appropriate for the incident and approved by a supervisor
- [ ] Expiration date is set appropriately
- [ ] Direction of travel and last known location are accurate
- [ ] Bulletin has been reviewed and approved before distribution
- [ ] No CJI is present if the system is not hardened for CJI workloads

---

*This template is part of the [NFCA Open Source LLM](../../README.md) companion resource. For prompting guidance, see the [Prompting Guide](../../docs/06-prompting-guide.md). For model selection, see the [Model Selection Guide](../../docs/05-model-selection.md).*
